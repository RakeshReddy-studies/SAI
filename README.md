# Set 11: Integrate GitHub with Jenkins to automatically deploy code changes to Kubernetes

## Steps
1. Push this folder to a GitHub repo.
2. Jenkins: install *GitHub* plugin; create Pipeline job > *Pipeline script from SCM* > Git > your repo URL.
3. In job config tick **GitHub hook trigger for GITScm polling**.
4. GitHub repo > Settings > Webhooks > Add: Payload URL `http://<jenkins-host>:8080/github-webhook/`, content type `application/json`, event: *push*.
   (Local Jenkins? expose it with `ngrok http 8080`.)
5. Add credentials `dockerhub-cred`, `kubeconfig-cred`.
6. Commit a change to app/index.html and push.

## Verify
Each git push triggers a build automatically and the new image rolls out.

## Prerequisites
Credentials `kubeconfig-cred` (Secret file) and `dockerhub-cred` (user/password) in Jenkins; replace `YOUR_DOCKERHUB_USER` with your Docker Hub username.
