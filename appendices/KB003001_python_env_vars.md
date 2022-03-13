---
layout: page
title: KB003001 Python Environment Variables
---

## Summary

Using a more universal, lightweight, means of evaluating scripts with environment variables.

**Important** add .env to your ```.gitignore``` file to prevent accidental committing of
sensitive data to respositories.

## Installing

Create a virtual environment as required, then install:

```bash
pip install python-dotenv
```

Define a local ```.env``` file, for example:

```bash
# Throw away example
SOME_ENVVAR="defined_from_local_.env_file"
```

Import the library and run it, note how in the below, the os.getenv() works
as if the environment variable was imported as normal

```python
import os
from dotenv import load_dotenv

def main():
    load_dotenv()
    print(os.getenv('SOME_ENVVAR'))


if __name__ == '__main__':
    main()
```

## Outcome

Running it:

```bash
(.tester)  nsheridan@shr1mbk1  ~/tmp  python something.py
defined_from_local_.env_file
(.tester)  nsheridan@shr1mbk1  ~/tmp 
```

## Justification

Running scripts in workflows/ pipleines, and taking input from ENVVARs, but want to test locally.

## References

The source documentation can be found [here](https://pypi.org/project/python-dotenv/)