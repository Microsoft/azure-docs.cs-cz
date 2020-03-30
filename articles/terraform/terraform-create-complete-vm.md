---
title: Úvodní příručka – pomocí Terraformu vytvořte kompletní virtuální počítač s Linuxem v Azure.
description: V tomto rychlém startu použijete Terraform k vytvoření a správě kompletního prostředí virtuálních strojů Linux v Azure.
keywords: azure devops terraform linux vm virtuální počítač
ms.topic: quickstart
ms.date: 03/15/2020
ms.openlocfilehash: f262734cc16d97e4d73af371410403a4cbb8815e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79415457"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Úvodní příručka: Vytvoření kompletní infrastruktury virtuálních strojů Linuxu v Azure pomocí Terraformu

Terraform umožňuje definovat a vytvářet kompletní nasazení infrastruktury v Azure. Šablony Terraform vytváříte v formátu čitelném pro člověka, který vytváří a konfiguruje prostředky Azure konzistentním a reprodukovatelným způsobem. Tento článek ukazuje, jak vytvořit kompletní linuxové prostředí a podpůrné prostředky s Terraform. Můžete se také dozvědět, jak [nainstalovat a nakonfigurovat Terraform](terraform-install-configure.md).

> [!NOTE]
> Pro podporu specifickou pro Terraform se prosím obraťte na Terraform přímo pomocí jednoho z jejich komunitních kanálů:
>
>    * [Část Terraform](https://discuss.hashicorp.com/c/terraform-core) na komunitním portálu obsahuje otázky, případy použití a užitečné vzory.
>
>    * Otázky týkající se poskytovatele naleznete v části [Terraform Providers](https://discuss.hashicorp.com/c/terraform-providers) na komunitním portálu.


## <a name="create-azure-connection-and-resource-group"></a>Vytvoření připojení Azure a skupiny prostředků

Projděme si každou část šablony Terraform. Můžete také zobrazit plnou verzi [šablony Terraform,](#complete-terraform-script) kterou můžete zkopírovat a vložit.

V `provider` části říká Terraform používat poskytovatele Azure. Informace o `subscription_id`získání `client_id` `client_secret`hodnot `tenant_id`pro aplikace , , a , naleznete [v tématu Instalace a konfigurace terraformu](terraform-install-configure.md). 

> [!TIP]
> Pokud vytvoříte proměnné prostředí pro hodnoty nebo používáte [prostředí Azure Cloud Shell Bash](/azure/cloud-shell/overview) , nemusíte zahrnout deklarace proměnných v této části.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

Následující část vytvoří skupinu `myResourceGroup` prostředků `eastus` s názvem v umístění:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

V dalších částech odkazujete `${azurerm_resource_group.myterraformgroup.name}`na skupinu prostředků pomocí .

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
Následující část vytvoří virtuální `myVnet` síť `10.0.0.0/16` s názvem v adresním prostoru:

```hcl
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}
```

Následující část vytvoří podsíť `mySubnet` pojmenovanou ve `myVnet` virtuální síti:

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Vytvořit veřejnou IP adresu
Chcete-li získat přístup k prostředkům přes Internet, vytvořte a přiřaďte virtuálnímu počítači veřejnou IP adresu. V následující části se vytvoří `myPublicIP`veřejná IP adresa s názvem :

```hcl
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-network-security-group"></a>Vytvořit skupinu zabezpečení sítě
Skupiny zabezpečení sítě řídí tok síťového provozu do a z virtuálního počítače. Následující část vytvoří skupinu `myNetworkSecurityGroup` zabezpečení sítě s názvem a definuje pravidlo umožňující přenos SSH na portu TCP 22:

```hcl
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-network-interface-card"></a>Vytvoření karty rozhraní virtuální sítě
Karta rozhraní virtuální sítě (NIC) propojuje váš virtuální počítač s danou virtuální sítí, veřejnou IP adresou a skupinou zabezpečení sítě. Následující část v šabloně Terraform vytvoří `myNIC` virtuální síťovou síťovou síť pojmenovanou připojenou k prostředkům virtuální sítě, které jste vytvořili:

```hcl
resource "azurerm_network_interface" "myterraformnic" {
    name                        = "myNIC"
    location                    = "eastus"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = "${azurerm_subnet.myterraformsubnet.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.myterraformpublicip.id}"
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}
```


## <a name="create-storage-account-for-diagnostics"></a>Vytvořit účet úložiště pro diagnostiku
Chcete-li uložit diagnostiku spouštění pro virtuální hod, potřebujete účet úložiště. Tato diagnostika spouštění vám pomůže vyřešit problémy a sledovat stav virtuálního počítače. Účet úložiště, který vytvoříte, je pouze pro uložení dat diagnostiky spouštění. Jako každý účet úložiště musí mít jedinečný název, v následující části generuje některé náhodný text:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Teď si můžete vytvořit účet úložiště. Následující část vytvoří účet úložiště s názvem založeným na náhodném textu generovaném v předchozím kroku:

```hcl
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_replication_type    = "LRS"
    account_tier                = "Standard"

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

Posledním krokem je vytvoření virtuálního virtuálního soudu a využití všech vytvořených prostředků. Následující část vytvoří virtuální `myVM` počítač s názvem a `myNIC`připojí virtuální nic s názvem . Používá `Ubuntu 16.04-LTS` se nejnovější obrázek a `azureuser` uživatel s názvem je vytvořen se zakázaným ověřováním hesla.

 Klíčové údaje SSH jsou `ssh_keys` uvedeny v sekci. Zadejte veřejný klíč SSH v `key_data` poli.

```hcl
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```

## <a name="complete-terraform-script"></a>Kompletní skript Terraform

Chcete-li spojit všechny tyto oddíly a zobrazit `terraform_azure.tf` Terraform v akci, vytvořte soubor s názvem a vložte následující obsah:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you're using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}

    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}

# Create a resource group if it doesn't exist
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual network
resource "azurerm_virtual_network" "myterraformnetwork" {
    name                = "myVnet"
    address_space       = ["10.0.0.0/16"]
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name

    tags = {
        environment = "Terraform Demo"
    }
}

# Create subnet
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.1.0/24"
}

# Create public IPs
resource "azurerm_public_ip" "myterraformpublicip" {
    name                         = "myPublicIP"
    location                     = "eastus"
    resource_group_name          = azurerm_resource_group.myterraformgroup.name
    allocation_method            = "Dynamic"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create Network Security Group and rule
resource "azurerm_network_security_group" "myterraformnsg" {
    name                = "myNetworkSecurityGroup"
    location            = "eastus"
    resource_group_name = azurerm_resource_group.myterraformgroup.name
    
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

    tags = {
        environment = "Terraform Demo"
    }
}

# Create network interface
resource "azurerm_network_interface" "myterraformnic" {
    name                      = "myNIC"
    location                  = "eastus"
    resource_group_name       = azurerm_resource_group.myterraformgroup.name

    ip_configuration {
        name                          = "myNicConfiguration"
        subnet_id                     = azurerm_subnet.myterraformsubnet.id
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = azurerm_public_ip.myterraformpublicip.id
    }

    tags = {
        environment = "Terraform Demo"
    }
}

# Connect the security group to the network interface
resource "azurerm_network_interface_security_group_association" "example" {
    network_interface_id      = azurerm_network_interface.myterraformnic.id
    network_security_group_id = azurerm_network_security_group.myterraformnsg.id
}

# Generate random text for a unique storage account name
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}

# Create storage account for boot diagnostics
resource "azurerm_storage_account" "mystorageaccount" {
    name                        = "diag${random_id.randomId.hex}"
    resource_group_name         = azurerm_resource_group.myterraformgroup.name
    location                    = "eastus"
    account_tier                = "Standard"
    account_replication_type    = "LRS"

    tags = {
        environment = "Terraform Demo"
    }
}

# Create virtual machine
resource "azurerm_linux_virtual_machine" "myterraformvm" {
    name                  = "myVM"
    location              = "eastus"
    resource_group_name   = azurerm_resource_group.myterraformgroup.name
    network_interface_ids = [azurerm_network_interface.myterraformnic.id]
    size                  = "Standard_DS1_v2"

    os_disk {
        name              = "myOsDisk"
        caching           = "ReadWrite"
        storage_account_type = "Premium_LRS"
    }

    source_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04.0-LTS"
        version   = "latest"
    }

    computer_name  = "myvm"
    admin_username = "azureuser"
    disable_password_authentication = true
        
    admin_ssh_key {
        username       = "azureuser"
        public_key     = file("/home/azureuser/.ssh/authorized_keys")
    }

    boot_diagnostics {
        storage_account_uri = azurerm_storage_account.mystorageaccount.primary_blob_endpoint
    }

    tags = {
        environment = "Terraform Demo"
    }
}
```


## <a name="build-and-deploy-the-infrastructure"></a>Vytváření a nasazování infrastruktury
Po vytvoření šablony Terraform je prvním krokem inicializaci Terraformu. Tento krok zajišťuje, že Terraform má všechny předpoklady k sestavení šablony v Azure.

```bash
terraform init
```

Dalším krokem je terraform zkontrolovat a ověřit šablonu. Tento krok porovná požadované prostředky s informacemi o stavu uloženými Terraform a potom navede plánované spuštění. Prostředky Azure nejsou vytvořeny v tomto okamžiku.

```bash
terraform plan
```

Po provedení předchozího příkazu byste měli vidět něco jako na následující obrazovce:

```console
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


...

Note: You didn't specify an "-out" parameter to save this plan, so when
"apply" is called, Terraform can't guarantee this is what will execute.
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

Pokud vše vypadá správně a jste připraveni k vytvoření infrastruktury v Azure, použijte šablonu v Terraform:

```bash
terraform apply
```

Po dokončení Terraform, vaše infrastruktura virtuálních počítače je připravená. Získejte veřejnou IP adresu virtuálního počítače s [az vm show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Potom můžete SSH do virtuálního počítače:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Další informace o používání Terraform v Azure](/azure/terraform)