# Databricks Data Pipeline Project

## Overview
This project implements a robust data pipeline using Databricks, designed for scalable data processing and transformation. The pipeline includes data validation, transformation, and logging capabilities.

## Project Structure 
```bash
.
├── main.py # Main pipeline implementation
├── .gitignore # Git ignore file
└── README.md # Project documentation
```

## Pipeline Features
- Data reading from multiple formats
- Custom data transformations
- Data validation
- Error handling and logging
- Delta Lake support
- Configurable input/output paths

## Code Overview

### Main Components 

```python
class DataPipeline:
def read_data() # Reads data from source
def transform_data() # Applies transformations
def validate_data() # Validates data quality
def write_data() # Writes data to target
def run_pipeline() # Orchestrates the pipeline
```

## Databricks Setup Using Azure CLI

### 1. Install Azure CLI
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### 2. Login to Azure
```bash
az login
```

### 3. Create Resource Group
```bash
az group create --name your-rg-name --location eastus
```

### 4. Create Databricks Workspace
```bash
az databricks workspace create \
    --resource-group your-rg-name \
    --name your-workspace-name \
    --location eastus \
    --sku standard
```

### 5. Create Managed Identity
```bash
az identity create \
    --name your-identity-name \
    --resource-group your-rg-name
```

### 6. Assign Roles to Managed Identity
```bash
az role assignment create \
    --assignee-object-id <managed-identity-object-id> \
    --role "Storage Blob Data Contributor" \
    --scope /subscriptions/<subscription-id>/resourceGroups/<resource-group>
```

## Linking GitHub Repository with Databricks

1. **In Databricks Workspace**:
   - Go to Repos
   - Click "Add Repo"
   - Select "GitHub"

2. **GitHub Authentication**:
   - Complete OAuth process
   - Select your repository
   - Choose branch

3. **Configure Git Integration**:
   ```bash
   # In your local repository
   git remote add databricks https://your-databricks-instance/repo/your-repo
   ```

## Creating a Data Pipeline in Databricks

1. **Create Cluster**:
   ```bash
   az databricks cluster create \
       --workspace-name your-workspace \
       --cluster-name your-cluster \
       --spark-version 7.3.x-scala2.12 \
       --node-type Standard_DS3_v2 \
       --min-workers 1 \
       --max-workers 2
   ```

2. **Mount Storage**:
   ```python
   dbutils.fs.mount(
       source = "wasbs://container@storage.blob.core.windows.net",
       mount_point = "/mnt/data",
       extra_configs = {
           "fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<managed-identity-client-id>"
       }
   )
   ```

3. **Create Pipeline Job**:
   ```bash
   az databricks job create \
       --workspace-name your-workspace \
       --name "Data Pipeline Job" \
       --existing-cluster-id your-cluster-id \
       --notebook-path "/Repos/your-repo/main"
   ```

## Running the Pipeline

1. **Manual Execution**:
   ```python
   pipeline = DataPipeline()
   pipeline.run_pipeline(
       source_path="/mnt/data/source",
       target_path="/mnt/data/target"
   )
   ```

2. **Scheduled Execution**:
   ```bash
   az databricks job create \
       --workspace-name your-workspace \
       --name "Scheduled Pipeline" \
       --existing-cluster-id your-cluster-id \
       --notebook-path "/Repos/your-repo/main" \
       --schedule "0 0 * * *"  # Daily at midnight
   ```

## Best Practices

1. **Security**:
   - Use Managed Identities for authentication
   - Implement proper access controls
   - Secure sensitive information in Key Vault

2. **Development**:
   - Use version control
   - Implement CI/CD pipelines
   - Write unit tests

3. **Monitoring**:
   - Set up alerts
   - Monitor cluster performance
   - Track job success/failure

## Troubleshooting

Common issues and solutions:
1. **Connection Issues**:
   - Verify network connectivity
   - Check access permissions
   - Validate credentials

2. **Performance Issues**:
   - Monitor cluster metrics
   - Optimize cluster configuration
   - Review code efficiency

## Contributing
1. Fork the repository
2. Create feature branch
3. Commit changes
4. Push to branch
5. Create Pull Request

## License
This project is licensed under the MIT License - see the LICENSE file for details.


