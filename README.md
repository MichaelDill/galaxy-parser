# Galaxy Parser

> Parser tool to read and parse the code metric files that your build already generates and pushes them into Galaxy

*NOTE:* Galaxy does not do any code analysis or anything, it will just parse files of code coverage, eslint, etc that your project already produces.

## Prerequisites

The *bare* minimum to report into galaxy is to have code coverage reporting in one of the supported formats.

#### Supported Code Coverage Formats

* LCOV (JavaScript/? projects)
* JACOCO XML (Java projects)

## Installation

    npm install --save-dev galaxy-parser

## Getting your project reporting into Galaxy

1) Create a `package.json` / Modify the existing `package.json`

*NOTE:* The `package.json` `name` field is used as the key. Keep it all lower-case with no spaces. I.E `my-project` or `myProject`.

2) Add an entry to the `package.json` for `galaxy`
```
    "galaxy": {
        "display": "My Project", // display name for galaxy (defaults to package.json name)
        "type": "javascript", // type of the project that we are reporting on (javascript|java|php)
        "goal": 80, // goal set for the unit test coverage number (defaults to 80 if not provided),
        "threshold": 0.15 // threshold to not report changes to slack (defaults to 0 if not provided),
        "precision": 2, // precision of decimal places to display for coverage differences (defaults to 2)
        "locations": {
          "sloc": "/coverage/sloc.json", // location of where the sloc.json file is for line of code parsing
          "lcov": "/coverage/lcov.info", // location of where the code coverage is saved
          "jacoco": "/coverage/jacoco.xml", // location of where jacoco saves the xml file
          "jacoco-multi": [ // location of multiple small jacoco files to sum up
            "/coverage/jacoco1.xml",
            "/coverage/jacoco2.xml",
            "/coverage/jacoco3.xml"
          ]
        }
    }
```
*NOTE:* Projects will only have *one (1)* code coverage metric, either `lcov` or `jacoco` at this point in time!

3) Add some scripts to your `package.json` to manage run the report

This will be different based on the type of project you are working on, this is an example for a `JavaScript` project:

    "loc": "sloc src/ --format json > ./coverage/sloc.json",
    "pregalaxy": "npm run loc",
    "galaxy": "galaxy analyze"

4) Add the NPM run scripts into your build process

This will vary on what CI tool that you are using, here are some examples for Jenkins and Travis

Jenkins

    // execute shell script (after everything else is done / test coverage ran)
    npm run galaxy -- FIREBASE_URL SLACK_WEB_HOOK SLACK_CHANNEL

Travis

    after_success:
      - test $TRAVIS_BRANCH = "master" && npm run galaxy $FIREBASE_URL $SLACK_WEB_HOOK $SLACK_CHANNEL

5) Add private environment variables for `FIREBASE_URL`, `SLACK_WEB_HOOK` and `SLACK_CHANNEL`

## Example Integrations

* [Novo-Elements (JavaScript)](https://github.com/bullhorn/novo-elements/commit/3de0a2032a7c0f96655f3bd0df2d7ee8dc3c7950)
* [DataLoader (Java)](https://github.com/bullhorn/dataloader/commit/7d8e53bc669d7b64717f05353e9d8a4fd862bb40)

*NOTE:* On those, the `galaxy` config is missing the `display` property. This is defaulted to the `package.json` name otherwise if not set.
