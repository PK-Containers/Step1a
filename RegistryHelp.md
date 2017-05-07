### To stop registry and remove registry

HQSML-151665:pkrish00c$ docker stop registry && docker rm -v registry
registry
registry
HQSML-151665:pkrish00c$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES


### Delete all containers
docker rm $(docker ps -a -q)

### Delete all images
docker rmi $(docker images -q)

Use -f for force remove

## Docker Image creation

### Notes
1. Each command in Dockerfile is created as file system layer

		HQSML-151665:Step1a pkrish00c$ docker build -t helloworld .
		Sending build context to Docker daemon 146.4 kB
		Step 1/8 : FROM python:3.5-alpine
		3.5-alpine: Pulling from library/python
		709515475419: Already exists
		7f8ede2d2484: Already exists
		139e26659560: Pull complete
		6e27808262c9: Pull complete
		4e1a9260ef8d: Pull complete
		Digest: sha256:99c05cf0fbd9b199c1ef8b393be49d3cb311bbb01b82829195a8ace9c510db8a
		Status: Downloaded newer image for python:3.5-alpine
		 ---> 9691bd606b6d
		Step 2/8 : EXPOSE 4000
		 ---> Running in cf1b6390c668
		 ---> 9b1a686376b3
		Removing intermediate container cf1b6390c668
		Step 3/8 : RUN mkdir -p /usr/src/app
		 ---> Running in e973058b1f01
		 ---> 5979962d9469
		Removing intermediate container e973058b1f01
		Step 4/8 : WORKDIR /usr/src/app
		 ---> 28889f135462
		Removing intermediate container 655a3ac78c9f
		Step 5/8 : COPY requirements.txt /usr/src/app/
		 ---> ed356c8c4ee5
		Removing intermediate container bcab93a36ad4
		Step 6/8 : RUN pip install --no-cache-dir -r requirements.txt
		 ---> Running in 14298307b0ce
		Collecting flask (from -r requirements.txt (line 1))
		  Downloading Flask-0.12.1-py2.py3-none-any.whl (82kB)
		Collecting click>=2.0 (from flask->-r requirements.txt (line 1))
		  Downloading click-6.7-py2.py3-none-any.whl (71kB)
		Collecting Jinja2>=2.4 (from flask->-r requirements.txt (line 1))
		  Downloading Jinja2-2.9.6-py2.py3-none-any.whl (340kB)
		Collecting itsdangerous>=0.21 (from flask->-r requirements.txt (line 1))
		  Downloading itsdangerous-0.24.tar.gz (46kB)
		Collecting Werkzeug>=0.7 (from flask->-r requirements.txt (line 1))
		  Downloading Werkzeug-0.12.1-py2.py3-none-any.whl (312kB)
		Collecting MarkupSafe>=0.23 (from Jinja2>=2.4->flask->-r requirements.txt (line 1))
		  Downloading MarkupSafe-1.0.tar.gz
		Installing collected packages: click, MarkupSafe, Jinja2, itsdangerous, Werkzeug, flask
		  Running setup.py install for MarkupSafe: started
		    Running setup.py install for MarkupSafe: finished with status 'done'
		  Running setup.py install for itsdangerous: started
		    Running setup.py install for itsdangerous: finished with status 'done'
		Successfully installed Jinja2-2.9.6 MarkupSafe-1.0 Werkzeug-0.12.1 click-6.7 flask-0.12.1 itsdangerous-0.24
		 ---> c4052d38c6da
		Removing intermediate container 14298307b0ce
		Step 7/8 : COPY hello.py /usr/src/app/hello.py
		 ---> 45b62755a815
		Removing intermediate container 2be0f963a37b
		Step 8/8 : CMD python hello.py
		 ---> Running in b88cd7d41e5f
		 ---> 067cd06b4060
		Removing intermediate container b88cd7d41e5f
		Successfully built 067cd06b4060
		
2. Images

		HQSML-151665:Step1a pkrish00c$ docker images
		REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
		helloworld          latest              067cd06b4060        6 minutes ago       99.5 MB
		python              3.5-alpine          9691bd606b6d        5 days ago          89.3 MB

3. When you try to run an image, it will always check if image is available locally, else pull from local registry if present/indicated, else from docker library(hub) registry

		HQSML-151665:Step1a pkrish00c$ docker run -d -p 5000:5000 --restart=always --name registry registry:2
		Unable to find image 'registry:2' locally
		2: Pulling from library/registry
		709515475419: Already exists
		df6e278d8f96: Pull complete
		4b0b08c1b8f7: Pull complete
		80119f43a01e: Pull complete
		acf34ba23c50: Pull complete
		Digest: sha256:412e3b6494f623a9f03f7f9f8b8118844deaecfea19e3a5f1ce54eed4f400296
		Status: Downloaded newer image for registry:2
		2412f6d4b51f5d54422552281a15b562bc6ab87db8825e33c028951541805156
		
4. Tagging an image

		HQSML-151665:Step1a pkrish00c$ docker tag helloworld:latest localhost:5000/helloworld:latest
		HQSML-151665:Step1a pkrish00c$ docker images
		REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
		helloworld                  latest              067cd06b4060        10 minutes ago      99.5 MB
		localhost:5000/helloworld   latest              067cd06b4060        10 minutes ago      99.5 MB
		python                      3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
		registry                    2                   136c8b16df20        4 weeks ago         33.2 MB
		
5. Docker search

	a. Search does not work.
	
	b. While untagging, the tagged image is removed leaving parent in tact.
		
			HQSML-151665:Step1a pkrish00c$ docker search localhost:5000/helloworld
			Error response from daemon: Unexpected status code 404
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
			helloworld                  latest              067cd06b4060        15 minutes ago      99.5 MB
			localhost:5000/helloworld   latest              067cd06b4060        15 minutes ago      99.5 MB
			python                      3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry                    2                   136c8b16df20        4 weeks ago         33.2 MB
			HQSML-151665:Step1a pkrish00c$ docker rmi localhost:5000/helloworld
			Untagged: localhost:5000/helloworld:latest
			Untagged: localhost:5000/helloworld@sha256:532be5725bff05f07adc80e8be8883a483a3248b6f814d04884189b6706021d9
			HQSML-151665:Step1a pkrish00c$ docker search localhost:5000/helloworld
			Error response from daemon: Unexpected status code 404
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
			helloworld          latest              067cd06b4060        16 minutes ago      99.5 MB
			python              3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry            2                   136c8b16df20        4 weeks ago         33.2 MB


6. Even after untagging, pull image localhost:5000/helloworld will pull from registry, if registry runs, else will error

			HQSML-151665:Step1a pkrish00c$ docker pull localhost:5000/helloworld
			Using default tag: latest
			latest: Pulling from helloworld
			Digest: sha256:532be5725bff05f07adc80e8be8883a483a3248b6f814d04884189b6706021d9
			Status: Downloaded newer image for localhost:5000/helloworld:latest
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
			helloworld                  latest              067cd06b4060        21 minutes ago      99.5 MB
			localhost:5000/helloworld   latest              067cd06b4060        21 minutes ago      99.5 MB
			python                      3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry                    2                   136c8b16df20        4 weeks ago         33.2 MB
			
If registry not running

			HQSML-151665:Step1a pkrish00c$ docker pull localhost:5000/helloworld
			Using default tag: latest
			Pulling repository localhost:5000/helloworld
			Error while pulling image: Get http://localhost:5000/v1/repositories/helloworld/images: dial tcp [::1]:5000: getsockopt: connection refused
			
7. Remove all images locally to check if it pulls from registry

			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
			helloworld                  latest              067cd06b4060        21 minutes ago      99.5 MB
			localhost:5000/helloworld   latest              067cd06b4060        21 minutes ago      99.5 MB
			python                      3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry                    2                   136c8b16df20        4 weeks ago         33.2 MB
			HQSML-151665:Step1a pkrish00c$ docker rmi -f 067cd06b4060
			Untagged: helloworld:latest
			Untagged: localhost:5000/helloworld:latest
			Untagged: localhost:5000/helloworld@sha256:532be5725bff05f07adc80e8be8883a483a3248b6f814d04884189b6706021d9
			Deleted: sha256:067cd06b4060cecde77602f9ce986ac015ce3d92b1c39303de2fde9b7e2a8c62
			Deleted: sha256:45b62755a8155e2750889477cd14656f8434c85430a612eee80f542c378ed008
			Deleted: sha256:30379a34d09dde4008d539d14a4c34863873b1b78b0851573bf7530a6970ae9a
			Deleted: sha256:c4052d38c6da7962c372f411b2e76f8f8a261af8c44d4047aa9c8cbe21dd1236
			Deleted: sha256:b50cd05e5b90f41c4203c7e2868f4e6de3d1de635fdea8352fecb12f0281abd9
			Deleted: sha256:ed356c8c4ee55adb4b3d009f3a91e8be0ca4102d6663c35cacffd312da67a0af
			Deleted: sha256:8b1d3a3e0282a7eabb77bbca4e7d372fac07a993359b592a27908deb7dbf8331
			Deleted: sha256:28889f135462ee742e82484eb895d39da5a618e9b943dd8a9174a6677110527e
			Deleted: sha256:5979962d9469463bebbebb673b41fd8a1683e4fcf13e80b6d9d1054183ed08ac
			Deleted: sha256:22aa945134b55f02b5bee26498f3003bc83320ad6a61f9e3c633743c7babf544
			Deleted: sha256:9b1a686376b370c155f5fa06882be106b2789fbf39b4d49caa86ca684599cf13
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
			python              3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry            2                   136c8b16df20        4 weeks ago         33.2 MB
			
8. Now do a pull to see if it gets from registry. 

Note: Images pulled from registry has the tagged name with which we pushed, not helloworld

			HQSML-151665:Step1a pkrish00c$ docker pull localhost:5000/helloworld
			Using default tag: latest
			latest: Pulling from helloworld
			12a7970a6783: Already exists
			be08563ff9b0: Already exists
			139e26659560: Already exists
			6e27808262c9: Already exists
			4e1a9260ef8d: Already exists
			9d91e33b9538: Pull complete
			a779d853e533: Pull complete
			be3effc257ba: Pull complete
			dbf434452e9e: Pull complete
			Digest: sha256:532be5725bff05f07adc80e8be8883a483a3248b6f814d04884189b6706021d9
			Status: Downloaded newer image for localhost:5000/helloworld:latest
			
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
			localhost:5000/helloworld   latest              067cd06b4060        23 minutes ago      99.5 MB
			python                      3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry                    2                   136c8b16df20        4 weeks ago         33.2 MB
		
			
Stop registry and try pulling image to ensure that there is no local instance available

			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
			localhost:5000/helloworld   latest              067cd06b4060        24 minutes ago      99.5 MB
			python                      3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry                    2                   136c8b16df20        4 weeks ago         33.2 MB
			HQSML-151665:Step1a pkrish00c$ docker rmi 067cd06b4060
			Untagged: localhost:5000/helloworld:latest
			Untagged: localhost:5000/helloworld@sha256:532be5725bff05f07adc80e8be8883a483a3248b6f814d04884189b6706021d9
			Deleted: sha256:067cd06b4060cecde77602f9ce986ac015ce3d92b1c39303de2fde9b7e2a8c62
			Deleted: sha256:30379a34d09dde4008d539d14a4c34863873b1b78b0851573bf7530a6970ae9a
			Deleted: sha256:b50cd05e5b90f41c4203c7e2868f4e6de3d1de635fdea8352fecb12f0281abd9
			Deleted: sha256:8b1d3a3e0282a7eabb77bbca4e7d372fac07a993359b592a27908deb7dbf8331
			Deleted: sha256:22aa945134b55f02b5bee26498f3003bc83320ad6a61f9e3c633743c7babf544
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
			python              3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry            2                   136c8b16df20        4 weeks ago         33.2 MB
			HQSML-151665:Step1a pkrish00c$ docker stop registry
			registry
			HQSML-151665:Step1a pkrish00c$ docker ps
			CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
			HQSML-151665:Step1a pkrish00c$ docker images
			REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
			python              3.5-alpine          9691bd606b6d        5 days ago          89.3 MB
			registry            2                   136c8b16df20        4 weeks ago         33.2 MB
			HQSML-151665:Step1a pkrish00c$ docker pull localhost:5000/helloworld
			Using default tag: latest
			Pulling repository localhost:5000/helloworld
			Error while pulling image: Get http://localhost:5000/v1/repositories/helloworld/images: dial tcp [::1]:5000: getsockopt: connection refused
			
However the image is available in registry and if we start registry, we can access it. If registry is deleted, then we will not have the image even if we restart.

			HQSML-151665:Step1a pkrish00c$ docker start registry
			registry
			HQSML-151665:Step1a pkrish00c$ docker pull localhost:5000/helloworld
			Using default tag: latest
			latest: Pulling from helloworld
			12a7970a6783: Already exists
			be08563ff9b0: Already exists
			139e26659560: Already exists
			6e27808262c9: Already exists
			4e1a9260ef8d: Already exists
			9d91e33b9538: Pull complete
			a779d853e533: Pull complete
			be3effc257ba: Pull complete
			dbf434452e9e: Pull complete
			Digest: sha256:532be5725bff05f07adc80e8be8883a483a3248b6f814d04884189b6706021d9
			Status: Downloaded newer image for localhost:5000/helloworld:latest
			HQSML-151665:Step1a pkrish00c$
			
To remove an image, we first need to untag and then remove

			HQSML-151665:Step1a pkrish00c$ docker rmi 136c8b16df20
			Error response from daemon: conflict: unable to delete 136c8b16df20 (must be forced) - image is being used by stopped container 2412f6d4b51f
			
			HQSML-151665:Step1a pkrish00c$ docker rmi -f 136c8b16df20
			Untagged: registry:2
			Untagged: registry@sha256:412e3b6494f623a9f03f7f9f8b8118844deaecfea19e3a5f1ce54eed4f400296
			Deleted: sha256:136c8b16df203ef26b2f39e24bd3f403b63be67610ec99a5b5af0cceac5c1b51

If registry is not running and if image is available locally with tagged name (localhost:5000/helloworld), docker run will still work. Even if untagged it will work.

			HQSML-151665:Step1a pkrish00c$ docker tag helloworld:latest localhost:5000/helloworld:latest
			HQSML-151665:Step1a pkrish00c$ docker ps
			CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
			HQSML-151665:Step1a pkrish00c$ docker run -d -p 4000:4000 localhost:5000/helloworld:latest
			82904a4c9ffb021b2147464969191b1ee665df3c8d693c316915938772a70c33
			HQSML-151665:Step1a pkrish00c$ docker ps
			CONTAINER ID        IMAGE                              COMMAND             CREATED             STATUS              PORTS                    NAMES
			82904a4c9ffb        localhost:5000/helloworld:latest   "python hello.py"   3 seconds ago       Up 2 seconds        0.0.0.0:4000->4000/tcp   determined_morse
			
However if untagged, run or pull will not work ->

			HQSML-151665:Step1a pkrish00c$ docker ps
			CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
			HQSML-151665:Step1a pkrish00c$ docker rmi localhost:5000/helloworld
			Untagged: localhost:5000/helloworld:latest
			HQSML-151665:Step1a pkrish00c$ docker run -d -p 4000:4000 localhost:5000/helloworld:latest
			Unable to find image 'localhost:5000/helloworld:latest' locally
			Pulling repository localhost:5000/helloworld
			docker: Error while pulling image: Get http://localhost:5000/v1/repositories/helloworld/images: dial tcp [::1]:5000: getsockopt: connection refused.
			See 'docker run --help'.
			
			HQSML-151665:Step1a pkrish00c$ docker pull localhost:5000/helloworld
			Using default tag: latest
			Pulling repository localhost:5000/helloworld
			Error while pulling image: Get http://localhost:5000/v1/repositories/helloworld/images: dial tcp [::1]:5000: getsockopt: connection refused










