# Introducción

Terraform es una herramienta de configuración de software diseñada para potenciar la automatización de múltiples procesos a través de conceptos como el de infrastructure as code. A través de su lenguaje permite crear definiciones (llamadas resources) de objetos o recursos de infraestructura.

Su principal objetivo es facilitar la creación de infraestructura de manera declarativa, por ejemplo un centro de datos en servicios como Azure, Amazon Web Services (AWS) o Google Cloud Platform (GCP). Es decir, esta configuración se almacena en la [nube](https://www.plainconcepts.com/es/cloud-computing/) y permite automatizar una infraestructura.

# Instalación 

Para poder instalar docker en una máquina Linux ejecutamos el siguiente código:
```sh
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

Para instalarlo en windows es algo más complejo; seguir los pasos de la documentación oficial que se adjunta.
https://developer.hashicorp.com/terraform/install?product_intent=terraform

# Providers

Terraform utiliza proveedores para poder ejecutar sus servicios. Para poder ver estos proveedores se consulta la documentación oficial de Terraform ya que cada proveedor utiliza sentencias distintas.

Providers (https://registry.terraform.io/browse/providers)

```sh
#Proveedor de azure
terraform {
  required_providers {
    azurerm = {
      source = "hashicorp/azurerm"
      version = "3.80.0"
    }
  }
}

provider "azurerm" {
  features{}
}

```

# Primeros pasos

Una vez están listos los proveedores lo siguiente es empezar a levantar los recursos que queremos tener. Para ellos adjunto codigo con ciertos recursos que usamos.
Igualmente toda la documentacion y codigo más especifico se encuentra en el sitio oficial de terraform
Página con la documentación para levantar recursos con el proveedor de Azure (https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)

```sh
#Script de terraform para crear una red virtual
 
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}
 
resource "azurerm_network_security_group" "example" {
  name                = "example-security-group"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}
 
resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
  dns_servers         = ["10.0.0.4", "10.0.0.5"]
 
  subnet {
    name           = "subnet1"
    address_prefix = "10.0.1.0/24"
  }
 
  subnet {
    name           = "subnet2"
    address_prefix = "10.0.2.0/24"
    security_group = azurerm_network_security_group.example.id
  }
 
  tags = {
    environment = "Production"
  }
}
```

```sh
#Script para crear una maquina virtual linux
 
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}
 
resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}
 
resource "azurerm_subnet" "example" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.2.0/24"]
}
 
resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
 
  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}
 
resource "azurerm_linux_virtual_machine" "example" {
  name                = "example-machine"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  size                = "Standard_F2"
  admin_username      = "adminuser"
  network_interface_ids = [
    azurerm_network_interface.example.id,
  ]
 
  admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }
 
  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
 
  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
}
```