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

Step I.: Install Rust

The easiest way to have all the necessary requirements for `uv` is to install cargo on our machine, the package manager of Rust.
```
-> cargo (Rust package manager) needs to be present on our machine
--> create requirements.in
---> uv pip compile requirements.in -o requirements.txt
```

/ Here uv automatically generates our .txt with comments on dependencies
```
uv pip install -r requirements.txt
```

/ Make sure the .venv is correctly set for pycharm (repository root and correct Python version)
Easy way to create uv venv: 
`uv venv --python 3.12.9`
`source .venv/bin/activate`

Note that uv treates req.txt AS THE LOCKFILE ITSELF. So we commit it with the repo and that's it.

Use another clever Rust tool for linting!!!
```
ruff format .
ruff check --fix
```

Let's use `uv` since it's much faster than poetry! 
We want to use the rust revolution to our advantage.

PART II. CREATE A DATA LAKE WITH GCS

We use a terraform subdir and generate resources programmatically.
Structure our lake by raw, staging, processed etc.
Free trial quota project with Google.

PART III. AIRFLOW CONFIG
As of 25 April 2025, the latest supported Python version by Airflow is < 3.13
So we install last stable version 3.12.9

WHAT'S NEW IN AIRFLOW 3.0.0 (Published 22 April)
-> Event-driven architecture possible (assets, like Dagster)
-> Environment can scale to zero (no need for constant uptime anymore) 

Airflow already has native support for uv [by documentation](https://airflow.apache.org/docs/apache-airflow/stable/start.html)
In fact installing via Poetry is NOT recommended by them!

- We set home dir for install `export AIRFLOW_HOME=~/airflow`. Then copy paste commands from docs.
- Note we're working with Airflow 3.0.0 where operators are now under `airflow.providers.standard.operators.empty`
- Airflow 3.0.0 started copying Dagster in terms of asset driven workflows. So let's try to use it! 
  - Note Cloud Composer won't have support for it for some time (only Airflow 2).
- To run Airflow with Docker Commpose, use [this guide](https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html)
- Run `docker compose up airflow-init` Then `docker compose up`
  - Note this is not optimized for production workflows.
  - As per Google [release notes](https://cloud.google.com/composer/docs/release-notes):
    - we don't know when Airflow 3.0 will be supported, 2.10.5 they released with 2 months lag
    - As per [recent discussion](https://github.com/apache/airflow/issues/49646) we need to set explicit auth jwt secret for workers

SHOW EVENT BASED FLOW IN AN AIRFLOW 3.0.0 SEPARATE ARTICLE.
So probably
#1 infra based: uv, ruff, docker setup for data tools (airflow[uv], postgres, kafka etc.)
#2 Airflow 3.0.0 event based, scales to zero, reparse dag, etc. mention novelties from YT video
https://www.youtube.com/watch?v=PMO5LPc112E&t=493s


# TODO: Add uv github actions step + circleci maybe