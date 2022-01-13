# 100 - Deployment Pipeline

![deployment-pipeline-dark](https://user-images.githubusercontent.com/12828104/149342136-89a82add-6580-440b-936f-61ab4f3a7955.png)

Deployment Pipeline

I think it can be quite instructive of overall architecture to describe how an app is deployed. The [Excalidraw](https://excalidraw.com/) diagram above describes it visually. Allow me to describe it in written form as well:

First, I commit a change to the local repo. Then I push my changes to the (open source) GitHub repository. From there, I have two GitHub Actions that run automatically on every push to the main branch.

The "Discord" circle just indicates that I've got a GitHub webhook installed for discord so each success/failure will result in a message in a channel on discord so I can easily keep track of how things are going at any time.

## 100 - GitHub Actions

See [README.md](./100/README.md)

## 200 - Database Connectivity

See [README.md](./200/README.md)

## 300 - Fly Request Replays

See [README.md](./300/README.md)
