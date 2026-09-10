# BankPulse

A live monitor for Indian banking & financial services — aggregates bank news, RBI notices, and UPI transaction data into one dashboard showing sentiment, active incidents, and industry trends.

Personal side project, built to get hands-on with a real AWS data pipeline end to end (ingestion → lake → enrichment → warehouse → dashboard).

## How it works

```
News + RBI + UPI data
        │
        ▼
   Ingestion (Lambda + EventBridge schedules)
        │
        ▼
   Raw lake (S3)
        │
        ▼
   Enrichment (Glue + LLM summarization/sentiment)
        │
        ▼
   Curated tables (S3 / Athena)
        │
        ▼
   Dashboard (React, on Amplify)
```

## Repo structure

```
apps/web/          Dashboard frontend — deployed via Amplify Hosting
services/          One folder per Lambda (ingestion, enrichment, api)
glue-jobs/         One file per Glue job (transforms, curated tables)
infra/             AWS CDK app (Python) — all infrastructure, one deploy
manifest/          Data lineage manifest
```

## Stack

Lambda · Glue · S3 · DynamoDB · EventBridge · AWS CDK · Amplify · React

## Local setup

```bash
# frontend
cd apps/web
npm install
npm run dev

# a Lambda — install its deps locally to develop/test
cd services/ingestion-news
pip install -r requirements.txt

# infra
cd infra
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
cdk synth      # sanity-check the generated CloudFormation
cdk diff       # see what would change before deploying
```

## Deploying

- **Frontend** — push to `main`, Amplify builds `apps/web` automatically.
- **Everything else** (Lambdas, Glue jobs, infra) — from `infra/`, run:
  ```bash
  cdk deploy --all
  ```
  No separate state bucket to manage — CDK stores stack state in CloudFormation, which AWS handles.

## Status

Early build. First ingestion job (`services/ingestion-news`) is scaffolded but not yet implemented — see the TODOs in `handler.py`. Its EventBridge schedule is deployed disabled until that's done. See `manifest/lineage.yaml` for what's wired up so far.

## License

MIT
