// Non-Compliant - AWS API Gateway


resource "aws_api_gateway_domain_name" "example" {
  domain_name     = "api.example.com"
  security_policy = "TLS_1_0"
}


// Non-Compliant AWS open search
resource "aws_elasticsearch_domain" "example" {
  domain_name = "example"
  domain_endpoint_options {
    enforce_https       = true
    tls_security_policy = "Policy-Min-TLS-1-0-2019-07"
  }
}





// Compliant - AWS API
resource "aws_api_gateway_domain_name" "example" {
  domain_name     = "api.example.com"
  security_policy = "TLS_1_2"
}

//Compliant  -- AWS Open Search

resource "aws_elasticsearch_domain" "example" {
  domain_name = "example"
  domain_endpoint_options {
    enforce_https       = true
    tls_security_policy = "Policy-Min-TLS-1-2-2019-07"
  }
}


//Policies authorizing public access to resources are security-sensitive

// non- compliant

resource "aws_s3_bucket_policy" "mynoncompliantpolicy" { # Sensitive
  bucket = aws_s3_bucket.mybucket.id
  policy = jsonencode({
    Id      = "mynoncompliantpolicy"
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        AWS = "*"
      }
      Action = [
        "s3:PutObject"
      ]
      Resource : "${aws_s3_bucket.mybucket.arn}/*"
      }
    ]
  })
}

// compliant

resource "aws_s3_bucket_policy" "mycompliantpolicy" {
  bucket = aws_s3_bucket.mybucket.id
  policy = jsonencode({
    Id      = "mycompliantpolicy"
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Principal = {
        AWS = [
          "arn:aws:iam::${data.aws_caller_identity.current.account_id}:root"
        ]
      }
      Action = [
        "s3:PutObject"
      ]
      Resource = "${aws_s3_bucket.mybucket.arn}/*"
      }
    ]
  })
}

//Administration services access should be restricted to specific IP addresses

// non- compliant 

resource "aws_security_group" "noncompliant" {
  name        = "allow_ssh_noncompliant"
  description = "allow_ssh_noncompliant"
  vpc_id      = aws_vpc.main.id

  ingress {
    description = "SSH rule"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"] # Noncompliant
  }
}



// complaiant 
resource "aws_security_group" "compliant" {
  name        = "allow_ssh_compliant"
  description = "allow_ssh_compliant"
  vpc_id      = aws_vpc.main.id

  ingress {
    description = "SSH rule"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["1.2.3.0/24"]
  }
}



//non - compliant 

resource "azurerm_app_service" "example" {
  name                = "example-app-service"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  app_service_plan_id = azurerm_app_service_plan.example.id

  logs {
    application_logs {
      file_system_level = "Off"
      azure_blob_storage {
        level = "Off"
        sas_url = azurerm_storage_account.example.primary_blob_endpoint
        retention_in_days = 7
      }
    }
  }
}

// compliant

resource "azurerm_app_service" "example" {
  name                = "example-app-service"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  app_service_plan_id = azurerm_app_service_plan.example.id

  logs {
    http_logs {
      file_system {
        retention_in_days = 90  # Retain logs for 90 days
        retention_in_mb   = 100 # Limit log storage to 100 MB
      }
    }

    application_logs {
      file_system_level = "Error" # Log only errors to the file system

      azure_blob_storage {
        sas_url           = azurerm_storage_account.example.primary_blob_endpoint # SAS URL required for Blob Storage
        retention_in_days = 90     # Retain logs for 90 days
        level             = "Error" # Log only errors to Blob Storage
      }
    }
  }
}

//Disabling S3 bucket MFA delete is security-sensitive

// non - compliant 


//A versioned S3 bucket does not have MFA delete enabled for AWS provider version 3 or below

resource "aws_s3_bucket" "example" { # Sensitive
  bucket = "example"

  versioning {
    enabled = true
  }
}

//A versioned S3 bucket does not have MFA delete enabled for AWS provider version 4 or above:

resource "aws_s3_bucket" "example" {
  bucket = "example"
}

resource "aws_s3_bucket_versioning" "example" { # Sensitive
  bucket = aws_s3_bucket.example.id
  versioning_configuration {
    status = "Enabled"
  }
}

// compliant solution

//A versioned S3 bucket does not have MFA delete enabled for AWS provider version 3 or below

resource "aws_s3_bucket" "example" {
  bucket = "example"

  versioning {
    enabled = true
    mfa_delete = true
  }
}


//A versioned S3 bucket does not have MFA delete enabled for AWS provider version 4 or above:


resource "aws_s3_bucket" "example" {
  bucket = "example"
}

resource "aws_s3_bucket_versioning" "example" {
  bucket = aws_s3_bucket.example.id
  versioning_configuration {
    status = "Enabled"
    mfa_delete = "Enabled"
  }
  mfa = "${var.MFA}"
}
