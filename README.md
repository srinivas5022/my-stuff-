name: Terraform S3 Deployment

on:
  push:
    branches:
      - main  # Trigger workflow when changes are pushed to the 'main' branch

jobs:
  terraform-s3:
    runs-on: ubuntu-latest

    steps:
    # Step 1: Checkout the repository
    - name: Checkout Repository
      uses: actions/checkout@v3

    # Step 2: Set up Terraform
    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v2
      with:
        terraform_version: 1.5.0 # Adjust the version as needed

    # Step 3: Initialize Terraform
    - name: Terraform Init
      run: terraform init

    # Step 4: Apply Terraform (creates S3 bucket and configures it)
    - name: Terraform Apply
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      run: terraform apply -auto-approve

    # Step 5: Copy files to S3 using AWS CLI
    - name: Upload Files to S3
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
      run: |
        aws s3 cp ./files/ s3://your-s3-bucket-name --recursive
