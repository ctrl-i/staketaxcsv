
# Reference Notes

* Random notes, hopefully helpful on occasion.  Probably not helpful on first look.
  
# Linting

* pep8 code style
* Can check for linter errors before submitting a pull request:
  ```sh
  # (some configuration in setup.cfg)
  pycodestyle
  ```

# Tests

* Examples:
  ```
  cd src
  
  # run all tests
  python -m unittest
  
  # run tests matching ...
  python -m unittest -k "test_redelegate"
  
  # run exact test
  python -m unittest tests.tests.test_osmo.TestOsmo.test_redelegate
  
  # run tests with different granularity
  python -m unittest tests.tests.test_osmo.TestOsmo
  python -m unittest tests.tests.test_osmo
  
  # run tests in file
  python -m unittest tests/tests/test_osmo.py
  ```
  
* Tests located at tests/tests/test*.py
* Note: test suite very limited at the moment.  It should expand over time.


# Docker

Sample of using a docker container

```sh
# build the docker container
docker build --platform linux/amd64 --tag staketaxcsv .

# Run/enter/mount docker container 
docker run --platform linux/amd64 -it --volume $PWD:/staketaxcsv staketaxcsv bash

# See README.md Usage section to run script(s)
# https://github.com/hodgerpodger/staketaxcsv#usage
```

# Run CSV job with no transaction limit

This is a common support request.  I provide some details here for those interested.  Steps:

  1. Follow `Install` section or `Docker` section to install python/packages.  For those familiar with docker, I recommend `Docker` section.
  3. Run CSV script using --limit flag

```
# Load environment variables from sample.env (add to ~/.bash_profile or ~/.bashrc to avoid doing every time)
set -o allexport
source sample.env
set +o allexport

# Run CSV job with custom high transaction limit
cd src
python3 staketaxcsv/report_sol.py <wallet_address> --limit 100000 --format all
```

# Ideal Configuration

Default code was made to work out of the box. These are changes that require manual actions. They improve reliability
(RPC Node settings) or speed (DB Cache) when compared to default version.

## RPC Node settings

* Default `sample.env` points to public RPC nodes.  This generally works, up to a point.
* Edit/uncomment `sample.env` to change to point to more reliable private RPC node(s).
  * Examples for private RPC nodes (Figment, Quicknode) are included.

## DB Cache

Use of a database for caching is ideal to speed up certain RPC queries (especially SOL). Here is the script usage to
enable caching:

```sh
cd src

# --cache flag requires working implementation of Cache class (common/cache.py)
python3 staketaxcsv/report_osmo.py <wallet_address> --cache
```

To enable --cache, you must configure an aws connection for the boto3 code found in src/common/Cache.py:

* One method: `aws configure`
  * See here to install AWS CLI: <https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html>
  * See here to use `aws configure`: <https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html>

Alternatively, you may implement your own Cache class (common/cache.py).

# Installing python 3.9 on macOS

* Personal method--google is probably better

  ```sh
  # Install brew (see https://brew.sh/)
  
  # python 3.9.9
  brew install openssl readline sqlite3 xz zlib
  brew install pyenv
  pyenv install 3.9.9
  
  # use virtualenv
  brew install virtualenv
  virtualenv -p ~/.pyenv/versions/3.9.9/bin/python3.9 env
  source env/bin/activate
  
  # install pip packages (same as README.md)
  pip3 install -r requirements.txt
  ```
