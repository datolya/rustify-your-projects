📊 August 19th, 2025 
📝 authored by Sebestyén Gergely 

It’s not immediately obvious why one should spend time on the *perfect* repository setup
Especially in Data, it is often cumbersome to sit down with your maths genius analyst colleagues and start explaining virtual 
environments, the kind of Poetry only software engineers appreciate, why they should give a damn about linting, 
let alone style formatting and type hints.

Luckily, Rust exists, and people exist who build formidable tools with it for Python.
"Rustifying" your repository means making use of those tools to cover all of the above with barely more than few clicks.
This way, even non-technical people will enjoy seeing the speed and simplicity of the tech setup journey.

PART I. The `uv` radiation is strong

`uv` [self-identifies](https://docs.astral.sh/uv/) as a *fast Python package and project manager*. The simplicity of this
little tagline foreshadows what it feels like working with the tool. They also attach a neat figure to prepare us for the speed that awaits down the line:

<div style="text-align: center; margin: 20px 0;">
  <img src="assets/uv_chart.png" alt="Alt text" style="max-width: 100%; height: auto;">
</div>

Which brings us to the first reason uv beats Poetry >> **speed**

As a Big Data Engineer, in my career I've come across a myriad of developers who knew for ages about this speed improvement.
Yet, many of them never took the opportunity to test the tool for themselves, let alone roll it out into production workflows.

Their oversight gets worse when you look at uv’s second superpower >> **automatic version resolution**. Unlike with Poetry, 
we won’t need to pin down every single package version for ourselves. Instead, it's possible to define a generic set of
Python packages, and leave it to the package manager to reconcile their latest compatible versions.

Let's see in detail, how to ride the wave of the Rust revolution.

Step zero, install the tool.
Either install with Homebrew (for Mac):
```
brew install uv
```
... or simply use the official installation guide:
```
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Now, we're ready to deploy `uv` to set up our virtual environment.
This will be similar to any `venv` we've already been adding to our Python projects, only an order of magnitude faster
and with bullet-proof computational power.

To generate the venv, simply call:
```
uv venv --python <version>
```

Now let's assume we're starting fresh.

assume we're working from scratch. We know the packages our project needs but don't care about their exact version.
It shouldn't take significant research time to understand how different packages currently work together - let `uv` 
figure out dependencies and install the latest compatible versions!

To do that, we produce a `requirements.in` file. 
Let's go with a few notorious heavy hitters that normally take ages and drain a lot of computation in Poetry:
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

Here comes the trick: `uv` defines the most recent compatible versions of our desired shopping list, and
generates the `.txt` file on its own. It's worth noting that uv treats the .txt *as our lockfile itself*. 
There's no need for a separate lockfile like in the structure we're used to with Poetry.

Generate the standard .txt:
```commandline
uv pip compile requirements.in -o requirements.txt
``` 

Now, we're ready to install required packages but rooting through Rust as our system-level resolver:
```commandline
uv pip install -r requirements.txt
```

You will notice this process wraps us in well below a minute. This is despite us having used some of the most feared
data-intensive packages. At this point, we now have a functional working environment. We're ready to start creating things 🛠️

Assume now we've progressed in our work. Some living and breathing code has now been added to the repository. Soon, it will be easier to maintain code with some common functional and style requirements. There is an ample and relatively 
standard set of tools (pylint, mypy, flake8, black, pystrict ...) developed around this purpose. At present, let's limit 
our attention to another product of Astral, the inventors of `uv`: `ruff`

Let's fetch it first then see what it's capable of:
```
brew install ruff
```
... or go with your preferred installation method. Now, these three short words give us a light-speed iteration through 
even the largest of repositories. This gives us an important life-line to weed out blatant coding errors:
```
ruff check .
```
To exclude single lines from the checks, use: `# noqa` <br>
To fix detected errors automatically, use: `ruff check . --fix`

Next, we might as well go on and format the codebase according to PEP 8 standards - with a bit of leeway for personal preference.
```
ruff format .
```

To exclude code sections from the checks,<br>
start the block with `# fmt: off` <br>
... and close it with `# fmt: on` <br>
... for single line exclusions, use `# fmt: skip`

Finally, let's manage `ruff` precisely. By adding a ruff.toml file in the python root repository, we can specifically 
control the behaviour of both our linting and formatting workflows.
```
line-length = 120

[lint]
ignore = [
  # Specific error codes to ignore
]
```

Finally, consider installing `ruff` as the preferred linter in the CI/CD workflow of the codebase, be it CircleCI, 
GitHub Actions or another tool of preference. AstralDocs have already released some promising tools in this respect, 
to integrate with our CI/CD `.yml` file in a few simple lines of code.

And that's it. Over and done with. Project rustified.