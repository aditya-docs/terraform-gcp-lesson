![logo_ironhack_blue 7](https://user-images.githubusercontent.com/23629340/40541063-a07a0a8a-601a-11e8-91b5-2f13e4e6b441.png)

# LAB | Deploy a Static Website on GCP with CDN and DNS

## Introduction

In this lab, you'll learn how to deploy a simple static website, "Meet for Coffee" using Google Cloud Storage for hosting, Google Cloud CDN for caching, and Google Cloud DNS to configure a custom domain. By the end of this lab, you will have a fully functioning website accessible via HTTPS, optimized for speed with CDN, and routed through DNS.

## Requirements

1. Install the following tools:
   - [Terraform](https://www.terraform.io/downloads.html)
   - [Google Cloud SDK](https://cloud.google.com/sdk/docs/install)
   - [Docker Desktop](https://www.docker.com/products/docker-desktop)

2. Make sure you have a Google Cloud project set up and billing enabled.

3. Clone this repo:

```bash
git clone <repo-url>
cd <repo-directory>
```

4. Ensure that you have a domain name that is managed via GCP Cloud DNS. If you don’t have a domain, you can purchase one (e.g., via Google Domains, GoDaddy, etc.) and configure it in GCP.

5. Configure the Google Cloud CLI using `project-id` from step 2:

```cli
gcloud auth login
gcloud config set project <your-project-id>
```

## Submission

Once you finish the assignment, submit a URL link to your 'Coffee website' in the field on Student Portal.

## Instructions

[🚨 to be removed, only for SME eyes🚨 - start]
- Ensure students have set up GCP credentials and are comfortable with using Terraform and Google Cloud services.
- If students face issues, refer them to the Terraform documentation and GCP services.
[🚨 to be removed, only for SME eyes🚨 - end]

In this lab, you'll go step-by-step to deploy the "Meet Aditya for Coffee" website. You'll be working with Terraform to automate the infrastructure and services on GCP.

## Tasks

Create a Terraform configuration file `main.tf` in the `lab-starter` folder. The following tasks have to be performed in the `main.tf` file.

### Task 1: Setup Google Cloud Storage for Static Website
   
1. Write the following resource in your Terraform file to create the storage bucket:
   
```hcl
resource "google_storage_bucket" "website" {
    provider = google
    name     = "example-aditya-coffee"
    location = "US"
}
```

2. Upload your index.html file (which contains the "Meet for Coffee" website) to the Cloud Storage bucket:

```hcl
resource "google_storage_bucket_object" "static_site_src" {
  name   = "index.html"
  source = "../website/index.html"
  bucket = google_storage_bucket.website.name
}
```

3. Set permissions so that the static website can be publicly accessible:

```hcl
resource "google_storage_object_access_control" "public_rule" {
  object = google_storage_bucket_object.static_site_src.output_name
  bucket = google_storage_bucket.website.name
  role   = "READER"
  entity = "allUsers"
}
```

### Task 2: Set Up Google Cloud DNS and Reserve an External IP

1. Reserve a global external IP address to make your site publicly accessible:

```hcl
resource "google_compute_global_address" "website" {
  provider = google
  name = "website-lb-ip"
}
```

2. Add the external IP to your domain's DNS record using Cloud DNS. You will need an existing managed DNS zone:

```hcl
data "google_dns_managed_zone" "gcp_coffeetime_dev" {
  provider = google
  name = "aditya-example"
}

resource "google_dns_record_set" "website" {
  name         = "website.${data.google_dns_managed_zone.gcp_coffeetime_dev.dns_name}"
  type         = "A"
  ttl          = 300
  managed_zone = data.google_dns_managed_zone.gcp_coffeetime_dev.name
  rrdatas      = [google_compute_global_address.website.address]
}
```

### Task 3: Enable Cloud CDN for Faster Website Delivery

Set up a CDN-backed bucket to cache and deliver your website's content quickly:

```hcl
resource "google_compute_backend_bucket" "website-backend" {
  provider    = google
  name        = "website-backend"
  description = "Contains files needed by the website"
  bucket_name = google_storage_bucket.website.name
  enable_cdn  = true
}
```

### Task 4: Configure HTTPS with a Managed SSL Certificate

1. Create an HTTPS certificate for your domain:

```hcl
resource "google_compute_managed_ssl_certificate" "website" {
  provider = google-beta
  name = "website-cert"
  managed {
    domains = [google_dns_record_set.website.name]
  }
}
```

2. Set up the GCP URL map and target proxy to route traffic to your CDN backend via HTTPS:

```hcl
resource "google_compute_url_map" "website" {
  provider        = google
  name            = "website-url-map"
  default_service = google_compute_backend_bucket.website-backend.self_link

  host_rule {
    hosts        = ["*"]
    path_matcher = "allpaths"
  }

  path_matcher {
    name            = "allpaths"
    default_service = google_compute_backend_bucket.website-backend.self_link
  }
}

resource "google_compute_target_https_proxy" "website" {
  provider         = google
  name             = "website-target-proxy"
  url_map          = google_compute_url_map.website.self_link
  ssl_certificates = [google_compute_managed_ssl_certificate.website.self_link]
}
```

### Task 5: Set Up Load Balancing and Forwarding Rules

Set up a forwarding rule to route traffic to your website:

```hcl
resource "google_compute_global_forwarding_rule" "default" {
  provider              = google
  name                  = "website-forwarding-rule"
  load_balancing_scheme = "EXTERNAL"
  ip_address            = google_compute_global_address.website.address
  ip_protocol           = "TCP"
  port_range            = "443"
  target                = google_compute_target_https_proxy.website.self_link
}
```

### Task 6: Configure the variables (terraform.tfvars)

Your main.tf file is ready, now configure the `terraform.tfvars` fil with the values for `key-file-path`, `project-id`, and `gcp-region`:

```hcl
# Path to the service account JSON key file
gcp_svc_key = "/path/to/your/service-account-key.json"

# GCP Project ID
gcp_project = "your-gcp-project-id"

# GCP Region (example: us-central1)
gcp_region = "your-gcp-region"
```

### Task 7: Update the wesite (index.html)

Update the index.html file in the website folder to have your name, github, linkedin and instagram links.

### Task 8: Deploy

Deploy your Terraform configuration:

```cli
terraform init
terraform apply
```

Once your infrastructure is deployed, check the website by accessing the custom domain in your browser!

Remember to submit the URL.