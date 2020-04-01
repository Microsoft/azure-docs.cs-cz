---
title: Kurz – vytvoření škálovací sady virtuálních strojů Azure z vlastní image Packer pomocí Terraform
description: Použijte Terraform ke konfiguraci a správě verzí škálovací sady virtuálních počítačů Azure z vlastní image vygenerované v Packeru (s virtuální sítí a spravovanými připojenými disky).
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 92a8221d625f8b6b73343f74b85fdfcf5e578b23
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472196"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image-by-using-terraform"></a>Kurz: Vytvoření škálovací sady virtuálních strojů Azure z vlastní image Packer pomocí Terraform

V tomto kurzu použijete [Terraform](https://www.terraform.io/) k vytvoření a nasazení [škálovací sady virtuálních počítačů Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) vytvořené pomocí vlastní bitové kopie vytvořené pomocí [packeru](https://www.packer.io/intro/index.html) se spravovanými disky, které používají [konfigurační jazyk HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte nasazení Terraformu.
> * Používejte proměnné a výstupy pro nasazení Terraform.
> * Vytvořte a nasaďte síťovou infrastrukturu.
> * Vytvořte si vlastní image virtuálního počítače pomocí Packeru.
> * Vytvořte a nasaďte škálovací sadu virtuálních strojů pomocí vlastní image.
> * Vytvořte a nasaďte jumpbox.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

- **Terraform**: [Nainstalujte Terraform a nakonfigurujte přístup k Azure](terraform-install-configure.md).
- **SSH pár klíčů**: [Vytvořte pár klíčů SSH](/azure/virtual-machines/linux/mac-create-ssh-keys).
- **Balírna**: [Nainstalujte packer](https://www.packer.io/docs/install/index.html).

## <a name="create-the-file-structure"></a>Vytvoření struktury souboru

V prázdném adresáři vytvořte tři nové soubory s následujícími názvy:

- `variables.tf`: Tento soubor obsahuje hodnoty proměnných použitých v šabloně.
- `output.tf`: Tento soubor popisuje nastavení, která se zobrazí po nasazení.
- `vmss.tf`: Tento soubor obsahuje kód infrastruktury, kterou nasazujete.

##  <a name="create-the-variables"></a>Vytvoření proměnných 

V tomto kroku definujete proměnné, které přizpůsobí prostředky vytvořené nástrojem Terraform.

Upravte `variables.tf` soubor, zkopírujte následující kód a uložte změny.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> Výchozí hodnota proměnné resource_group_name je unset. Definujte svou vlastní hodnotu.

Uložte soubor.

Při nasazení šablony Terraform chcete získat plně kvalifikovaný název domény, který se používá pro přístup k aplikaci. Použijte typ prostředku `output` nástroje Terraform a získejte vlastnost `fqdn` tohoto prostředku. 

Upravte soubor `output.tf` a zkopírujte následující kód, abyste virtuálním počítačům zveřejnili plně kvalifikovaný název domény. 

```hcl 
output "vmss_public_ip" {
    value = azurerm_public_ip.vmss.fqdn
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování infrastruktury sítě v šabloně 

V tomto kroku vytvoříte v nové skupině prostředků Azure následující síťovou infrastrukturu: 
  - Jedna virtuální síť s adresním prostorem 10.0.0.0/16.
  - Jedna podsíť s adresním prostorem 10.0.2.0/24.
  - Dvě veřejné IP adresy Jeden se používá nástroj pro vyrovnávání zatížení sady zatížení virtuálního počítače. Druhý se používá pro připojení ke jumpboxu SSH.

Budete také potřebovat skupinu prostředků, ve které se budou všechny prostředky vytvářet. 

Do souboru `vmss.tf` zkopírujte následující kód: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = var.resource_group_name
  location = var.location

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = azurerm_resource_group.vmss.name
  virtual_network_name = azurerm_virtual_network.vmss.name
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = azurerm_resource_group.vmss.name

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Označte prostředky, které se nasazují v Azure, aby se v budoucnu usnadnila jejich identifikace.

## <a name="create-the-network-infrastructure"></a>Vytvoření síťové infrastruktury

Spuštěním následujícího příkazu v adresáři, ve kterém jste vytvořili soubory `.tf`, inicializujte prostředí Terraformu:

```bash
terraform init 
```
 
Moduly plug-in zprostředkovatele se stáhnou `.terraform` z registru Terraform do složky v adresáři, ve kterém jste příkaz spustili.

Spuštěním následujícího příkazu nasaďte infrastrukturu do Azure.

```bash
terraform apply
```

Ověřte, zda plně kvalifikovaný název domény veřejné IP adresy odpovídá vaší konfiguraci.

![Škálovací sada virtuálních počítačů Terraform plně kvalifikovaný název domény pro veřejnou IP adresu](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Skupina prostředků obsahuje následující prostředky:

![Síťové prostředky škálovací sady virtuálních počítačů Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-by-using-packer"></a>Vytvoření image Azure pomocí Packeru
Vytvořte si vlastní bitovou kopii Linuxu podle kroků v kurzu [Jak pomocí Packeru vytvořit ibi virtuálních strojů Linux v Azure](/azure/virtual-machines/linux/build-image-with-packer).
 
Postupujte podle kurzu vytvořit deprovisioned Ubuntu image s Nginx nainstalován.

![Po vytvoření obrázku Packer máte k vám obrázek](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Pro účely tohoto kurzu je v bitové kopii packeru spuštěn příkaz k instalaci Nginx. Při vytváření si také můžete spustit vlastní skript.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Úprava infrastruktury pro přidání škálovací sady virtuálních počítačů

V tomto kroku vytvoříte v síti, kterou jste dříve nasadili, následující prostředky:
- Azure vyrovnávání zatížení obsluhovat aplikaci. Připojte ji k veřejné IP adrese, která byla nasazena dříve.
- Jeden Azure vyrovnávání zatížení a pravidla pro obsluhu aplikace. Připojte ji k veřejné IP adrese, která byla nakonfigurována dříve.
- Fond adres azure back-end. Přiřaďte jej k vykladaču zatížení.
- Port sondy stavu používaný aplikací a nakonfigurovaný v zařízení pro vyrovnávání zatížení.
- Škálovací sada virtuálních strojů, která je za nástrojem pro vyrovnávání zatížení a běží ve virtuální síti, která byla nasazena dříve.
- [Nginx](https://nginx.org/) na uzlech měřítka virtuálního počítače nainstalovaného z vlastní bitové kopie.


Na konec souboru `vmss.tf` přidejte následující kód.

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.vmss.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = azurerm_resource_group.vmss.name
  loadbalancer_id     = azurerm_lb.vmss.id
  name                = "ssh-running-probe"
  port                = var.application_port
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = azurerm_resource_group.vmss.name
  loadbalancer_id                = azurerm_lb.vmss.id
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = var.application_port
  backend_port                   = var.application_port
  backend_address_pool_id        = azurerm_lb_backend_address_pool.bpepool.id
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = azurerm_lb_probe.vmss.id
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = data.azurerm_resource_group.image.name
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id=data.azurerm_image.image.id
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = azurerm_subnet.vmss.id
      load_balancer_backend_address_pool_ids = [azurerm_lb_backend_address_pool.bpepool.id]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Přidáním následujícího kódu do souboru `variables.tf` přizpůsobte nasazení:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Nasazení škálovací sady virtuálních počítačů do Azure

Vizualizujte nasazení škálovací sady virtuálních počítačů spuštěním následujícího příkazu:

```bash
terraform plan
```

Výstup tohoto příkazu bude vypadat podobně jako následující obrázek:

![Přidání plánu Terraformu škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Nasaďte do Azure další prostředky: 

```bash
terraform apply 
```

Obsah skupiny prostředků vypadá jako na následujícím obrázku:

![Skupina prostředků škálovací sady virtuálních počítačů Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Otevřete prohlížeč a připojte se k plně kvalifikovanému názvu domény vrácenému příkazem. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Přidání jumpboxu do stávající sítě 

Tento volitelný krok povolí použitím jumpboxu přístup prostřednictvím protokolu SSH k instancím škálovací sady virtuálních počítačů.

Přidejte do stávajícího nasazení následující prostředky:
- Síťové rozhraní připojené ke stejné podsíti jako škálovací sada virtuálních strojů
- Virtuální počítač s tímto síťovým rozhraním

Na konec souboru `vmss.tf` přidejte následující kód:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = var.location
  resource_group_name          = azurerm_resource_group.vmss.name
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = var.location
  resource_group_name = azurerm_resource_group.vmss.name

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = azurerm_subnet.vmss.id
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = azurerm_public_ip.jumpbox.id
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = var.location
  resource_group_name   = azurerm_resource_group.vmss.name
  network_interface_ids = [azurerm_network_interface.jumpbox.id]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = file("~/.ssh/id_rsa.pub")
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Upravte `outputs.tf` následující kód, který zobrazí název hostitele jumpboxu po dokončení nasazení:

```
output "jumpbox_public_ip" {
    value = azurerm_public_ip.jumpbox.fqdn
}
```

## <a name="deploy-the-jumpbox"></a>Nasazení jumpboxu

Nasaďte jumpbox.

```bash
terraform apply 
```

Po dokončení nasazení vypadá obsah skupiny prostředků na následujícím obrázku:

![Skupina prostředků škálovací sady virtuálních počítačů Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Přihlášení pomocí hesla je ve jumpboxu a škálovací sadě virtuálních strojů, kterou jste nasadili, zakázáno. Přihlaste se pomocí SSH pro přístup k virtuálním kvitům.

## <a name="clean-up-the-environment"></a>Vyčištění prostředí

Následující příkazy odstraní prostředky vytvořené v tomto kurzu:

```bash
terraform destroy
```

Po potvrzení odstranění prostředků zadejte *ano.* Odstranění můžete trvat i několik minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraform v Azure](/azure/terraform)
