https://rominirani.com/docker-tutorial-series-part-6-docker-private-registry-15d1fd899255

	HQSML-151665:local pkrish00c$ docker run -d -p 5000:5000 --restart=always --name registry registry:2
	Unable to find image 'registry:2' locally
	2: Pulling from library/registry
	709515475419: Already exists
	df6e278d8f96: Pull complete
	4b0b08c1b8f7: Pull complete
	80119f43a01e: Pull complete
	acf34ba23c50: Pull complete
	Digest: sha256:412e3b6494f623a9f03f7f9f8b8118844deaecfea19e3a5f1ce54eed4f400296
	Status: Downloaded newer image for registry:2
	e34ebb4cc0ff7e15e9acda5414a29102f6e8ccdc84c6c76f3c4527d617985e5e
	HQSML-151665:local pkrish00c$ docker ps
	CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
	e34ebb4cc0ff        registry:2          "/entrypoint.sh /e..."   31 seconds ago      Up 30 seconds       0.0.0.0:5000->5000/tcp   registry
	ef6013dcc2f2        testhello           "python hello.py"        8 hours ago         Up 8 hours          0.0.0.0:8080->8080/tcp   unruffled_borg
	HQSML-151665:python_learn pkrish00c$ docker images
	REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
	testhello                                       latest              5bacf52e49c2        8 hours ago         99 MB
	HQSML-151665:python_learn pkrish00c$ docker tag testhello:latest localhost:5000/testhello:latest
	HQSML-151665:python_learn pkrish00c$ docker images
	REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
	localhost:5000/testhello                        latest              5bacf52e49c2        8 hours ago         99 MB
	testhello                                       latest              5bacf52e49c2        8 hours ago         99 MB
	HQSML-151665:python_learn pkrish00c$ docker push localhost:5000/testhello:latest
	The push refers to a repository [localhost:5000/testhello]
	118cd39f6d6b: Pushed
	9cec188f8aa8: Pushed
	e4b2f97aa582: Pushed
	58a22e92c3eb: Pushed
	9e9183fb7112: Pushed
	84af43867f07: Pushed
	af4997faab5b: Pushed
	9f8566ee5135: Pushed
	latest: digest: sha256:de18d9943b99885d4de94b157d00ea18a82f300860d867e89bcb8d2d06307371 size: 1989
	HQSML-151665:python_learn pkrish00c$ docker search localhost:5000/testhello
	Error response from daemon: Unexpected status code 404
	HQSML-151665:python_learn pkrish00c$ docker search localhost:5000/testhello:latest
	Error response from daemon: Unexpected status code 404
	HQSML-151665:python_learn pkrish00c$ docker pull localhost:5000/testhello
	Using default tag: latest
	latest: Pulling from testhello
	Digest: sha256:de18d9943b99885d4de94b157d00ea18a82f300860d867e89bcb8d2d06307371
	Status: Image is up to date for localhost:5000/testhello:latest
	HQSML-151665:python_learn pkrish00c$

