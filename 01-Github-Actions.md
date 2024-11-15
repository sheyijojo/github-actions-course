## Executing multiple jobs
- Need to share artifact for the next jobs
- upload buildartifact actions in the marketplace
- Download a Build Artiact in the market place 

```yaml
name: Genrate ASVII Art work
on:
  push
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
      runs-on: ubuuntu-latest
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

  deploy_job_3:
      needs: [test_job_2]
      runs-on: ubuntu-latest
      steps:
      - name: Read File
        run: cat dragon.txt



```
