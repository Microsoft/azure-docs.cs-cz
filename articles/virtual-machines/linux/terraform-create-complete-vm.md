---
title: K vytvoření kompletní virtuální počítač s Linuxem v Azure použijte Terraform | Microsoft Docs
description: Další informace o použití Terraform k vytváření a správě dokončení prostředí Linux virtuálního počítače v Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2017
ms.author: echuvyrov
ms.openlocfilehash: 6b2dc2e8859efdcc57c45831381bc1870495ecf6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Vytvořte v Azure s Terraform kompletní infrastrukturu virtuálních počítačů Linux

Terraform umožňuje definovat a vytvořit nasazení kompletní infrastrukturu v Azure. Můžete vytvořit šablony Terraform ve formátu čitelném, které vytvoření a konfigurace prostředků Azure konzistentní a reprodukovatelné způsobem. Tento článek ukazuje, jak vytvořit úplný prostředí Linux a podpůrné prostředky s Terraform. Můžete si také přečíst postup [nainstalovat a nakonfigurovat Terraform](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Vytvoření připojení Azure a skupiny prostředků

Přejděte prostřednictvím každá část Terraform šablony. Můžete také zjistit plnou verzi [Terraform šablony](#complete-terraform-script) , kterou můžete zkopírovat a vložit.

`provider` Části je uvedeno, Terraform používat zprostředkovatele služby Azure. Chcete-li získat hodnoty pro *ID_ODBĚRU*, *client_id*, *tajný klíč client_secret*, a *tenant_id*, najdete v části [nainstalovat a Konfigurace Terraform](terraform-install-configure.md). 

> [!TIP]
> Pokud vytvoříte proměnné prostředí pro hodnoty nebo používají [prostředí Azure Cloud prostředí Bash](/azure/cloud-shell/overview) , nemusíte zahrnovat deklarace proměnných v této části.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

V následující části vytvoří skupinu prostředků s názvem `myResourceGroup` v `eastus` umístění:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

V dalších částech odkazujete skupiny prostředků se *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
V následující části vytvoří virtuální síť s názvem *myVnet* v *10.0.0.0/16* adresní prostor:

```tf
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}
```
: V následující části vytvoří podsíť s názvem *mySubnet* v *myVnet* virtuální sítě

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Vytvoření veřejné IP adresy
Pro přístup k prostředkům přes Internet, vytvořte a přiřaďte veřejnou IP adresu pro virtuální počítač. V následující části vytvoří veřejnou IP adresu s názvem *myPublicIP*:

```tf
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Skupiny zabezpečení sítě řídí tok síťový provoz do/z virtuálního počítače. V následující části vytvoří skupinu zabezpečení sítě s názvem *myNetworkSecurityGroup* a definuje pravidlo umožňující přenos SSH na TCP port 22:

```tf
resource "azurerm_network_security_group" "temyterraformpublicipnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Vytvořit virtuální síťová karta
Virtuální síťová karta (NIC) připojí k dané virtuální síti, veřejnou IP adresu a skupinu zabezpečení sítě virtuálního počítače. V následující části v šabloně Terraform vytvoří virtuální síťový adaptér s názvem *myNIC* připojené k virtuální síťové prostředky, které jste vytvořili:

```tf
resource "azurerm_network_interface" "myterraformnic" {
    name                = "myNIC"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-storage-account-for-diagnostics"></a>Vytvořit účet úložiště pro diagnostiku
Pro uložení Diagnostika spouštění pro virtuální počítač, potřebujete účet úložiště. Tato Diagnostika spouštění může pomoci při řešení problémů a monitorovat stav virtuálního počítače. Nově vytvořený účet úložiště je pouze pro ukládání dat diagnostiky spouštění. Protože každý účet úložiště musí mít jedinečný název, v následující části generuje některé náhodného textu:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Nyní můžete vytvořit účet úložiště. V následující části vytvoří účet úložiště s názvem podle náhodného textu v předchozím kroku:

```tf
resource "azurerm_storage_account" "mystorageaccount" {
    name                = "diag${random_id.randomId.hex}"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    location            = "eastus"
    account_replication_type = "LRS"
    account_tier = "Standard"

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Posledním krokem je vytvoření virtuálního počítače a použít všechny prostředky, které jsou vytvořené. V následující části vytvoří virtuální počítač s názvem *Můjvp* a připojí virtuální síťový adaptér s názvem *myNIC*. Nejnovější *Ubuntu 16.04-LTS* image používá, a uživatel s názvem *azureuser* je vytvořena s zakázáno ověřování hesla.

 Je součástí dat klíče SSH *ssh_keys* části. Zadejte platný veřejný klíč SSH v *key_data* pole.

```tf
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled     = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Dokončení Terraform skriptu

Tyto části společně nabídnou a zobrazit Terraform v akci, vytvořte soubor s názvem *terraform_azure.tf* a vložte následující obsah:

```tf
variable "resourcename" {
  default = "myResourceGroup"
}

# Configure the Microsoft Azure Provider
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn’t exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"

    tags {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = "${azurerm_resource_group.myterraformgroup.name}"
    public_ip_address_allocation = "dynamic"

    tags {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = "${azurerm_resource_group.myterraformgroup.name}"
    
    security_rule {
        name                       = "SSH"
        priority                   = 1001
        direction                  = "Inbound"
        access                     = "Allow"
        protocol                   = "Tcp"
        source_port_range          = "*"
        destination_port_range     = "22"
        source_address_prefix      = "*"
        destination_address_prefix = "*"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = "${azurerm_resource_group.myterraformgroup.name}"
    network_security_group_id = "${azurerm_network_security_group.myterraformnsg.id}"

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags {
        environment = "Terraform Demo"
    }
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = "${azurerm_resource_group.myterraformgroup.name}"
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = "${azurerm_resource_group.myterraformgroup.name}"
    network_interface_ids = ["${azurerm_network_interface.myterraformnic.id}"]
    vm_size               = "Standard_DS1_v2"

    storage_os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Premium_LRS"
    }

    storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    os_profile {
        computer_name  = "myvm"
        admin_username = "azureuser"
    }

    os_profile_linux_config {
        disable_password_authentication = true
        ssh_keys {
            path     = "/home/azureuser/.ssh/authorized_keys"
            key_data = "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
        }
    }

    boot_diagnostics {
        enabled = "true"
        storage_uri = "${azurerm_storage_account.mystorageaccount.primary_blob_endpoint}"
    }

    tags {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Sestavení a nasazení infrastruktury
S Terraform šablony vytvořit je prvním krokem k chybě při inicializaci Terraform. Tento krok zajistí, že Terraform má všechny předpoklady k vytvoření šablony v Azure.

```bash
terraform init
```

Dalším krokem je tak, aby měl Terraform zkontrolovat a ověřit šablony. Tento krok porovná požadované prostředky pro informace o stavu ukládaná Terraform a pak výstupy plánované provádění. Prostředky jsou *není* vytvoří v Azure.

```bash
terraform plan
```

Po provedení předchozí příkaz byste měli vidět něco podobného jako následující obrazovka:

```bash
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn’t specify an “-out” parameter to save this plan, so when
“apply” is called, Terraform can’t guarantee this is what will execute.
  + azurerm_resource_group.myterraform
      <snip>
  + azurerm_virtual_network.myterraformnetwork
      <snip>
  + azurerm_network_interface.myterraformnic
      <snip>
  + azurerm_network_security_group.myterraformnsg
      <snip>
  + azurerm_public_ip.myterraformpublicip
      <snip>
  + azurerm_subnet.myterraformsubnet
      <snip>
  + azurerm_virtual_machine.myterraformvm
      <snip>
Plan: 7 to add, 0 to change, 0 to destroy.
```

Pokud všechno, co vypadá v pořádku a budete chtít vytvořit infrastrukturu v Azure, použijte šablonu v Terraform:

```bash
terraform apply
```

Po dokončení Terraform infrastrukturu virtuálních počítačů je připraven. Získat veřejnou IP adresu vašeho virtuálního počítače s [az virtuálních počítačů zobrazit](/cli/azure/vm#az_vm_show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Pak můžete SSH pro virtuální počítač:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Další postup
Základní infrastruktura jste vytvořili v Azure pomocí Terraform. Složitější scénáře, včetně příklady, které pomocí služby Vyrovnávání zatížení a virtuální počítač škálování sad, najdete v tématu množství [Terraform příklady Azure](https://github.com/hashicorp/terraform/tree/master/examples). Aktuální seznam podporovaných zprostředkovatelů Azure, najdete v článku [Terraform dokumentaci](https://www.terraform.io/docs/providers/azurerm/index.html).
