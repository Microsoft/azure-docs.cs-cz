---
title: Rychlý Start – použití Terraformu k vytvoření kompletního virtuálního počítače se systémem Linux v Azure
description: V tomto rychlém startu použijete Terraformu k vytvoření a správě kompletního prostředí virtuálního počítače se systémem Linux v Azure.
keywords: virtuální počítač s virtuálním počítačem Azure DevOps terraformu Linux
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 03974d68477855d4ff55b7179312c91ba7d0d055
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943527"
---
# <a name="quickstart-create-a-complete-linux-virtual-machine-infrastructure-in-azure-with-terraform"></a>Rychlý Start: vytvoření úplné infrastruktury virtuálního počítače se systémem Linux v Azure pomocí Terraformu

Terraformu umožňuje definovat a vytvářet kompletní nasazení infrastruktury v Azure. Šablony Terraformu můžete vytvářet v uživatelsky čitelném formátu, který umožňuje vytvářet a konfigurovat prostředky Azure konzistentním a reprodukovatelným způsobem. V tomto článku se dozvíte, jak vytvořit úplné prostředí Linux a podpůrné prostředky pomocí Terraformu. Můžete si také přečíst, jak [nainstalovat a nakonfigurovat terraformu](terraform-install-configure.md).

> [!NOTE]
> V případě podpory specifické pro Terraformu se prosím obraťte na Terraformu přímo pomocí některého z jejich kanálů komunity:
>
>    * [Část terraformu](https://discuss.hashicorp.com/c/terraform-core) na portálu komunity obsahuje otázky, případy použití a užitečné vzory.
>
>    * Otázky související se zprostředkovatelem najdete v části [poskytovatelé terraformu](https://discuss.hashicorp.com/c/terraform-providers) na portálu komunity.


## <a name="create-azure-connection-and-resource-group"></a>Vytvoření připojení Azure a skupiny prostředků

Pojďme si projít každý oddíl šablony Terraformu. Můžete také zobrazit úplnou verzi [šablony terraformu](#complete-terraform-script) , kterou můžete zkopírovat a vložit.

Část `provider` oznamuje Terraformu použití poskytovatele Azure. Pokud chcete získat hodnoty pro *subscription_id*, *client_id*, *client_secret*a *tenant_id*, přečtěte si téma [install and Configure terraformu](terraform-install-configure.md). 

> [!TIP]
> Pokud vytvoříte proměnné prostředí pro hodnoty nebo používáte prostředí [Azure Cloud Shell bash](/azure/cloud-shell/overview) , nemusíte do této části zahrnout deklarace proměnných.

```hcl
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
    
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret   = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id       = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

V následující části se vytvoří skupina prostředků s názvem `myResourceGroup` v umístění `eastus`:

```hcl
resource "azurerm_resource_group" "myterraformgroup" {
    name     = "myResourceGroup"
    location = "eastus"

    tags = {
        environment = "Terraform Demo"
    }
}
```

V dalších oddílech odkazujete na skupinu prostředků pomocí *$ {azurerm_resource_group. myterraformgroup. Name}* .

## <a name="create-virtual-network"></a>Vytvoření virtuální sítě
V následující části se vytvoří virtuální síť s názvem *myVnet* v adresním prostoru *10.0.0.0/16* :

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

V následující části se vytvoří podsíť s názvem *mySubnet* ve virtuální síti *myVnet* :

```hcl
resource "azurerm_subnet" "myterraformsubnet" {
    name                 = "mySubnet"
    resource_group_name  = azurerm_resource_group.myterraformgroup.name
    virtual_network_name = azurerm_virtual_network.myterraformnetwork.name
    address_prefix       = "10.0.2.0/24"
}
```


## <a name="create-public-ip-address"></a>Vytvořit veřejnou IP adresu
Pokud chcete získat přístup k prostředkům přes Internet, vytvořte a přiřaďte k VIRTUÁLNÍmu počítači veřejnou IP adresu. Následující část vytvoří veřejnou IP adresu s názvem *myPublicIP*:

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
Skupiny zabezpečení sítě řídí tok síťového provozu na VIRTUÁLNÍm počítači a z něj. V následující části se vytvoří skupina zabezpečení sítě s názvem *myNetworkSecurityGroup* a definuje pravidlo povolující provoz protokolu SSH na portu TCP 22:

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


## <a name="create-virtual-network-interface-card"></a>Vytvořit kartu virtuálního síťového rozhraní
Virtuální síťová karta (NIC) připojí virtuální počítač k dané virtuální síti, veřejné IP adrese a skupině zabezpečení sítě. V následující části šablony Terraformu se vytvoří virtuální síťová karta s názvem *myNIC* připojená k virtuálním prostředkům sítě, které jste vytvořili:

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


## <a name="create-storage-account-for-diagnostics"></a>Vytvoření účtu úložiště pro diagnostiku
K uložení diagnostiky spouštění pro virtuální počítač budete potřebovat účet úložiště. Tyto diagnostiky spouštění vám můžou pomoct vyřešit problémy a monitorovat stav virtuálního počítače. Vytvořený účet úložiště slouží pouze k uložení dat diagnostiky spouštění. Protože každý účet úložiště musí mít jedinečný název, následující oddíl vygeneruje nějaký náhodný text:

```hcl
resource "random_id" "randomId" {
    keepers = {
        # Generate a new ID only when a new resource group is defined
        resource_group = azurerm_resource_group.myterraformgroup.name
    }
    
    byte_length = 8
}
```

Nyní můžete vytvořit účet úložiště. Následující část vytvoří účet úložiště s názvem založeným na náhodném textu vygenerovaném v předchozím kroku:

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

Posledním krokem je vytvoření virtuálního počítače a použití všech vytvořených prostředků. V následující části se vytvoří virtuální počítač s názvem *myVM* a připojí se virtuální síťová karta s názvem *myNIC*. Použije se nejnovější image *Ubuntu 16,04-LTS* a vytvoří se uživatel s názvem *azureuser* se zakázaným ověřováním hesla.

 Data klíče SSH jsou k dispozici v části *ssh_keys* . Zadejte platný veřejný klíč SSH v poli *key_data* .

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

    storage_image_reference {
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

## <a name="complete-terraform-script"></a>Dokončit skript Terraformu

Chcete-li všechny tyto oddíly přenést společně a zobrazit Terraformu v akci, vytvořte soubor s názvem *terraform_azure. TF* a vložte následující obsah:

```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
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


## <a name="build-and-deploy-the-infrastructure"></a>Sestavení a nasazení infrastruktury
Když máte vytvořenou šablonu Terraformu, prvním krokem je inicializace Terraformu. Tento krok zajistí, že Terraformu má všechny požadavky na sestavení šablony v Azure.

```bash
terraform init
```

Dalším krokem je Terraformu přezkoumání a ověření šablony. Tento krok porovná požadované prostředky s informacemi o stavu uloženým v Terraformu a pak provede výstup plánovaného provedení. Prostředky se *v Azure nevytvářejí.*

```bash
terraform plan
```

Po provedení předchozího příkazu by se měla zobrazit například následující obrazovka:

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

Pokud vše vypadá správně a jste připraveni vytvořit infrastrukturu v Azure, použijte šablonu v Terraformu:

```bash
terraform apply
```

Jakmile se Terraformu dokončí, vaše infrastruktura virtuálních počítačů je připravená. Získejte veřejnou IP adresu vašeho virtuálního počítače pomocí [AZ VM show](/cli/azure/vm):

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
```

Pak můžete přes SSH k VIRTUÁLNÍmu počítači:

```bash
ssh azureuser@<publicIps>
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Další informace o používání Terraformu v Azure](/azure/terraform)