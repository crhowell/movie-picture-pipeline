# Movie Picture Pipeline

This project relies on deploying the infrastructure using Terraform and AWS. 
This means you will need to the `terraform` executable available in your terminal along with the `aws` CLI tool.

To run this project in a different repo while still leveraging CI/CD workflows these are the steps before automation takes over.

> NOTE: These steps will begin creating resources within AWS using your account which may incur costs.
> Run at your own risk


## 1. Terraform setup
You will need to ensure you are logged in with aws using `aws configure`

From the root directory of this repo run with `terraform` executable available from the terminal.

```
cd setup
terraform apply
```

## 2. AWS User Configuration
The previous step will have created an AWS user named `github-action-user` for you. You will need to go into the IAM Dashboard for that user and create an Access Key under the **Security Credentials** tab and select the option **Application running outside AWS** as this key will be for GitHub Actions to use.

Leave this screen open while you record the values to GitHub secrets or save them to your own local file.

## 3. GitHub Secrets Environment
These variables needs to be set within GitHub secrets.

* `AWS_ACCESS_KEY`
* `AWS_SECRET_KEY`

* `KUBECONFIG` -- this would be the output of `cat ~/.kube/config` saved as a secret for the GitHub Action to use when running kubectl and kustomize commands.

* `BACKEND_API_URL` -- This will come from the load balancer that was auto-setup when the backend finishes deploying. You will need the load balancers DNS name. You will need to prefix `http` or `https` to the value when saving it into the secrets environment. Probably just set it to a placeholder like `localhost` for the first build until the load balancer comes up and you can get the AWS generated DNS name for the backend load balancer that wont change.

## 4. GitHub Action user to K8s
From the repos root directory

```
cd setup
./init.sh
```

> This normally only fails if you dont a valid AWS id/key configured with `aws configure`
> Or if you need to perform a `aws eks update-kubeconfig --name=cluster`, then try again.

## 5. Making changes
From this point you should be able to modify code in the `src/backend` or `src/frontend` directories and push your changes and the CI/CD workflow for GitHub should Lint, Test, Build, and Deploy the code on Pushes into `main`

CI should run on Pull Requests against `main`

---

## Project Summary
You've been brought on as the DevOps resource for a development team that manages a web application that is a catalog of Movie Picture movies. They're in dire need of automating their development workflows in hopes of accelerating their release cycle. They'd like to use Github Actions to automate testing, building and deploying their applications to an existing Kubernetes cluster.

The team's project is comprised of 2 applications.

1. A frontend UI written in Typescript, using the React framework
2. A backend API written in Python using the Flask framework.

You'll find 2 folders, one named `frontend` and one named `backend`, where each application's source code is maintained. Your job is to use the team's [existing documentation](#frontend-development-notes) and create CI/CD pipelines to meet the teams' needs.

## Deliverables

### Frontend

1. A Continuous Integration workflow that:
   1. Runs on `pull_requests` against the `main` branch,only when code in the frontend application changes.
   2. Is able to be run on-demand (i.e. manually without needing to push code)
   3. Runs the following jobs in parallel:
      1. Runs a linting job that fails if the code doesn't adhere to eslint rules
      2. Runs a test job that fails if the test suite doesn't pass
   4. Runs a build job only if the lint and test jobs pass and successfully builds the application
2. A Continuous Deployment workflow that:
   1. Runs on `push` against the `main` branch, only when code in the frontend application changes.
   2. Is able to be run on-demand (i.e. manually without needing to push code)
   3. Runs the same lint/test jobs as the Continuous Integration workflow
   4. Runs a build job only when the lint and test jobs pass
      1. The built docker image should be tagged with the git sha
   5. Runs a deploy job that applies the Kubernetes manifests to the provided cluster.
      1. The manifest should deploy the newly created tagged image
      2. The tag applied to the image should be the git SHA of the commit that triggered the build

### Backend

1. A Continuous Integration workflow that:
   1. Runs on `pull_requests` against the `main` branch,only when code in the frontend application changes.
   2. Is able to be run on-demand (i.e. manually without needing to push code)
   3. Runs the following jobs in parallel:
      1. Runs a linting job that fails if the code doesn't adhere to eslint rules
      2. Runs a test job that fails if the test suite doesn't pass
   4. Runs a build job only if the lint and test jobs pass and successfully builds the application
2. A Continuous Deployment workflow that:
   1. Runs on `push` against the `main` branch, only when code in the frontend application changes.
   2. Is able to be run on-demand (i.e. manually without needing to push code)
   3. Runs the same lint/test jobs as the Continuous Integration workflow
   4. Runs a build job only when the lint and test jobs pass
      1. The built docker image should be tagged with the git sha
   5. Runs a deploy job that applies the Kubernetes manifests to the provided cluster.
      1. The manifest should deploy the newly created tagged image
      2. The tag applied to the image should be the git SHA of the commit that triggered the build


**⚠️ NOTE**
Once you begin work on Continuous Deployment, you'll need to first setup the AWS and Kubernetes environment.


## License

[License](LICENSE.md)
