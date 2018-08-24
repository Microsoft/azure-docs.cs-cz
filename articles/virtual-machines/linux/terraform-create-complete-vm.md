---
title: Použití Terraformu k vytvoření kompletní virtuální počítač s Linuxem v Azure | Dokumentace Microsoftu
description: Další informace o použití Terraformu k vytvoření a správě kompletního prostředí pro virtuální počítač Linux v Azure
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
ms.openlocfilehash: dfebda8f92837f8573fb3362c9210bce9b70d23d
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42748040"
---
# <a name="create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Vytvoření kompletní infrastrukturu virtuálních počítačů Linux v Azure s využitím Terraformu

Terraform umožňuje definovat a vytvářet kompletní infrastrukturu nasazení v Azure. Můžete vytvářet šablony Terraformu v lidsky čitelném formátu, vytváření a konfiguraci prostředků Azure v podobě konzistentní vzhledem k aplikacím a reprodukovatelné. Tento článek popisuje, jak k vytvoření kompletního linuxového prostředí a podpůrné prostředky s využitím Terraformu. Můžete také zjistíte, jak [instalace a konfigurace Terraformu](terraform-install-configure.md).


## <a name="create-azure-connection-and-resource-group"></a>Vytvoření připojení Azure a skupiny prostředků

Pojďme si jednotlivé části Terraformu šablony. Můžete také zobrazit úplnou verzi [Terraformu šablony](#complete-terraform-script) , který můžete zkopírovat a vložit.

`provider` Části říká Terraformu pro Azure poskytovatel se použije. K získání hodnot pro *subscription_id*, *client_id*, *hodnota client_secret*, a *tenant_id*, naleznete v tématu [nainstalovat a Nakonfigurujte Terraform](terraform-install-configure.md). 

> [!TIP]
> Pokud vytvoříte proměnné prostředí pro hodnoty, nebo používáte [prostředí Azure Cloud Shell Bash](/azure/cloud-shell/overview) , není nutné zahrnout deklarace proměnných v této části.

```tf
provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Následující části se vytvoří skupina prostředků s názvem `myResourceGroup` v `eastus` umístění:

```tf
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags {
        environment = "Terraform Demo"
    }
}
```

V další části, můžete odkazovat na skupinu prostředků s *${azurerm_resource_group.myterraformgroup.name}*.

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Následující části se vytvoří virtuální síť s názvem *myVnet* v *10.0.0.0/16* adresní prostor:

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

Následující části se vytvoří podsíť s názvem *mySubnet* v *myVnet* virtuální sítě:

```tf
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = "${azurerm_resource_group.myterraformgroup.name}"
    virtual_network_name = "${azurerm_virtual_network.myterraformnetwork.name}"
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Vytvoření veřejné IP adresy
Pro přístup k prostředkům v síti Internet, vytvořit a přiřadit veřejnou IP adresu vašeho virtuálního počítače. Následující části se vytvoří veřejnou IP adresu s názvem *myPublicIP*:

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
Skupiny zabezpečení sítě řízení toku síťového provozu do a z virtuálního počítače. Následující části se vytvoří skupina zabezpečení sítě s názvem *myNetworkSecurityGroup* a definuje pravidlo, které umožní provoz SSH na portu TCP 22:

```tf
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
```


## <a name="create-virtual-network-interface-card"></a>Vytvořit virtuální síťové karty
Virtuální síťové karty (NIC) připojí k dané virtuální sítě, veřejná IP adresa a skupiny zabezpečení sítě virtuálního počítače. Následující části v šabloně Terraformu se vytvoří virtuální síťovou kartu s názvem *myNIC* připojené k virtuální síťové prostředky, které jste vytvořili:

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


## <a name="create-storage-account-for-diagnostics"></a>Vytvoření účtu úložiště pro diagnostiku
Pro ukládání diagnostiky spouštění pro virtuální počítač, budete potřebovat účet úložiště. Tyto nástroje pro diagnostiku spouštění můžete řešit problémy a monitorovat stav virtuálního počítače. Nově vytvořený účet úložiště je pouze pro ukládání dat diagnostiky spouštění. Jako každý účet úložiště musí mít jedinečný název, následující část vygeneruje náhodný text:

```tf
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = "${azurerm_resource_group.myterraformgroup.name}"
    }
    
    byte_length = 8
}
```

Nyní můžete vytvořit účet úložiště. Následující části se vytvoří účet úložiště s názvem podle náhodného textu vygenerované v předchozím kroku:

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

Posledním krokem je vytvoření virtuálního počítače a použít všechny prostředky vytvořené. Následující části se vytvoří virtuální počítač s názvem *myVM* a připojí virtuální síťovou kartu s názvem *myNIC*. Nejnovější *Ubuntu 16.04-LTS* image používá, a uživateli se jménem *azureuser* se vytvoří s zakázané ověřování pomocí hesla.

 Nejsou k dispozici data klíče SSH v *ssh_keys* oddílu. Zadejte platný veřejný klíč SSH v *key_data* pole.

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

## <a name="complete-terraform-script"></a>Dokončení skriptu Terraformu

Všechny tyto části pohromadě a prohlédněte si Terraformu v akci, vytvořte soubor s názvem *terraform_azure.tf* a vložte následující obsah:

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


## <a name="build-and-deploy-the-infrastructure"></a>Vytvoření a nasazení infrastruktury
Pomocí Terraformu šablony vytvoří prvním krokem je inicializace Terraformu. Tento krok zajistí, že Terraformu obsahuje vše potřebné pro vytvoření šablony v Azure.

```bash
terraform init
```

Dalším krokem je, aby Terraformu kontrola a ověření šablony. Tento krok porovnává požadované prostředky, které informace o stavu uložené Terraform a potom vypíše plánovaného spuštění. Prostředky jsou *není* vytvořené v Azure.

```bash
terraform plan
```

Po spuštění předchozího příkazu by měl vypadat přibližně jako na následujícím obrázku:

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

Pokud vše vypadá v pořádku a jste připraveni vytvořit infrastrukturu v Azure, použijte šablonu v Terraformu:

```bash
terraform apply
```

Po dokončení Terraformu infrastruktury virtuálních počítačů je připraven. Získat veřejnou IP adresu vašeho virtuálního počítače s [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Pak můžete SSH k virtuálnímu počítači:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Další postup
V Azure jste vytvořili základní infrastruktura pomocí Terraformu. Pro složitější scénáře, včetně příklady použití nástroje pro vyrovnávání zatížení a virtuální počítače škálovací sady, najdete v tématu mnoho [příklady Terraformu pro Azure](https://github.com/hashicorp/terraform/tree/master/examples). Aktuální seznam podporovaných zprostředkovatelů Azure, najdete v článku [Terraformu dokumentaci](https://www.terraform.io/docs/providers/azurerm/index.html).
