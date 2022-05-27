# Setting Up For Development
This is how I set up my development environment on Ubuntu 22.04 LTS. I don't particularly recommend using WSL (which is what I would typically use) for this, as the script `docker_tools.py` doesn't support using Docker from within WSL.

---
### Docker Setup

```bash
mkdir runestone
cd runestone
curl -fsSLO https://raw.githubusercontent.com/RunestoneInteractive/RunestoneServer/master/docker/docker_tools.py
python3 docker_tools.py build --single-dev --clone-rc benstensen
```

Log out and log back in (or restart your VM). Then, navigate back to the `runestone` folder you initially created.
```bash
cd RunestoneServer
docker-compose up -d # This may take awhile on the first run
# type `docker-compose logs --follow` to follow the container logs
```

To stop all containers, make sure you are in the RunestoneServer directory, and then use the command
```bash
docker-compose stop
```

To restart all containers, make sure you are in the RunestoneServer directory, and then use the command 
```bash
docker-compose restart
```
---
### Runestone Server/Components Setup

Make sure that you're in RunestoneServer, and that your containers are running. We're going to add a textbook and a user to a course so you can see a page of an interactive textbook as you work.
```bash
pip install -e rsmanage
cd books
git clone https://github.com/RunestoneInteractive/fopp
# THIS SECTION IS OPTIONAL
    # I like to delete most of the chapters except for the chapter on sorting,
    # as this makes the book build faster.
    cd fopp/_sources
    # Remove every folder except for Sorting and _static
# END OF OPTIONAL SECTION
cd /path/to/RunestoneServer
rsmanage build --course fopp
rsmanage adduser
```
Follow the prompts from rsmanage. When asked for course name, input `fopp`. Don't forget your username and password; these credentials will be used to sign into Runestone on your machine. 

Now we will get RunestoneComponents set up.
```bash
# RunestoneComponents should be in the initial runestone directory you made.
cd ../RunestoneComponents 
git checkout -b collab
git pull origin collab
# Set up a Python virtual environment
python3 -m venv env
source env/bin/activate
pip install -r requirements-dev.txt
npm install
```
---

### Start Developing

At this point, you should have everything you need to start developing. I will personally have 3 tabs open in my terminal:

1. A tab watching the container logs (``docker-compose logs --follow`` from the ``RunestoneServer`` directory).
2. A tab in the ``RunestoneServer`` directory. Whenever you want to see the changes in your code reflected in the browser, type ``rsmanage build --course fopp``, and then refresh the page. 
3. A tab in the ``RunestoneComponents`` directory, running ``npm run watch``.

The key thing here: execute ``npm run build`` from ``runestone/RunestoneComponents``, then ``rsmanage build --course fopp`` from ``runestone/RunestoneServer`` when you want to update the scripts being executed in the browser. Runestone should be served to ``localhost``.

Most code related to the ActiveCode editor is in ``runestone/RunestoneComponents/runestone/activecode/js``. 