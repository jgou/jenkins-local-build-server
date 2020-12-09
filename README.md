# jenkins-local-build-server
Run locally Jenkins server to locally dev and test your pipelines

# How to start using it

Use the docker compose file in this repo to start Jenkins server container.

**Note**: You can run the docker compose file in a Linux computer or in Windows computers through WSL2

```
docker-compose up -d
```

**Note**: You will find Jenkins configuration files will be stored in your folder __~/.jenkins__

Check that the container is correctly running

```
docker container ls
```

You should get something like the following result

```
CONTAINER ID        IMAGE                 COMMAND                  CREATED              STATUS              PORTS                                              NAMES
fc1894f65429        jenkins/jenkins:lts   "/sbin/tini -- /usr/…"   About a minute ago   Up About a minute   0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   jenkins
```

Open a browser and insert the following url

```
http://localhost:8080/
```

A message asking for unlocking Jenkins will be displayed. You need to insert the initial admin password in the input field.
The following command returns this password

```
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

The password looks like '553fc45fa98b4469a50248f415476873'

Once the initial admin password is provided, Jenkins wizard will ask you for the plugins you want to install.
Select the ones you require in your environment. Select 'Install suggested plugins' if you still don't know what plugins you require.
Other plugins can be added later.

Finally, you can create your first admin user in Jenkins.

After saving you can start using Jenkins by browsing the following url

```
http://localhost:8080/
```

## Adding Jenkins workers

Although you can start using your Jenkins server using the master worker, you may want to add nodes (workers)
For example, you can configure a Windows worker (your laptop) and a Linux worker (WSL2 in our laptop) to trigger jobs in both OS.

Browse the following url or browse in Jenkins options to add a new node.

```
http://localhost:8080/computer/new
```

Configure the node name and select 'Permanent agent' option

In the next screen, fill the options you need. The only field required is the 'Remote root directory' field.
In my case, I selected __/mnt/c/jenkins__ to have the root directory in __c:\jenkins__.

Finally, you need to start the agent with the following commands (secret will change in your case, but this is something provided to you when you created the node)

```
cd /mnt/c/jenkins
curl http://localhost:8080/jnlpJars/agent.jar --output ./agent.jar
java -jar agent.jar -jnlpUrl http://localhost:8080/computer/WSL%20laptop/slave-agent.jnlp -secret f3682558c97e6b1e4a0e9f3deb8d9b1b56b06892076580a26b4dcf4563b01a12 -workDir "/mnt/c/jenkins"
```

You can start using this Jenkins worker to build your pipelines now.
