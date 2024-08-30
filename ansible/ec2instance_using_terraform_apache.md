## To integrate Ansible with Terraform for creating a new EC2 instance on AWS, enabling SSH access for a specific public IP, allowing HTTP/HTTPS access on all IPs, installing NGINX, and deploying a sample page, follow these steps:

To automate the entire process, including running the Ansible playbook as part of the Terraform apply command, you can use a local-exec provisioner in Terraform. Below is the complete code, which includes Terraform configuration and Ansible playbook execution:

### main.tf
```
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"  # Use the appropriate AMI ID
  instance_type = "t2.micro"
  key_name      = var.key_name

  security_groups = [aws_security_group.web_sg.name]

  tags = {
    Name = "Ansible-Managed-Instance"
  }

  provisioner "local-exec" {
    command = <<EOT
      echo '[all]' > inventory.ini
      echo '${self.public_ip}' >> inventory.ini
    EOT
  }

  user_data = <<-EOF
              #!/bin/bash
              sudo apt-get update
              sudo apt-get install -y python3 python3-pip
              EOF
}

resource "aws_security_group" "web_sg" {
  name = "web_sg"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.ssh_ip_cidr]
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web_sg"
  }
}

resource "null_resource" "ansible_provision" {
  provisioner "local-exec" {
    command = "ansible-playbook -i inventory.ini playbook.yml --private-key /path/to/your/private-key.pem"
    environment = {
      ANSIBLE_HOST_KEY_CHECKING = "False"
    }
  }

  depends_on = [aws_instance.web]
}

output "web_public_ip" {
  value = aws_instance.web.public_ip
}

output "nginx_url" {
  value = "http://${aws_instance.web.public_ip}"
}

variable "key_name" {
  description = "Name of the SSH key pair"
  type        = string
}

variable "ssh_ip_cidr" {
  description = "CIDR block for SSH access"
  type        = string
}
```

### Variables file (terraform.tfvars):

```
key_name   = "your-key-name"
ssh_ip_cidr = "your-public-ip/32"  # Replace with your public IP
```

### playbook.yml:
```
- hosts: all
  become: yes
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Start and enable NGINX
      systemd:
        name: nginx
        enabled: yes
        state: started

    - name: Deploy a sample HTML page
      copy:
        dest: /var/www/html/index.html
        content: "<h1>Welcome to NGINX on Terraform-Ansible Managed Instance</h1>"
```

### To execute the configuration and playbook, run:
```
terraform init
terraform apply -auto-approve
```

### Once Terraform and Ansible have completed, you can verify the deployment by visiting the NGINX server using the outputted URL:
```
terraform output nginx_url
```

### Explanation
- aws_instance resource: Provisions the EC2 instance and sets up an SSH key and security group.
- aws_security_group resource: Manages the security rules for SSH, HTTP, and HTTPS access.
- null_resource with local-exec: Runs the Ansible playbook automatically after the EC2 instance is created. The playbook installs NGINX, starts the service, and deploys a sample web page.
- Outputs: Terraform outputs the public IP and NGINX URL after the resources are created.

This setup ensures that both the infrastructure provisioning and configuration management happen seamlessly in one go using Terraform and Ansible.
