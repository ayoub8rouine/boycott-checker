# ConsumeSafe

An Angular web application for checking boycotted products. Built with Angular 19, featuring SSR, Docker containerization, Kubernetes deployment, and Jenkins CI/CD.

## Prerequisites

- **Node.js** (v18+): [Download](https://nodejs.org/)
- **npm** (comes with Node.js)
- **Angular CLI**: `npm install -g @angular/cli`
- **Docker** (for containerization)
- **Kubernetes** (for orchestration, e.g., Minikube)
- **Jenkins** (for CI/CD, optional for local development)

## Installation

1. Clone the repo:
   ```bash
   git clone https://github.com/ayoub8rouine/boycott-checker.git
   cd boycott-checker
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

## Running Locally

### Development Server
```bash
npm start
```
- Opens at `http://localhost:4200`
- Auto-reloads on changes.

### Build for Production
```bash
npm run build
```
- Artifacts in `dist/`

### Run Tests
```bash
npm test
```
- Uses Karma with headless Chrome.

## Docker

### Build Image
```bash
docker build -t consume-safe:latest .
```

### Run Container
```bash
docker run -p 8080:80 consume-safe:latest
```
- Access at `http://localhost:8080`

## Kubernetes

### Deploy Locally
1. Ensure K8s cluster (e.g., Minikube) is running.
2. Build and load image: `docker build -t consume-safe:latest . && minikube image load consume-safe:latest`
3. Apply manifests: `kubectl apply -f k8s/`
4. Check: `kubectl get pods,services`

### Access
- Via Ingress or `minikube service consume-safe`

## CI/CD with Jenkins

The project includes a `Jenkinsfile` for automated pipelines.

### Setup
1. Create a Jenkins Pipeline job pointing to this repo.
2. Add credentials:
   - GitHub PAT (ID: `right`)
   - DockerHub token (ID: `dokcer-paswd`)
   - K8s kubeconfig (ID: `kube-configfile`)
3. Enable GitHub webhooks for push detection.
4. On push, Jenkins builds, tests, pushes Docker image, and deploys to K8s.

### Manual Trigger
- Build the job manually in Jenkins.

## Project Structure

- `src/app/`: Angular components
- `k8s/`: Kubernetes manifests
- `playbooks/`: Ansible scripts (legacy)
- `Jenkinsfile`: CI/CD pipeline
- `Dockerfile`: Container build

## Contributing

1. Fork the repo.
2. Create a feature branch.
3. Commit changes.
4. Push and create a PR.

## License

MIT License.
