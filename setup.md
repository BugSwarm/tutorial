# BugSwarm Setup and Basic Usage

## Installation

If you've SSH'ed into one of the provided machines, then everything you need has already been installed.
You can skip this section.

1. Install Docker: <https://docs.docker.com/engine/install>

2. Install the BugSwarm toolset and the client with `pip` (requires Python 3.8 or later):
   ```sh
   python3 -m pip install bugswarm-common bugswarm-client
   ```


## Basic API Usage

All API actions can be performed using the `DatabaseAPI` object.
Initialize it like so:

```python
from bugswarm.common.rest_api.database_api import DatabaseAPI
api = DatabaseAPI(token='BugSwarmTutorialToken-1')
```

> [!NOTE]
> The `token` argument on the `DatabaseAPI` constructor is optional -- you can use the API unauthenticated.
> However, unauthenticated API requests are rate-limited.

Get metadata for an artifact:

```python
api.find_artifact('AntonLydike-riscemu-13266995693').json()
```

Get the original build log for a job:

```python
# Pass a string, not an int!
api.get_build_log('13266995693')
```

Filter artifacts by various criteria:

```python
# Use MongoDB query syntax.
# Returns the metadata for each artifact that (1) is active in the dataset,
# (2) was sourced from GitHub Actions, and (3) encountered a
# NullPointerException.
api.filter_artifacts('{"status": "active", "ci_service": "github", "classification.exceptions": "NullPointerException"}')
```


## Basic CLI Usage

You can view an artifact's metadata with `bugswarm show`:

```sh
bugswarm show --image-tag AntonLydike-riscemu-13266995693
```
<details><summary>Show output</summary>

```json
{
    "_created": "2023-07-19T19:42:48Z",
    "_deleted": false,
    "_etag": "d9a08e1e239c7a9f3c92344d3632db8f92970f5e",
    "_id": "64b83cb831600abb42b87a5d",
    "_links": {
        "collection": {
            "href": "artifacts",
            "title": "artifacts"
        },
        "next": {
            "href": "artifacts/AntonLydike-riscemu-13266995693?page=2",
            "title": "next page"
        },
        "parent": {
            "href": "/",
            "title": "home"
        },
        "self": {
            "href": "artifacts/AntonLydike-riscemu-13266995693",
            "title": "Artifact"
        }
    },
    "_updated": "2023-08-14T18:41:44Z",
    "added_version": "1.2.2",
    "base_branch": "",
    "branch": "sasha/typing",
    "build_system": "NA",
    "cached": true,
    "ci_service": "github",
    "classification": {
        "build": "No",
        "code": "Partial",
        "exceptions": [
            "NameError"
        ],
        "test": "No"
    },
    "creation_time": 1689795768,
    "current_image_tag": "AntonLydike-riscemu-13266995693",
    "deprecated_version": null,
    "failed_job": {
        "base_sha": "",
        "build_id": 4862859261,
        "build_job": "9.1",
        "committed_at": "2023-05-02T15:16:34Z",
        "component_versions": {
            "analyzer": "05374c8abe869159c5a060f3bda41fcc7f27a437",
            "reproducer": "05374c8abe869159c5a060f3bda41fcc7f27a437"
        },
        "config": {
            "id-in-workflow": "build",
            "runs-on": "ubuntu-latest",
            "steps": [
                {
                    "uses": "actions/checkout@v3"
                },
                {
                    "name": "Set up Python",
                    "uses": "actions/setup-python@v4",
                    "with": {
                        "python-version": "${{ matrix.python-version }}"
                    }
                },
                {
                    "name": "Upgrade pip",
                    "run": "pip install --upgrade pip\n"
                },
                {
                    "name": "Install the package locally",
                    "run": "pip install -r requirements.txt -r requirements-dev.txt"
                },
                {
                    "name": "Test with pytest",
                    "run": "pytest -W error\n"
                },
                {
                    "name": "Test with lit",
                    "run": "lit -v test/filecheck\n"
                }
            ],
            "strategy": {
                "matrix": {
                    "python-version": "3.10"
                }
            }
        },
        "failed_tests": "(test.test_helpers)#(test.test_integers)#(test.test_isa)#(test.test_tokenizer)",
        "is_git_repo": true,
        "job_id": 13266995693,
        "message": "add some typing annotations",
        "mismatch_attrs": [],
        "num_tests_failed": 4,
        "num_tests_run": 4,
        "patches": {
            "remove-ppa": "2023-07-19"
        },
        "trigger_sha": "6ec6e5130e8fbaca8b151fc7c5308b5a40aad2c8"
    },
    "filtered_reason": null,
    "image_tag": "AntonLydike-riscemu-13266995693",
    "is_error_pass": false,
    "lang": "Python",
    "match": 1,
    "merged_at": null,
    "metrics": {
        "additions": 3,
        "changes": 4,
        "deletions": 1,
        "num_of_changed_files": 3
    },
    "passed_job": {
        "base_sha": "",
        "build_id": 4895189877,
        "build_job": "11.1",
        "committed_at": "2023-05-05T15:48:27Z",
        "component_versions": {
            "analyzer": "05374c8abe869159c5a060f3bda41fcc7f27a437",
            "reproducer": "05374c8abe869159c5a060f3bda41fcc7f27a437"
        },
        "config": {
            "id-in-workflow": "build",
            "runs-on": "ubuntu-latest",
            "steps": [
                {
                    "uses": "actions/checkout@v3"
                },
                {
                    "name": "Set up Python",
                    "uses": "actions/setup-python@v4",
                    "with": {
                        "python-version": "${{ matrix.python-version }}"
                    }
                },
                {
                    "name": "Upgrade pip",
                    "run": "pip install --upgrade pip\n"
                },
                {
                    "name": "Install the package locally",
                    "run": "pip install -r requirements.txt -r requirements-dev.txt"
                },
                {
                    "name": "Test with pytest",
                    "run": "pytest -W error\n"
                },
                {
                    "name": "Test with lit",
                    "run": "lit -v test/filecheck\n"
                }
            ],
            "strategy": {
                "matrix": {
                    "python-version": "3.10"
                }
            }
        },
        "failed_tests": "",
        "is_git_repo": true,
        "job_id": 13267406539,
        "message": "import Optional",
        "mismatch_attrs": [],
        "num_tests_failed": 0,
        "num_tests_run": 16,
        "patches": {
            "remove-ppa": "2023-07-19"
        },
        "trigger_sha": "5f2f4e44af6d638ce9af46f48368fbfa9fc39122"
    },
    "pr_num": -1,
    "repo": "AntonLydike/riscemu",
    "repo_mined_version": "26d7f65cc1660fef10f9e0546e96ad090f297ac5",
    "reproduce_attempts": 3,
    "reproduce_successes": 3,
    "reproduced": true,
    "reproducibility_status": {
        "status": "Reproducible",
        "time_stamp": "2023-07-19"
    },
    "stability": "3/3",
    "status": "active",
    "test_framework": "pytest"
}
```

</details>

You can start a shell in an artifact's Docker container with `bugswarm run`:

```sh
bugswarm run --image-tag AntonLydike-riscemu-13266995693 --use-sandbox
```

- `--image-tag <image-tag>`: Specify an artifact to run
- `--use-sandbox`: Mount `~/bugswarm-sandbox` on the host to `/bugswarm-sandbox` in the artifact container.


### Artifact Structure

```
/usr/local/bin/
 ├─ run_failed.sh            Scripts to run the failed
 └─ run_passed.sh            and passed jobs.

/home/bugswarm/build/
 ├─ <FAILED-ID>.log          The original failed and
 ├─ <PASSED-ID>.log          passed logs.
 │
 ├─ failed/
 │   └─ <REPO-NAME>/
 │       └─ <...>            The contents of the repo at
 └─ passed/                  the time of the failed and
     └─ <REPO-NAME>/         passed jobs.
         └─ <...> 
```

For instance, you can make changes to the failed version of a given repo in `~/build/failed/<repo-name>`, and then test whether those changes fix the problem by running `run_failed.sh`.


## Selecting Artifacts (Advanced API Usage)

Using `DatabaseAPI.filter_artifacts()`, you can query artifacts based on many different criteria.
Queries use [MongoDB query syntax](https://www.mongodb.com/docs/manual/tutorial/query-documents/).
A full specification of BugSwarm's artifact schema can be found [here](https://github.com/BugSwarm/bugswarm/blob/master/docs/Artifact-Structure.md).

> [!NOTE]
> In general, it's best to include `"status": "active"` in your query to ensure you don't get back any deprecated artifacts.


```python
import json
from bugswarm.common.rest_api.database_api import DatabaseAPI
api = DatabaseAPI('BugSwarmTutorialToken')
```


### Selecting Based on Exceptions

Select active artifacts whose failed job encountered a `NullPointerException`:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'classification.exceptions':
    'NullPointerException',
}))
```

Select active artifacts whose failed job encountered both a `NullPointerException` and an `AssertionError`:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'classification.exceptions': { 
    '$all':[
      'NullPointerException',
      'AssertionError',
    ],
  },
}))
```

Select active artifacts whose failed job encountered either a `NullPointerException` or an `AssertionError`:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'classification.exceptions': { 
    '$in':[
      'NullPointerException',
      'AssertionError',
    ],
  },
}))
```


### Selecting Based on Patch Location

You can select artifacts based on whether the commit(s) that fixed the failed build affected code files, build files, and/or test files.

Select active artifacts whose patches **only** affected code files:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'classification.code': 'Yes',
}))
```

Select active artifacts whose patches affected code files and optionally also build or test files :

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'classification.code': {'$in': ['Yes', 'Partial']}
}))
```

Select active artifacts whose patches affected both code and build files, but not test files:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'classification.code': 'Partial',
  'classification.build': 'Partial',
  'classification.test': 'No',
}))
```


### Selecting Based on Diff Size

Select active artifacts whose patches changed between 1 and 20 lines:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'metrics.changes': {'$gte': 1, '$lte': 20}
}))
```

Select active artifacts whose patches only deleted code:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'metrics.additions': 0,
  'metrics.deletions': {'$gt': 0},
}))
```

## Selecting Based on Tests

Select active artifacts whose failed jobs had at least one test run:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'failed_job.num_tests_run': {'$gt': 0},
}))
```

Select active artifacts whose failed jobs had exactly one test fail:

```python
api.filter_artifacts(json.dumps({
  'status': 'active',
  'failed_job.num_tests_failed': 1,
}))
```
