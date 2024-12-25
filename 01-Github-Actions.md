## Executing multiple jobs
- Need to share artifact for the next jobs
- upload buildartifact actions in the marketplace
- Download a Build Artiact in the market place
- Artifact are available in github for downloads- stored in 90days- for free accounts.

```yaml
name: Genrate ASVII Art work
on:
  #push
jobs:
  build_job_1:
    needs: test_job_2
    runs-on: ubuntu-latest
    steps:
    - name: Install Cowsay Program
      run: sudo apt-get install cowsay -y

    - name: Execute Cowsay CMD
      run: cowsay -f dragon "Run for cover, I am a Dragon....RAWR" >> dragon.txt

    - name: sleep for 30 seconds
      run: sleep 30
    - name: Upload Dragon text file
      uses: actions/upload-artifact@v4
      with:
        name: dragon-text-file
        path: dragon.txt

  test_job_2:
      needs: build_job_1
      runs-on: ubuntu-latest
      steps:
      - name: Sleep for 10 seconds
        run: sleep 10
        
      - name: Test File exists
        run: grep -i "dragon" dragon.txt

      - name: Download Dragon text file
      - uses: actions/download-artifact@v4
        with:
          name: dragon-text-file
         
      - name: Display structure of downloaded files
        run: ls -R
##add download to deploy jobs as well 


  ascii_job:
      runs-on: ubuntu-latest
      steps:
      - name: Executing shell script
        run: |
          chmod +x ascii-script.sh
          ./ascii-script.sh 



  deploy_job_3:
      needs: [test_job_2]
      runs-on: ubuntu-latest
      steps:
      - name: Read File
        run: cat dragon.txt



```
## prevent the same actions running simultanouesly

```yml
use concurrency:
on the job or workflow level

name: exploring concurrency
on:
  workflow_dispatch
env:
  CONTAINER_REGISTRY: docker.io
  IMAGE_NAME: github-actions-nginx

  deploy:
   needs: docker
   concurrency: 
     group: production-deploy
     cancel-in-progress: false
   steps:
   - name: Test File exists
     run: grep -i "dragon" dragon.txt
     time-out-minutes: 1
```
## USING Matrix

```yml
name: Matrix Configuration

on:
  push:
  workflow_dispatch:

jobs:
    deploy:
      strategy:
        fail-fast: false ##prevent cancelling all in-progress jobs if any matrix fails. Default true 
        max-parallel: 2 ##control number of jobs to run in parallel 
        matrix:
          os: [ubuntu-latest, ubuntu-20.04, windows-latest]
          images: [hello-world, alpine]
          exclude:
            - images: alpine
              os: windows-latest
          include:
            - images: amd64/alpine
              os: ubuntu-20.04
      runs-on: ${{ matrix.os }}
      steps:
      - name: Echo Docker Details
        run: docker info

      - name: Run Image on ${{ matrix.os }}
        run: docker run ${{ matrix.images }}

```
## Contexts
```yaml
## A set of predefined objs or vars that contain info about a workflow run 
## contexts are a way to access info about a workflow runs, vars, runner env, jobs and steps 

name: Context testing
on: push

jobs:
  dump_contexts_to_log:
    runs-on: ubuntu-latest
    steps:
      - name: Dump GitHub context
        env:
          GITHUB_CONTEXT: ${{ toJson(github) }}
        run: echo "$GITHUB_CONTEXT"
      - name: Dump job context
        env:
          JOB_CONTEXT: ${{ toJson(job) }}
        run: echo "$JOB_CONTEXT"
      - name: Dump steps context
        env:
          STEPS_CONTEXT: ${{ toJson(steps) }}
        run: echo "$STEPS_CONTEXT"
      - name: Dump runner context
        env:
          RUNNER_CONTEXT: ${{ toJson(runner) }}
        run: echo "$RUNNER_CONTEXT"
      - name: Dump strategy context
        env:
          STRATEGY_CONTEXT: ${{ toJson(strategy) }}
        run: echo "$STRATEGY_CONTEXT"
      - name: Dump matrix context
        env:
          MATRIX_CONTEXT: ${{ toJson(matrix) }}
        run: echo "$MATRIX_CONTEXT"

```

## using if expression 

```yaml
deploy:
##using GITHUB context, using the ref 
   if: github.ref == 'refs/heads/main'
   needs: docker 

```

## workflow event filters and activity types

```yml
name: workflow filters and Activities

on:
  workflow_dispatch:

```