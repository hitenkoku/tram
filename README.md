# TRAM

[![codecov](https://codecov.io/gh/center-for-threat-informed-defense/tram/branch/master/graph/badge.svg?token=YISO1NSAMZ)](https://codecov.io/gh/center-for-threat-informed-defense/tram)

Threat Report ATT&CK Mapping (TRAM) is an open-source platform designed to
advance research into automating the mapping of cyber threat intelligence
reports to MITRE ATT&CK®.

TRAM enables researchers to test and refine Machine Learning (ML) models for
identifying ATT&CK techniques in prose-based cyber threat intel reports and
allows threat intel analysts to train ML models and validate ML results.

Through research into automating the mapping of cyber threat intel reports to
ATT&CK, TRAM aims to reduce the cost and increase the effectiveness of
integrating ATT&CK into cyber threat intelligence across the community. Threat
intel providers, threat intel platforms, and analysts should be able to use TRAM
to integrate ATT&CK more easily and consistently into their products.

## Table of contents

- [TRAM](#tram)
  - [Table of contents](#table-of-contents)
  - [Installation](#installation)
  - [Installation Troubleshooting](#installation-troubleshooting)
    - [[97438] Failed to execute script docker-compose](#97438-failed-to-execute-script-docker-compose)
  - [Report Troubleshooting](#report-troubleshooting)
    - [How long until my queued report is complete?](#how-long-until-my-queued-report-is-complete)
    - [Why is my report stuck in queued?](#why-is-my-report-stuck-in-queued)
    - [Do I have to manually accept all of the parsed sentences in the report?](#do-i-have-to-manually-accept-all-of-the-parsed-sentences-in-the-report)
  - [Requirements](#requirements)
  - [For Developers](#for-developers)
    - [Makefile Version](#makefile-version)
      - [Useful User Commands](#useful-user-commands)
      - [Useful Dev Commands](#useful-dev-commands)
    - [Developer Setup](#developer-setup)
  - [Machine Learning Development](#machine-learning-development)
    - [Existing ML Models](#existing-ml-models)
    - [Creating Your Own ML Model](#creating-your-own-ml-model)
  - [How do I contribute?](#how-do-i-contribute)
    - [Contribute Training Data](#contribute-training-data)
  - [Notice](#notice)

## Installation

1. Get Docker: <https://docs.docker.com/get-docker/>
2. Get Docker Compose:  <https://docs.docker.com/compose/install/>
3. Ensure Docker is running. On some operating systems (e.g., MacOS), you will
   need to provide Docker with permissions before proceeding.
4. Download docker-compose.yml (view raw, save as)

    ```url
    https://github.com/center-for-threat-informed-defense/tram/blob/master/docker/docker-compose.yml
    ```

5. If desired, edit the settings in `docker-compose.yml`
6. Navigate to the directory where you saved `docker-compose.yml`
7. Run TRAM using docker

    ```shell
    docker-compose -f docker-compose.yml up
    ```

8. Navigate to <http://localhost:8000/> and login using the username and
password specified in docker-compose.yml
![image](https://user-images.githubusercontent.com/2951827/129959436-d36e8d1f-fe74-497e-b549-a74be8d140ca.png)

## Installation Troubleshooting

### [97438] Failed to execute script docker-compose

If you see this stack trace:

```shell
Traceback (most recent call last):
  File "docker-compose", line 3, in <module>
  File "compose/cli/main.py", line 81, in main
  File "compose/cli/main.py", line 200, in perform_command
  File "compose/cli/command.py", line 60, in project_from_options
  File "compose/cli/command.py", line 152, in get_project
  File "compose/cli/docker_client.py", line 41, in get_client
  File "compose/cli/docker_client.py", line 170, in docker_client
  File "docker/api/client.py", line 197, in __init__
  File "docker/api/client.py", line 221, in _retrieve_server_version
docker.errors.DockerException: Error while fetching server API version: ('Connection aborted.', ConnectionRefusedError(61, 'Connection refused'))
[97438] Failed to execute script docker-compose
```

Then most likely Docker is not running and you need to start Docker.

## Report Troubleshooting

### How long until my queued report is complete?

A queued report should only take about a minute to complete.

### Why is my report stuck in queued?

This is likely a problem with the processing pipeline. If the pipeline is not
working when you are running TRAM via docker, then this might be a TRAM-level
bug. If you think this is the case, then please file an issue and we can tell
you how to get logs off the system to troubleshoot.

### Do I have to manually accept all of the parsed sentences in the report?

Yes. The workflow of TRAM is that the AI/ML process will propose mappings, but a
human analyst needs to validate/accept the proposed mappings.

## Requirements

* [python3](https://www.python.org/) (3.7+)
* Google Chrome is our only supported/tested browser

## For Developers

### Makefile Version

#### Useful User Commands

- Run TRAM application
  - `make start-container`
- Stop TRAM application
  - `make stop-container`
- View TRAM logs
  - `make container-logs`

#### Useful Dev Commands

- Build Python virtualenv
  - `make venv`
- Install all development dependencies
  - `make install-dev`
- Activate newly created virtualenv
  - `source .venv/bin/activate`
- Setup pre-commit (required one-time process per local `git clone` repository)
  - `pre-commit install`
- Manually run pre-commit hooks without performing a commit
  - `make pre-commit-run`
- Build container image (By default, container is tagged with timestamp and git hash of codebase)
  - `make build-container`
- Run linting locally
  - `make lint`
- Run unit tests, safety, and bandit locally
  - `make test`

### Developer Setup

The following steps are only required for local development and testing. The
containerized version is recommended for non-developers.

1. Start by cloning this repository.

    ```sh
    git clone git@github.com:center-for-threat-informed-defense/tram.git
    ```

2. Change to the TRAM directory.

    ```sh
    cd tram/
    ```

3. Create a virtual environment and activate the new virtual environment.
   1. Mac and Linux

      ```sh
      python -m venv venv
      source venv/bin/activate
      ```

   2. Windows

      ```bat
      venv\Scripts\activate.bat
      ```

4. Install Python application requirements.

    ```sh
    pip install -r requirements/requirements.txt
    ```

5. Set up the application database.

    ```sh
    python src/tram/manage.py makemigrations tram
    python src/tram/manage.py migrate
    ```

6. Run the Machine learning training.

    ```sh
    python src/tram/manage.py attackdata load
    python src/tram/manage.py pipeline load-training-data
    python src/tram/manage.py pipeline train --model nb
    python src/tram/manage.py pipeline train --model logreg
    #  python src/tram/manage.py pipeline train --model <replace-with-registered-model-name>
    ```

7. Create a superuser (web login)

    ```sh
    python src/tram/manage.py createsuperuser
    ```

8. Run the application server

    ```sh
    python src/tram/manage.py runserver
    ```

9. Open the application in your web browser.
    1. Navigate to <http://localhost:8000> and use the superuser to log in
10. In a separate terminal window, run the ML pipeline

     ```sh
     cd tram/
     source venv/bin/activate
     python src/tram/manage.py pipeline run
     ```

## Machine Learning Development

All source code related to machine learning is located in TRAM
[src/tram/tram/ml](https://github.com/center-for-threat-informed-defense/tram/tree/master/src/tram/tram/ml).

### Existing ML Models

TRAM has three machine learning models that can be used out-of-the-box:

1. LogisticRegressionModel - Uses SKLearn's [Logistic
   Regression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html).
   [[source
   code](https://github.com/center-for-threat-informed-defense/tram/blob/master/src/tram/tram/ml/base.py#L296)]
2. NaiveBayesModel - Uses SKLearn's [Multinomial
   NB](https://scikit-learn.org/stable/modules/generated/sklearn.naive_bayes.MultinomialNB.html#sklearn.naive_bayes.MultinomialNB).
   [[source
   code](https://github.com/center-for-threat-informed-defense/tram/blob/master/src/tram/tram/ml/base.py#L283)]
3. DummyModel - Uses SKLearn's [Dummy
   Classifier](https://scikit-learn.org/stable/modules/generated/sklearn.dummy.DummyClassifier.html#sklearn.dummy.DummyClassifier)
   for testing purposes. [[source
   code](https://github.com/center-for-threat-informed-defense/tram/blob/master/src/tram/tram/ml/base.py#L275)]

All ML models are implemented as an SKLearn Pipeline. Other types of models can be added in the future if there is a need.

### Creating Your Own ML Model

In order to write your own model, take the following steps:

1. Create a subclass of `tram.ml.base.SKLearnModel` that implements the
   `get_model` function. See existing ML Models for examples that can be copied.

    ```python
    class DummyModel(SKLearnModel):
        def get_model(self):
            # Your model goes here
            return Pipeline([
                ("features", CountVectorizer(lowercase=True, stop_words='english', min_df=3)),
                ("clf", DummyClassifier(strategy='uniform'))
            ])
    ```

2. Add your model to the `ModelManager`
   [registry](https://github.com/center-for-threat-informed-defense/tram/blob/a4d874c66efc11559a3faeced4130f153fa12dca/src/tram/tram/ml/base.py#L309)
   1. Note: This method can be improved. Pull requests welcome!

    ```python
    class ModelManager(object):
        model_registry = {
            'dummy': DummyModel,
            'nb': NaiveBayesModel,
            'logreg': LogisticRegressionModel,
            # Your model on the line below
            'your-model': python.path.to.your.model
        }
    ```

3. You can now train your model, and the model will appear in the application
   interface.

   ```sh
   python src/tram/manage.py pipeline train --model your-model
   ```

4. If you are interested in sharing your model with the community, thank you!
   Please [open a Pull
   Request](https://github.com/center-for-threat-informed-defense/tram/pulls)
   with your model, and please include performance statistics in your Pull
   Request description.

## How do I contribute?

We welcome your feedback and contributions to help advance TRAM. Please see the
guidance for contributors if are you interested in [contributing or simply
reporting issues.](/CONTRIBUTING.md)

Please submit
[issues](https://github.com/center-for-threat-informed-defense/tram/issues) for
any technical questions/concerns or contact ctid@mitre-engenuity.org directly
for more general inquiries.

### Contribute Training Data

All training data is formatted as a report export. If you are contributing
training data, please ensure that you have the right to publicly share the
threat report. Do not contribute reports that are proprietary material of
others.

To contribute training data, please:

1. Use TRAM to perform the mapping, and ensure that all mappings are accepted
2. Use the report export feature to export the report as JSON
3. Open a pull request where the training data is added to data/training/contrib

## Notice

Copyright 2021 MITRE Engenuity. Approved for public release. Document number
CT0035.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use
this file except in compliance with the License. You may obtain a copy of the
License at

<http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software distributed
under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
CONDITIONS OF ANY KIND, either express or implied. See the License for the
specific language governing permissions and limitations under the License.

This project makes use of MITRE ATT&CK®

[ATT&CK Terms of Use](https://attack.mitre.org/resources/terms-of-use/)
