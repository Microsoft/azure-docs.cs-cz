---
title: Použití Terraformu k vytvoření škálovací sady virtuálních počítačů Azure z vlastní image Packeru
description: Použijte Terraform ke konfiguraci a správě verzí škálovací sady virtuálních počítačů Azure z vlastní image vygenerované v Packeru (s virtuální sítí a spravovanými připojenými disky).
services: terraform
ms.service: terraform
keywords: terraform, devops, škálovací sada, virtuální počítač, síť, úložiště, moduly, vlastní image, packer
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/29/2017
ms.openlocfilehash: 9e999ba8a36edd990bbab4648d9d4d98e3301153
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43668627"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Použití Terraformu k vytvoření škálovací sady virtuálních počítačů Azure z vlastní image Packeru

V tomto kurzu použijete [Terraform](https://www.terraform.io/) k vytvoření a nasazení [škálovací sady virtuálních počítačů Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) vytvořené pomocí vlastní image, kterou vytvoříte v [Packeru](https://www.packer.io/intro/index.html) pomocí spravovaných disků používajících [jazyk konfigurace společnosti HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL).  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavit nasazení Terraformu
> * Použít proměnné a výstupy u nasazení Terraformu 
> * Vytvořit a nasadit síťovou infrastrukturu
> * Vytvořit vlastní image virtuálního počítače pomocí Packeru
> * Vytvořit a nasadit škálovací sady virtuálních počítačů pomocí vlastní image
> * Vytvořit a nasadit jumpbox 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete
> * [Nainstalujte Terraform a nakonfigurujte přístup k Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
> * [Vytvořte pár klíčů SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys), pokud ho ještě nemáte.
> * [Nainstalujte Packer](https://www.packer.io/docs/install/index.html), pokud ho ještě na svém místním počítači nainstalovaný nemáte.


## <a name="create-the-file-structure"></a>Vytvoření struktury souboru

V prázdném adresáři vytvořte tři nové soubory s následujícími názvy:

- ```variables.tf``` tento soubor bude obsahovat hodnoty proměnných používaných v šabloně.
- ```output.tf``` tento soubor bude popisovat nastavení, která se zobrazí po nasazení.
- ```vmss.tf``` tento soubor bude obsahovat kód infrastruktury, kterou nasazujete.

##  <a name="create-the-variables"></a>Vytvoření proměnných 

V tomto kroku definujete proměnné, které přizpůsobí prostředky vytvořené nástrojem Terraform.

Upravte soubor `variables.tf`, zkopírujte následující kód a potom změny uložte.

```tf 
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
> Výchozí hodnota proměnné resource_group_name (název skupiny prostředků) není nastavená, definujte si vlastní.

Uložte soubor.

Při nasazování šablony Terraformu budete chtít získat plně kvalifikovaný název domény, který se používá pro přístup k aplikaci. Použijte typ prostředku ```output``` nástroje Terraform a získejte vlastnost ```fqdn``` tohoto prostředku. 

Upravte soubor `output.tf` a zkopírujte následující kód, abyste virtuálním počítačům zveřejnili plně kvalifikovaný název domény. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování infrastruktury sítě v šabloně 

V tomto kroku vytvoříte v nové skupině prostředků Azure následující síťovou infrastrukturu: 
  - Jednu virtuální síť (VNET) s adresním prostorem 10.0.0.0/16 
  - Jednu podsíť s adresním prostorem 10.0.2.0/24
  - Dvě veřejné IP adresy – jednu pro nástroj pro vyrovnávání zatížení škálovací sady virtuálních počítačů a druhou pro připojení k jumpboxu SSH

Budete také potřebovat skupinu prostředků, ve které se budou všechny prostředky vytvářet. 

Do souboru ```vmss.tf``` zkopírujte následující kód: 

```tf 

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Doporučujeme prostředky nasazované do Azure označovat, abyste si do budoucna usnadnili jejich identifikaci.

## <a name="create-the-network-infrastructure"></a>Vytvoření síťové infrastruktury

Spuštěním následujícího příkazu v adresáři, ve kterém jste vytvořili soubory `.tf`, inicializujte prostředí Terraformu:

```bash
terraform init 
```
 
Moduly plug-in poskytovatele se z registru Terraformu stáhnou do složky ```.terraform``` adresáře, ve kterém jste příkaz spustili.

Spuštěním následujícího příkazu nasaďte infrastrukturu do Azure.

```bash
terraform apply
```

Ověřte, zda plně kvalifikovaný název domény veřejné IP adresy odpovídá vaší konfiguraci.

![Plně kvalifikovaný název domény škálovací sady virtuálních počítačů Terraform pro veřejnou IP adresu](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Skupina prostředků obsahuje následující prostředky:

![Síťové prostředky škálovací sady virtuálních počítačů Terraform](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Vytvoření image Azure pomocí Packeru
Vytvořte vlastní image Linuxu pomocí postupu uvedeném v kurzu o tom, [jak v Azure vytvořit pomocí Packeru image virtuálních počítačů s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Postupujte podle kurzu a vytvořte image Ubuntu se zrušeným zřízením a nainstalovaným serverem NGINX.

![Po vytvoření image Packeru budete mít vlastní image.](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Pro účely tohoto kurzu se v imagi Packeru spouští příkaz k instalaci serveru NGINX. Při vytváření si také můžete spustit vlastní skript.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Úprava infrastruktury pro přidání škálovací sady virtuálních počítačů

V tomto kroku vytvoříte v síti, kterou jste dříve nasadili, následující prostředky:
- Nástroj pro vyrovnávání zatížení Azure, který bude obsluhovat aplikaci a připojí ji k veřejné IP adrese nasazené v kroku 4.
- Jeden nástroj pro vyrovnávání zatížení Azure a pravidla, která budou obsluhovat aplikaci a připojí ji k veřejné IP adrese nakonfigurované dříve.
- Backendový fond adres Azure, který přiřadíte k nástroji pro vyrovnávání zatížení. 
- Port sondy stavu, který používá aplikace a konfiguruje se v nástroji pro vyrovnávání zatížení. 
- Škálovací sada virtuálních počítačů, která se nachází za nástrojem pro vyrovnávání zatížení a běží na virtuální síti nasazené dříve v tomto článku.
- Server [Nginx](http://nginx.org/) na uzlech škálovací sady virtuálních počítačů nainstalované z vlastní image


Na konec souboru `vmss.tf` přidejte následující kód.

```tf


resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
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
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Přidáním následujícího kódu do souboru `variables.tf` přizpůsobte nasazení:

```tf 
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

![Skupina prostředků Terraformu škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Otevřete prohlížeč a připojte se k plně kvalifikovanému názvu domény vrácenému příkazem. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Přidání jumpboxu do stávající sítě 

Tento volitelný krok povolí použitím jumpboxu přístup prostřednictvím protokolu SSH k instancím škálovací sady virtuálních počítačů.

Přidejte do stávajícího nasazení následující prostředky:
- Síťové rozhraní připojené ke stejné podsíti jako škálovací sada virtuálních počítačů
- Virtuální počítač s tímto síťovým rozhraním

Na konec souboru `vmss.tf` přidejte následující kód:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
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
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Upravte soubor `outputs.tf` a přidejte do něj následující kód, který po dokončení nasazení zobrazí název hostitele jumpboxu:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Nasazení jumpboxu

Nasaďte jumpbox.

```bash
terraform apply 
```

Po dokončení nasazení se bude obsah skupiny prostředků podobat následujícímu obrázku:

![Skupina prostředků Terraformu škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Možnost přihlásit se pomocí hesla je u nasazeného jumpboxu a škálovací sady virtuálních počítačů zakázaná. Přihlaste se pomocí SSH, abyste k virtuálním počítačům získali přístup.

## <a name="clean-up-the-environment"></a>Vyčištění prostředí

Následující příkazy odstraní prostředky vytvořené v tomto kurzu:

```bash
terraform destroy
```

Když se zobrazí výzva k potvrzení odstranění skupiny prostředků, zadejte `yes`. Odstranění můžete trvat i několik minut.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí Terraformu nasadili do Azure škálovací sadu virtuálních počítačů a jumpbox. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Inicializovat nasazení Terraformu
> * Použít proměnné a výstupy u nasazení Terraformu 
> * Vytvořit a nasadit síťovou infrastrukturu
> * Vytvořit vlastní image virtuálního počítače pomocí Packeru
> * Vytvořit a nasadit škálovací sady virtuálních počítačů pomocí vlastní image
> * Vytvořit a nasadit jumpbox 