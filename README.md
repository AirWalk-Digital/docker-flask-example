# Containerised Flask app example
An introductory example to Docker images, in the context of a Python Flask app.

#### We're going to contrast two ways of running this simple Flask App:

* Using a classic [python environment](https://docs.python.org/3/library/venv.html)
* Using a Docker container.

## Python environment
1. Create new python enviornment using [venv](https://docs.python.org/3/library/venv.html):
```
python3 -m venv env
```
2. Activate the environment: 
```
source env/bin/activate
```
3. Check the python path: `which python3` (this should point to our `env/bin/python3`)
    
4. Install the _flask_ package:
```
pip3 install flask
```
5. Run _app.py_
```
python3 app.py
```
6. Visit http://127.0.0.1:5000 to see our running Flask app. Yay!

## Docker 
1. Let's inspect the ___Dockerfile___ in our project root. What do all the layers seem to be doing?
2. To build a docker image from the Dockerfile, and give it a meaningful tag (**-t**), run: 
```
docker build -t my-flask-image .
```
_Notice the . at the end_

3. Whoops, what went wrong? You've noticed we don't have a _requirements.txt_ file, so let's use pip3 to write our packages to a file: 
``` pip3 freeze > requirements.txt ```
_This is a common approach to building python projects. Install the dependencies when you need them, then output them to requirements.txt, ideally keeping them to a minimum._

4. You can now re-run the same `docker build` command from step 2. Open Docker Desktop and find the built image. You can inspect its properties (size, underlying layers, packages, etc).

4. Run the image as a container: 
```
docker run my-flask-image
```
5. Notice that the output is now coming from the running container. Visit the same http://localhost:5000/. 

**What's going on?**

Our Flask server is indeed running inside the container, listening on port 5000. But there's a catch, the container doesn't know anything about the outside world. In other words, our local machine can't communicate with it, _yet_.

We need to tell Docker how to make the port 5000 accessible from **outside** the container. This is where **port mapping** comes into play.

Press CTRL+C in order to interrupt the running container.

6. It turns out that the **EXPOSE** command in our Dockerfile doesn't help too much (it's more of a documentation feature). However, the **-p** flag does. Let's use it.
```
docker run -p 5000:5000 my-flask-image
```
**`-p 5000:5000`** means: map the port 5000 on our local host (left) to port 5000 in the container (right).

7. Refresh the browser tab to see the running app. Well done! You are running a containerised Flask application, ready to serve traffic.

### More on Port Mapping

You can convince yourself about the ordering of the mapping by running:
```
docker run -p 3000:5000 my-flask-image
```
Now we've mapped port 3000 on our local host to port 5000 in the container. _Which url should you visit to see the running app?_

To learn more about port mapping and, more generally, networking within Docker - have a look at their [ documentation](https://docs.docker.com/engine/network/#published-ports).
