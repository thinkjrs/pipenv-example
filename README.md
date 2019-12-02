# pipenv-example

An overview of working with `pipenv` coming from Musicfox's `conda` + `pip`
development and deployment workflow.

## The backstory

The workflow used at Musicfox prior to `pipenv` can be summarized as follows:

- **development** uses a per-project (repository) level conda environment,
automatically generated per-project specifications given in its application
template setup [`cookiecutter`](https://github.com/cookiecutter/cookiecutter)

- **production** uses a per-project `requirements.txt` generated from running
a `pip freeze` command within the project's active conda environment. 


### Issues encountered

This is reliable for small pieces of software and those without compiled 
components. Much Python does not require sophistication. However,
larger applications, not so much, as they often require sophisticated 
compiled database drivers, which come precompiled as `conda` packages, and 
often require container-level dependencies for `pip install` to build wheels
properly.

It is often the case that a developer's first setup build after generating
an application via template, fails, due to this mis-match in dependencies. What
we're lacking is **deterministic builds** for all stages of development.

## `pipenv` to the rescue

Of course Python's most prolific developer Keneth Reitz contributed yet another
glorious addition, this time for Python's byzantine packaging system.

In particular, it bridges the gap between pip and virtual environments in
addition to implementing new security/deployment features.

### Congruencies in action

Let's do some normal things that we normally need to do, below.
#### Initial project setup

Clone/create your project and move into it:

```bash
cd my-project
```

If there is a `Pipfile`:

```bash
pipenv install # --dev or --deploy, don't forget to update docker commands!
```

Or create a `Pipfile` using the template below.

#### Install a package
```bash
pipenv install <package>
```

#### Activate the shell
```bash
pipenv shell
# to exit
exit
```

#### Updates

To see what needs an update or can be updated:

```bash
pipenv update --outdated
```

To upgrade everything:
```bash
pipenv update
```
#### Convert `requirements.txt` to a `Pipfile`

```bash
pipenv install -r path/to/requirements.txt
```
**Don't forget!** 

Edit the new Pipfile to remove those, and let pipenv keep track of pinning.

Update your packages (same as above):

```bash
pipenv update 
```

#### The `Pipfile`

Here's the basic `Pipfile` from the [docs](https://pipenv.kennethreitz.org/en/latest/basics/#example-pipenv-workflow):

```python

[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"

[packages]
requests = "*"

[dev-packages]
pytest = "*"

[requires]
python_version = "3.6"

```
#### Uninstall

To uninstall a package or all packages:
```bash
pipenv uninstall <package>

pipenv uninstall --all # --all-dev: remove from Pipfile as well
```

#### Locking mechanism

Promoting the same build from development into production:

> You can use `pipenv` lock to compile your dependencies on your development 
> environment and deploy the compiled `Pipfile.lock` to all of your production
> environments for reproducible builds.
\-\- [the docs](https://pipenv.kennethreitz.org/en/latest/basics/#example-pipenv-workflow)

### Gotchas

