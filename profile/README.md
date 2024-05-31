# nullinside-development-group

This organization is primarily responsible for the code required to run https://nullinside.com and its ancillary
applications.

## Architecture

### CICD

#### Pull Request Testing

All pull requests are tested prior to be merged using GitHub actions that run on self-hosted runners. These checks,
where possible, include CodeQL in addition to common sense sanity tests on the proposed code.

```mermaid
flowchart LR
    a1-->|Get Work|API
    a2-->|Get Work|API
    a3-->|Get Work|API
    a4-->|Get Work|API
    subgraph Web Server - Docker
        a1[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/github-runner'>docker-build-agent</a>]
        a2[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/github-runner'>docker-build-agent</a>]
        a3[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/github-runner'>docker-build-agent</a>]
        a4[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/github-runner'>docker-build-agent</a>]
    end
    subgraph GitHub
        API
    end
```

#### Code Building

Code is built on Jenkins using self-hosted runners. The jenkins instance has two publicly exposed endpoints that ingest
JSON data indicating when updates are made to the GitHub codebase. Updates to each codebase's `main` branch
automatically triggers builds and deploys artifacts.

```mermaid
flowchart LR
    API-->|Code Pushed|angular-ui
    subgraph Web Server - Docker
        angular-ui[<a href='https://github.com/nullinside-development-group/nullinside-ui'>angular-ui</a>]-->|NGINX Route: <a href='https://github.com/nullinside-development-group/nullinside-ui/blob/main/nginx/filesystem/etc/nginx/conf.d/nginx.conf'>/github-webhook</a>|Jenkins
        angular-ui[<a href='https://github.com/nullinside-development-group/nullinside-ui'>angular-ui</a>]-->|NGINX Route: <a href='https://github.com/nullinside-development-group/nullinside-ui/blob/main/nginx/filesystem/etc/nginx/conf.d/nginx.conf'>/multibranch-webhook-trigger</a>|Jenkins
        b1["<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>Build
            Agent</a>"]-->|Get Work|Jenkins[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>jenkins</a>]
        b2["<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>Build
            Agent</a>"]-->|Get Work|Jenkins[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>jenkins</a>]
        b3["<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>Build
            Agent</a>"]-->|Get Work|Jenkins[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>jenkins</a>]
        b4["<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>Build
            Agent</a>"]-->|Get Work|Jenkins[<a href='https://github.com/ProgrammingByPermutation/scripts/tree/main/jenkins'>jenkins</a>]
    end
    subgraph GitHub
        API
    end
```