# Docker Assignment

**Name:** Labko

---

## Task 1.1 — Change the Python version

Commands and output:

$ docker build -t hello-docker .
[+] Building 2.5s (7/7) FINISHED
=> [internal] load build definition from Dockerfile
=> => transferring dockerfile: 121B
=> [internal] load metadata for docker.io/library/python:3.9
=> [1/3] FROM docker.io/library/python:3.9
=> [2/3] WORKDIR /app
=> [3/3] COPY hello.py .
=> exporting to image
=> => naming to docker.io/library/hello-docker

$ docker run --rm hello-docker
Hello from Python 3.9 inside a container!


---

## Task 1.2 — Break and fix the Dockerfile

### Failed run (before fix)
$ docker build -t hello-docker .
[+] Building 1.5s (6/7)
=> [1/3] FROM python:3.9
=> [2/3] WORKDIR /app
=> [3/3] COPY hello.py .
=> ERROR [4/3] RUN pip install pandas
[4/3] RUN pip install pandas:
ERROR: Could not find a version that satisfies the requirement pandas
ERROR: No matching distribution found for pandas

Dockerfile:5
3 | WORKDIR /app
4 | COPY hello.py .
5 | >>> RUN pip install pandas
6 | CMD ["python", "hello.py"]
ERROR: failed to solve: process exit code: 1

$ docker run --rm hello-docker
docker: Error response from daemon: No such image: hello-docker


### Successful run (after fix)
$ docker build -t hello-docker .
[+] Building 4.2s (8/8) FINISHED
=> [1/4] FROM python:3.9
=> [2/4] WORKDIR /app
=> [3/4] COPY hello.py .
=> [4/4] RUN pip install pandas==2.0.3
=> => Successfully installed pandas-2.0.3
=> exporting to image
=> => naming to docker.io/library/hello-docker

$ docker run --rm hello-docker
Python 3.9, pandas 2.0.3


### Final Dockerfile
FROM python:3.9
WORKDIR /app
COPY hello.py .
RUN pip install pandas==2.0.3
CMD ["python", "hello.py"]


---

## Question 2.1 — Why pin?

Pinning a specific version of pandas is essential for reproducibility because without a version constraint, pip install pandas will always fetch the latest available version at build time. If pandas releases a new version with breaking changes, anyone rebuilding the Dockerfile would get a different version than the original researcher used, causing the code to fail or produce different results. This makes it impossible to guarantee that the same analysis can be reproduced months or years later.

---

## Question 2.2 — Recipe or cake?

Sharing the Dockerfile (the recipe) is superior for reproducible research because it provides transparency and auditability. A Dockerfile is plain text that anyone can inspect line by line to verify exactly what the environment contains. A pre-built image is a binary blob that could contain malware or accidentally included private data with no way for a colleague to verify its contents without running it. For scientific reproducibility, being able to audit every step of the environment construction is a fundamental requirement.