📊 August 19th, 2025 
📝 authored by Sebestyén Gergely 

It isn’t immediately obvious why one should invest time in the *perfect* repository setup. 
Especially in data, it is often cumbersome to take your maths genius analyst colleagues, sit down 
down and explain about virtual environments, the kind of poetry only software engineers know, why they should give a damn about 
linting, let alone style formatting and type hints.

Luckily, Rust exists, and people exist who build formidable tools with it for Python.
"Rustifying" your repository now means all of the above should take two clicks, and even non-technical people will enjoy
seeing the speed and simplicity of their solution.

PART I. The `uv` radiation is strong

AstralDocs self-introduction:
Astral's mission is to make the Python ecosystem more productive by building high-performance developer tools.

`uv` [self-identifies](https://docs.astral.sh/uv/) as a *fast Python package and project manager*. The simplicity of this
intro already gives us an impression of what it feels like using this tool. They also attach a neat figure to tell us the speed we should expect:

<div style="text-align: center; margin: 20px 0;">
  <img src="assets/uv_chart.png" alt="Alt text" style="max-width: 100%; height: auto;">
</div>

This gives us the first property where `uv` beats `poetry` >> **speed**

In my career as Big Data Engineer, I have met many developers who had been aware for many months of this speed improvement,
yet never took the time to test the tool for themselves, let alone introduce it in production workflows.

Their oversight here is made worse by the second massive feat of `uv` >> **automatic version resolution**

Unlike with poetry, we will be able to define a generic set of Python packages, and leave it to our package manager to generate our
requirements.txt file, insert the correct versions, reconcile everything, then download all of that within the fraction of a minute.

Let's see, how we make use of the Rust revolution.

First, install uv
```
# Install with Homebrew (for Mac)
brew install uv
# ... or simply use the official installation guide
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Now, set up the virtual environment. This will be similar to any virtual environment added to our Python project,
only an order of magnitude faster.
`uv venv --python <version>`

III.
At this point, assume we're working from scratch. We know the packages needed for our project but don't care about their exact version.
We will leave uv to figure out dependencies and install the latest available versions.

Create a `requirements.in` file. Let's go with a few commonly used modules, that are usually computationally expensive for Poetry
to install:
```
pyspark
pandas
numpy
pydantic
sqlalchemy
pyarrow
apache-airflow
boto3
requests
ruff
```

Here comes the trick: we let `uv` define the most recent compatible versions of our desired packages:

Generate the standard requirements.txt file
```commandline
uv pip compile requirements.in -o requirements.txt
``` 

Install the required packages from the dependencies `uv` generated for us:
```commandline
uv pip install -r requirements.txt
```

You will notice this process has taken well below a minute, despite the data intensive nature of our packages.
We now have a functional working environment and all the necessary tools to start creating things 🛠️

It's worth noting that uv treats the .txt *as our lockfile itself*. There is no need for a separate lockfile like Poetry does.

Assume now we've progress in advance with our work. Our repository now has some living and breathing code.
We will soon notice that it's easier to maintain our code with some common functional and style requirements.

There is an ample and relatively standard set of tools (pylint, mypy, flake8, black, pystrict ...) widely employed by developers
for this purpose. At present, let's limit our attention to another product of Astral, the inventors of `uv`: `ruff`

Step IV. Install ruff
```
brew install ruff
# Or go with your preferred installation method
```

Now, the following gives us light-speed iterations through even the largest repositories, to check blatant coding errors:
```
ruff check .
# To exclude single lines from the checks, use # noqa
# To fix automatically, use:
ruff check . --fix
```

... and we can go on and format according to PEP 8 standards and a bit of leeway according to personal preference.
```
ruff format .
# To exclude code sections from the checks, start the block with # fmt: off and close it with # fmt: on
# For single lines, use # fmt: skip
```

To more precisely manage the behaviour of our linter, create a ruff.toml file in the python root repository. Here we control the behaviour for both formatting and linting.
```
line-length = 120

[lint]
ignore = [
  # Set specific flake error codes to ignore here
  # Such as this one: https://www.flake8rules.com/rules/F403.html
]
```

Now, assume we even want to install our linter in a CI/CD workflow that we manage via CircleCI, GitHub Actions or our tool of preference. 
As of today, we can simply rely on the official Astral tools, and add them to our `.yml` file.

Over and done with. Projects rustified.