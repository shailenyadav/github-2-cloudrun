# Deploy to Google Cloud Run

This GitHub Actions workflow automates the deployment of a Docker container to Google Cloud Run. The workflow is triggered on pushes and pull requests to the `dev` branch.

## Permissions

The workflow requires the following permissions:

- **id-token:** Write access
- **contents:** Read access
- **pull-requests:** Write access

## Workflow Steps

### 1. Setup Google Cloud

- **Job:** `gcloud-setup`
- **Runs on:** `ubuntu-latest`

#### Steps:

1. **Checkout:**
   - Uses the `actions/checkout@v3` action to clone the repository.

2. **Google Cloud Authentication:**
   - Uses the `google-github-actions/auth@v1` action to authenticate with Google Cloud.
   - Token format: `access_token`
   - Workload identity provider: specified in the `POOL_URL` secret.
   - Service account: specified in the `SERVICE_ACCOUNT` secret.

3. **Docker Authentication:**
   - Uses the `docker/login-action@v1` action to authenticate with Docker.
   - Uses the access token obtained from the Google Cloud authentication step.
   - Registry: `${{ vars.GCP_REGION }}-docker.pkg.dev`

4. **Build, Tag, and Push Container:**
   - Uses the `docker/build-push-action@v3` action to build, tag, and push the Docker container.
   - Context: `${{ env.code_directory }}`
   - Tags: `${{ vars.GCP_REGION }}-docker.pkg.dev/${{ vars.GCP_PROJECT_ID }}/${{ vars.ARTIFACT_REPO }}/${{ vars.SERVICE_NAME }}:latest`

5. **Deploy to Cloud Run:**
   - Uses the `google-github-actions/deploy-cloudrun@v1` action to deploy the container to Google Cloud Run.
   - Service name: `<cloudrun_name>` (replace with your Cloud Run service name)
   - Region: `${{ vars.GCP_REGION }}`
   - Image: `${{ vars.GCP_REGION }}-docker.pkg.dev/${{ vars.GCP_PROJECT_ID }}/${{ vars.ARTIFACT_REPO }}/${{ vars.SERVICE_NAME }}:latest`

### Environment Variables

- `CLOUDSDK_CORE_DISABLE_PROMPTS`: Set to `1` to disable prompts in the Google Cloud SDK.

Make sure to replace placeholder values (`<cloudrun_name>`, etc.) with your specific configuration.
