# gitlab-devsecops
This repository contains a basic template implementing Application Security pipeline on GitLab.

## Usage Guide

Add the following lines of code in your existing DevOps pipeline configuration `.gitlab-ci.yml` at project root.

```yml
appsec-pipeline:
  stage: devsecops
  variables:
    DAST_TEST_URL: "http://abc.xyz.com"
  only:
    - merge_requests
  trigger:
    strategy: depend
    forward:
      pipeline_variables: true
      yaml_variables: true
    include:
      - project: infra/appsec-pipeline
        ref: main
        file: /security.yml
```

Make sure to add the `devsecops` stage along side with others

```yml
stages:
  - build_abc
  - deploy_xzy
  - test_123
  ...
  - devsecops
```

You can also enable or disable scans using variables

```yml
  variables:
    DAST_TEST_URL: "http://abc.xyz.com"
    APPSEC_PIPELINE_SAST: "false"
    APPSEC_PIPELINE_DAST: "true"
```

Create new branch on your project, do some work there. After finishing open the merge request it will run the AppSec pipeline. If you are using containers in your project make sure that your build stages push the images to GitLab's container registry for scanning. You can also do that by mofiying this template to include a container build stage tag it like `:devsecops-build`

![Job run on merge](/Docs/Img/screenshot-1.png)

![Pipeline Breakdown](/Docs/Img/screenshot-2.png)

The template for Nuclei scan can be copied at the root of the project directory. By default it is for Owasp Juice Shop project for scanning SQLInjection at login page to perform auth bypass for admin user.

![DAST Setup](/Docs/Img/screenshot-0.png)

![Alt text](/Docs/Img/screenshot-3.png)

Reports can be downloaded from uploaded artifacts.

![Alt text](/Docs/Img/screenshot-4.png)

## Remarks

That's it. This is simple straight forward modular AppSec pipeline template for GitLab which can be expanded into many ways to suit your orgainzation's or project needs.