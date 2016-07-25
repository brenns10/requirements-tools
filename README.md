check-requirements
========

check-requirements tests for problems with requirements. It's intended to be
run as part of your project's tests.


## What it does

* Checks for requirements listed in `requirements.txt` but not `setup.py`
  (probably indicates unused requirements or used requirements that need to be
  added to `setup.py`)

* Checks for requirements in `setup.py` but not in `requirements.txt`

* Checks for consistency between `requirements.txt` and `package.json` (if one
  exists)

* Checks for dashes instead of underscores in requirement names

* Checks for unpinned requirements or loosely-pinned requirements


## Integrating into your project
### Recommended requirements setup

The recommended layout for your project is:

* `setup.py` contains a list of unpinned (or loosely-pinned) top-level
  requirements needed in production. For example, you might list `requests`,
  but you wouldn't list libraries `requests` depends on.

  If you know of a problematic version, you should *loosely* pin here (e.g.
  `requests>=4` if you know you depend on APIs introduced in version 4).

* `requirements-dev-minimal.txt` is much like `setup.py`, but is intended for
  dev dependencies. You should list loosely-pinned top-level dependencies only.

* `requirements.txt` contains a list of all production dependencies (and
  sub-dependencies) with strict pinning. When deploying your app, you install
  dependencies from this file, not `setup.py.

  The benefits to strict pinning are more deterministic versioning (you can
  roll back more easily) and faster virtualenv generation with
  [pip-faster](https://github.com/Yelp/pip-faster).

  It should be possible to automatically generate `requirements.txt` by
  creating a fresh virtualenv, installing your app's dependencies from
  `setup.py`, and running `pip freeze`.

* `requirements-dev.txt` is just like `requirements.txt` but for dev
  dependencies (and dev sub-dependencies).

  It could be automatically generated by creating a fresh virtualenv,
  installing the requirements listed in `requirements-dev-minimal.txt`, running
  `pip freeze`, and subtracting out common requirements already in
  `requirements.txt`.

* `package.json` (for frontend services only) should list strictly-pinned
  dependencies. For packages which provide both a Python and an npm package
  (e.g. `yelp-styleguide`), those versions should match the versions in
  `requirements.txt`.

All of these files should be checked into your project.


### Adding `check-requirements` to your tests

You should run the executable `check-requirements` in a virtualenv with the
`check-requirements` package installed as part of your tests.

If you're using `tox`, you can just add it to the end of `commands` and add
`check-requirements` to your dev requirements file (probably
`requirements-dev.txt`).
