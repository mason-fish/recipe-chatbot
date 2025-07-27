# Recipe Chatbot - Okteto Deployment Guide

This guide explains how to deploy the Recipe Chatbot on Okteto.

## Prerequisites

1. **Okteto CLI**: Install from [okteto.com/docs/getting-started/installation](https://www.okteto.com/docs/getting-started/installation)
2. **API Keys**: You'll need at least one LLM provider API key (OpenAI, Anthropic, etc.)

## Quick Deployment

1. **Clone and navigate to the project**:
   ```bash
   git clone <your-repo-url>
   cd recipe-chatbot
   ```

2. **Set up environment variables**:
   ```bash
   cp .env.okteto .env
   # Edit .env and add your API keys
   ```

3. **Deploy to Okteto**:
   ```bash
   okteto deploy --wait
   ```

4. **Access your application**:
   ```bash
   okteto endpoints
   ```
   Your app will be available at: `https://recipe-chatbot-${OKTETO_NAMESPACE}.${OKTETO_DOMAIN}`

## Environment Variables

Configure these environment variables in your `.env` file or Okteto secrets:

| Variable | Description | Default |
|----------|-------------|---------|
| `MODEL_NAME` | LLM model to use | `openai/gpt-4o-mini` |
| `OPENAI_API_KEY` | OpenAI API key | - |
| `ANTHROPIC_API_KEY` | Anthropic API key | - |
| `TOGETHER_API_KEY` | Together AI API key | - |
| `GEMINI_API_KEY` | Google Gemini API key | - |

## Development Mode

To develop the application in Okteto:

```bash
okteto up recipe-chatbot
```

This will:
- Sync your local code to the remote container
- Forward port 8000 to your local machine
- Start a bash session in the container

Then run the development server:
```bash
uvicorn backend.main:app --reload --host 0.0.0.0 --port 8000
```

## Testing

Run the basic health checks:
```bash
okteto test unit
```

## Architecture

The deployment includes:

- **Deployment**: Runs the FastAPI application
- **Service**: Exposes the application internally
- **Ingress**: Provides external HTTPS access
- **Health Checks**: Liveness and readiness probes

## Troubleshooting

### Check application logs:
```bash
kubectl logs -l app=recipe-chatbot -n ${OKTETO_NAMESPACE}
```

### Check pod status:
```bash
kubectl get pods -n ${OKTETO_NAMESPACE}
```

### Validate Okteto manifest:
```bash
okteto validate
```

### Common Issues:

1. **Missing API Keys**: Ensure you've set at least one LLM provider API key
2. **Build Failures**: Check Docker build logs with `okteto build recipe-chatbot`
3. **Pod Not Starting**: Check resource limits and environment variables

## Security Notes

- API keys are passed as environment variables
- For production, consider using Okteto Secrets instead
- The application creates trace files in `/app/annotation/traces`

## File Structure

```
recipe-chatbot/
├── Dockerfile              # Container definition
├── okteto.yml              # Okteto manifest
├── k8s.yml                 # Kubernetes resources
├── .env.okteto             # Environment template
├── .dockerignore           # Docker build exclusions
└── OKTETO_DEPLOYMENT.md    # This guide
```

## Support

- [Okteto Documentation](https://www.okteto.com/docs/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [LiteLLM Providers](https://docs.litellm.ai/docs/providers)