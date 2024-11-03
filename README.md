# DRIVEAZ - Transmute polars cli/class wrapper
## version: v0.0.10

>Project repo: [https://github.com/ua-data7/driveaz](https://github.com/ua-data7/driveaz)

### Building/installing poetry project

>Helpful guide: [https://diegoquintanav.github.io/poetry-primer.html](https://diegoquintanav.github.io/poetry-primer.html)

#### Follow assumes you are using [pyenv + poetry + pipx] as outlined above

1)  `cd <project>; pyenv virtualenv 3.11.9 driveaz-env`
2)  `pyenv activate driveaz-env` OR `pyenv local driveaz-env`
3)  `pyenv rehash`

Enable poetry's use of virtualenvs (and we'll use pyenv)

4)  `poetry config virtualenvs.create true`
5)  `poetry config virtualenvs.in-project false`

setup poetry environment/development tools

6)  `poetry install --dev`

Build whl/zip packages

7)  `make build`

Test project to generate parquet from csv

8)  `make test`

### Tag build version and auto build whl package/release via github action

1)  Edit the `RELEASE`, `README.md` and `pyproject.toml` files and bump version up

Make sure git repo is

2)  `git commit -am "some statement about commit"; git push`

Create tag on repo and push - trigger build process

3)  `make tag`

### Download generic testing data from data.world

The test dataset Rides_DataA.csv not included with repo.

```
    cd <project>/datasets
    # Page: https://data.world/ride-austin/ride-austin-june-6-april-13/workspace/file?filename=Rides_DataA.csv
    curl -SL https://query.data.world/s/peg4okfj6jhdzdrnqxr6hueekesyzk?dws=00000 -o Rides_DataA.csv
    # OR
    make pull-data
```

### CSV/Parquet YAML Schema for convert.py script
For conversion of csv <-> parquet, define yaml a file:

```
version: <FILE VERSION>
quote_style: <String for defining quote style>
input_file:
  columns:
    - name: <NAME OF COLUMN>
      col_number: <COLUMN NUMBER>
      dtype: <POLARS DATA TYPE>
# -- Optional / Data type specific
      format: <DATETIME STRING FORMATING>
      time_zone: <DATETIME ENCODING TIMEZONE>
      time_unit: <DATETIME TIME UNIT>
      precision: <DECIMAL number of digits to store>
      scale: <DECIMAL number of decimal points stored>
      ...
```

An example for the Ride Austin dataset can be found in `<project>/schemas`


### To use Transmute in a python project

```
    from driveaz.transmute import TArguments, Transmute

    # Setup arguments
    targs = TArguments(
        schema='<filepath to schema.yaml>',
        input='<filepath to input csv>',
        output='<filepath to output parquet>',
        null_values=['NA', 'null'],
        infer_len=100,
        compression="gzip",
        verbose=False,
        force=True,
        apply_map=False,
        skip_recast=False,
        use_col_num=False
    )
    # Using TArguments dataclass
    tm = Transmute(targs)

    # OR

    tm = Transmute.New(
        schema='<filepath to schema.yaml>',
        input='<filepath to input csv>',
        output='<filepath to output parquet>',
        null_values=['NA', 'null'],
        infer_len=100,
        compression="gzip",
        verbose=False,
        force=True,
        apply_map=False,
        skip_recast=False,
        use_col_num=False
    )

    # Convert into parquet
    tm.convert_file()
    # Load parquet file
    df = pl.read_parquet('<filepath to output parquet>')
```