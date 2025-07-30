To copy a custom model in Azure Document Intelligence between source and destination, you can use the **Copy Model** operation. Here's how to do it:

## Prerequisites
- Custom model trained in the source Document Intelligence resource
- Destination Document Intelligence resource in the target region/subscription
- Appropriate permissions on both resources

## Steps to Copy a Model

### 1. Get Copy Authorization from Destination
First, get authorization from the destination resource:

```http
POST https://{destination-endpoint}/documentintelligence/models:authorizeCopy?api-version=2023-07-31
Content-Type: application/json
Ocp-Apim-Subscription-Key: {destination-key}

{
  "modelId": "{new-model-id-in-destination}"
}
```

This returns a copy authorization token.

### 2. Initiate Copy from Source
Use the authorization token to start copying from the source:

```http
POST https://{source-endpoint}/documentintelligence/models/{source-model-id}:copy?api-version=2023-07-31
Content-Type: application/json
Ocp-Apim-Subscription-Key: {source-key}

{
  "targetResourceId": "{destination-resource-id}",
  "targetResourceRegion": "{destination-region}",
  "targetModelId": "{new-model-id-in-destination}",
  "accessToken": "{copy-authorization-token}",
  "expirationDateTime": "{expiration-time}"
}
```

### 3. Monitor Copy Operation
Track the copy progress using the operation ID returned from step 2:

```http
GET https://{source-endpoint}/documentintelligence/operations/{operation-id}?api-version=2023-07-31
Ocp-Apim-Subscription-Key: {source-key}
```

## Using Azure CLI
You can also use Azure CLI commands:

```bash
# Get copy authorization
az cognitiveservices account document-intelligence model copy-to \
  --resource-group {destination-rg} \
  --account-name {destination-resource} \
  --model-id {new-model-id}

# Copy model
az cognitiveservices account document-intelligence model copy \
  --resource-group {source-rg} \
  --account-name {source-resource} \
  --model-id {source-model-id} \
  --target-resource-id {destination-resource-id} \
  --target-region {destination-region} \
  --target-model-id {new-model-id} \
  --access-token {authorization-token}
```

## Important Notes
- The copy authorization token expires (typically after 24 hours)
- Both resources must be in the same pricing tier
- The destination model ID must be unique in the destination resource
- Custom neural models and composed models can be copied
- Training data is not copied, only the trained model

The copied model will be available in the destination resource once the operation completes successfully.
