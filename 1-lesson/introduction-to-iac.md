# Lesson: "Introduction to Infrastructure as Code with Terraform"

---

## Lesson Overview :pencil2:

---

This lesson introduces the concept of Infrastructure as Code (IaC) and explores the fundamentals of using Terraform, an open-source tool for automating cloud infrastructure management. 

The focus will be on how Terraform helps to create, update, and manage infrastructure resources on Google Cloud Platform (GCP) using declarative configurations. 

Participants will gain hands-on experience writing basic Terraform configurations and understanding how Terraform manages infrastructure state.

## Learning Objectives :notebook:

---

By the end of this lesson, you will be able to:

- Understand the purpose and benefits of Infrastructure as Code (IaC) and Terraform in managing cloud infrastructure.
- Write basic Terraform configurations to define Google Cloud Platform (GCP) resources.
Understand Terraform state management and implement best practices for managing the infrastructure lifecycle.

## Key Definitions and Concepts :key:

---

1. **Infrastructure as Code (IaC):** A method to manage and provision infrastructure through code rather than manual processes. It allows for the automation of tasks like server provisioning, network configuration, and storage management.

2. **Declarative vs. Imperative:**

    - **Imperative approach:** You define the steps required to achieve the desired state.
    - **Declarative approach:** You define what the desired state is, and the tool (like Terraform) figures out the steps to reach that state.

3. **Idempotence:** Running the same IaC code multiple times results in the same outcome, ensuring infrastructure stability and predictability.

4. **Version Control:** Treating infrastructure definitions as code means they can be versioned and managed in a source control system (e.g., Git).

## Benefits of IaC:

- **Consistency and Repeatability:** Ensures that the same infrastructure can be deployed multiple times in different environments.
- **Scalability:** Automates the provisioning of infrastructure, enabling quicker scaling.
Collaboration: IaC allows teams to collaborate on infrastructure management, track changes, and ensure accountability.

## What is Terraform?

An open-source IaC tool developed by HashiCorp that uses a declarative configuration language (HCL) to define, create, and manage infrastructure across multiple cloud providers like GCP, AWS, and Azure.

### Benefits of Terraform:

- **Multi-Cloud Compatibility:** Terraform supports multiple cloud providers and services.
- **Modularity:** Reusable configurations, known as modules, allow for the separation of infrastructure components.
- **State Management:** Terraform keeps track of the current infrastructure state, ensuring changes are applied safely.
- **Execution Plans:** Before making any changes, Terraform creates an execution plan that shows what changes will be made to the infrastructure.

### Core Concepts of Terraform

**Providers:** Plugins that enable Terraform to interact with different cloud platforms (e.g., google provider for GCP).

**Resources:** The infrastructure objects (e.g., virtual machines, networks) that Terraform manages.

**Modules:** A way to organize and reuse configurations for multiple infrastructure components.

**State Management:** Terraform stores metadata about managed resources in a state file, allowing it to track resource changes and dependencies.

**Terraform Commands:**

- `terraform init`: Initializes the Terraform working directory.
- `terraform plan`: Generates an execution plan showing the changes Terraform will make.
- `terraform apply`: Applies the changes required to reach the desired infrastructure state.
- `terraform destroy`: Destroys the managed infrastructure, cleaning up resources.

## Installing and Setting Up Terraform

We will use Docker and Terraform to set up and experiment with Terraform configurations for managing GCP resources. The following steps outline the requirements and installation procedure:

### Requirements:

- Install Docker.
    - Install Docker Desktop if necessary.
- Create a free-tier GCP account and set up a project.
- Install the Google Cloud SDK for authentication and interaction with GCP services.

### Setting Up Terraform:

Pull the Terraform Docker image:

```cli
docker pull hashicorp/terraform:latest
```