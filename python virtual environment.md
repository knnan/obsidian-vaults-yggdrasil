
pyenv and poetry 
ex
1.

```bash
mkdir -p ~/dev/my-project 
cd ~/dev/my-project
pyenv virtualenv mp3111
pyenv local mp3111
poetry init
poetry add "pydantic[email,dotenv]" beartype icontract
poetry add -G test pytest pytest-cov pytest-recording hypothesis
poetry add -G lint blue isort flake8 mypy
poetry install
echo 'PYTHONUNBUFFERED=1\nPYTHONDONTWRITEBYTECODE=1\nDEBUG=1\nLOCAL=1" > .env.local
git init
```

2.
```bash
cd /path/to/project 
pyenv install 3.11.1
pyenv local 3.11.1
python -m venv .venv
. .venv/bin/activate
```
3.
venv works fine. Never used any other solutions.

```bash

python -m venv venv

```

Got some bash alias to do this even faster:

```bash

alias vm ="python -m venv venv"

alias vd="deactivate"

```