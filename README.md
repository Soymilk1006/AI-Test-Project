# Twin

A cloud-native Digital Twin app with a static React frontend, a serverless Python backend, and AWS infrastructure managed by Terraform.

## What this project does

- Hosts a static website on S3 + CloudFront.
- Runs a FastAPI backend as an AWS Lambda function.
- Exposes the backend through API Gateway.
- Stores conversation memory in S3.
- Optionally supports a custom domain via Route53 and ACM.

## Components

### `frontend/`

- Next.js app serving the web UI.
- Uses `components/twin.tsx` to render the main experience.
- Built and deployed as a static site to the S3 frontend bucket.
- Reads the backend API URL from `NEXT_PUBLIC_API_URL`.

### `backend/`

- Python FastAPI application.
- `backend/server.py` contains the application logic.
- `backend/lambda_handler.py` exposes the app through Mangum for AWS Lambda.
- `backend/deploy.py` packages the Lambda bundle into `backend/lambda-deployment.zip` to upload the zip to the s3 bucket
- Dependencies are declared in `backend/pyproject.toml`.

### `terraform/`

- Defines AWS resources for the full stack.
- Creates S3 buckets for frontend hosting and memory storage.
- Creates IAM roles, Lambda functions, API Gateway, and CloudFront distribution.
- Optionally configures Route53 and ACM for a custom domain.

### `scripts/deploy.sh`

- Builds the backend Lambda package.
- Initializes and applies Terraform.
- Builds the frontend.
- Syncs the built static site to the S3 bucket.
- Prints the deployed CloudFront and API endpoints.

## How to run

### 1. Install prerequisites

- Node.js / npm
- Python 3.12+
- AWS CLI configured with credentials
- Terraform

### 2. Build the backend package

```bash
cd backend
uv run deploy.py
```

### 3. Deploy the full stack

From the repository root:

```bash
./scripts/deploy.sh
```

Optional:

```bash
./scripts/deploy.sh dev twin
```

### 4. Run frontend locally

```bash
cd frontend
npm install
npm run dev
```

Open `http://localhost:3000`.

## Notes

- `deploy.sh` uses AWS credentials from the CLI.
- Terraform state is managed in the configured backend.
- The backend Lambda package is built before Terraform runs.
- If you want a custom domain, enable the related Terraform variables and ensure Route53/ACM support.
