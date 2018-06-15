---
title: Nastavte Terraform použijte k vytvoření škálování virtuálního počítače Azure ze balírna vlastní image
description: Terraform použijte ke konfiguraci a verze Azure škálovací sady virtuálních počítačů z vlastní image generované balírna (dokončení se virtuální sítě a spravovaných připojených disků).
keywords: terraform, devops, škálovat nastaveno, virtuální počítače, sítě, úložiště, moduly, vlastních bitových kopií, balírna
author: VaijanathB
ms.author: tarcher
ms.date: 10/29/2017
ms.topic: article
ms.openlocfilehash: 284eae93de36986e41ba80f98f86495d8f34f57b
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2017
ms.locfileid: "23945853"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Nastavte Terraform použijte k vytvoření škálování virtuálního počítače Azure ze balírna vlastní image

V tomto kurzu použijete [Terraform](https://www.terraform.io/) vytvoření a nasazení [škálovací sadu virtuálních počítačů Azure](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) vytvořené pomocí vlastní image vytvořeného pomocí [balírna](https://www.packer.io/intro/index.html) s spravované disky pomocí [HashiCorp konfigurace jazyk](https://www.terraform.io/docs/configuration/syntax.html) (kompatibilního hardwaru).  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení nasazení Terraform
> * Použití proměnných a výstupy Terraform nasazení 
> * Vytvoření a nasazení síťové infrastruktury
> * Vytvoření vlastního virtuálního počítače image pomocí balírna
> * Vytvoření a nasazení škálování virtuálních počítačů, nastavit pomocí vlastní image
> * Vytvoření a nasazení jumpbox 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="before-you-begin"></a>Než začnete
> * [Terraform instalace a konfigurace přístupu k Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Vytvořte dvojici klíčů SSH](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) Pokud jste již nemáte
> * [Nainstalujte balírna](https://www.packer.io/docs/install/index.html) Pokud ještě nemáte balírna nainstalována na místním počítači


## <a name="create-the-file-structure"></a>Vytvořit strukturu souborů

Vytvořte tři nové soubory v prázdného adresáře s těmito názvy:

- ```variables.tf```Tento soubor obsahuje hodnoty proměnných použité v šabloně.
- ```output.tf```Tento soubor popisuje nastavení, která se zobrazí po nasazení.
- ```vmss.tf```Tento soubor obsahuje kód infrastrukturu, kterou nasazujete.

##  <a name="create-the-variables"></a>Vytvořte proměnné 

V tomto kroku definujete proměnné, které prostředky vytvořené Terraform přizpůsobit.

Upravit `variables.tf` souboru, zkopírujte následující kód a potom uložte změny.

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
> Výchozí hodnota proměnné resource_group_name je unset, definujte vlastní hodnotu.

Uložte soubor.

Při nasazení šablony Terraform, budete chtít získat název plně kvalifikované domény, který se používá pro přístup k aplikaci. Použití ```output``` typ prostředku Terraform a get ```fqdn``` vlastnost prostředku. 

Upravit `output.tf` soubor a zkopírujte následující kód ke zveřejnění plně kvalifikovaný název domény pro virtuální počítače. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování síťové infrastruktury v šabloně 

V tomto kroku vytvoříte následující síťové infrastruktury do nové skupiny prostředků Azure: 
  - Jedna virtuální síť s adresním prostorem 10.0.0.0/16 
  - Jednu podsíť s adresním prostorem 10.0.2.0/24
  - Dvě veřejné IP adresy. Jeden používá Vyrovnávání zatížení virtuálního počítače škálování sadu; druhá používá pro připojení k SSH jumpbox

Musíte taky skupinu prostředků, kde jsou všechny prostředky vytvořeny. 

Upravit a zkopírujte následující kód v ```vmss.tf``` souboru: 

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
> Doporučujeme označování prostředky nasazované v Azure a usnadnění jejich identifikace v budoucnu.

## <a name="create-the-network-infrastructure"></a>Vytvořit síťovou infrastrukturu

Inicializace Terraform prostředí tak, že spustíte následující příkaz v adresáři, kde jste vytvořili `.tf` soubory:

```bash
terraform init 
```
 
Moduly plug-in zprostředkovatele stáhnout z registru Terraform do ```.terraform``` složky v adresáři, kde jste spustili příkaz.

Spusťte následující příkaz pro nasazení infrastruktury v Azure.

```bash
terraform apply
```

Ověřte, že plně kvalifikovaný název domény veřejné IP adresy odpovídá konfiguraci.

![Škálovací sady virtuálních počítačů Terraform plně kvalifikovaný název domény pro veřejnou IP adresu](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Skupina prostředků obsahuje následující prostředky:

![Škálovací sady virtuálních počítačů Terraform síťové prostředky](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Vytvoření image Azure pomocí balírna
Vytvoření vlastní image Linux pomocí kroků uvedených v tomto kurzu [postup k vytvoření bitové kopie virtuálních počítačů Linux v Azure použijte balírna](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer).
 
Postupujte podle kurzu Vytvoření bitové kopie deprovisioned Ubuntu pomocí NGINX nainstalována.

![Po vytvoření bitové kopie balírna mít bitovou kopii](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Pro účely tohoto kurzu, v imagi balírna příkaz spustí se nainstaluje nginx. Při vytváření můžete použít také vlastní skript.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Upravit infrastrukturu k přidání sady škálování virtuálního počítače

V tomto kroku vytvoříte v síti, který byl dříve nasazen v následujících zdrojích informací:
- Nástroje pro vyrovnávání zatížení Azure aplikace a připojte ji k veřejnou IP adresu, který byl nasazen v kroku 4
- Jeden Azure zatížení vyrovnávání a pravidla aplikace a připojte ji k dříve nakonfigurovali veřejnou IP adresu.
- Azure back-end fondu adres a přiřaďte ho ke službě Vyrovnávání zatížení 
- Port testu stavu používá aplikace a nakonfigurovat na Vyrovnávání zatížení 
- Nastavit uložený za nástroj pro vyrovnávání zatížení, systémem virtuální sítě předtím škálování virtuálního počítače
- [Nginx](http://nginx.org/) na uzlech škálování virtuálních počítačů nainstalovat z vlastní image


Přidejte následující kód do konce `vmss.tf` souboru.

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

Přizpůsobení nasazení přidáním následující kód do `variables.tf`:

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


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Nasadit v Azure sad škálování virtuálního počítače

Spusťte následující příkaz k vizualizaci nasazení sady škálování virtuálního počítače:

```bash
terraform plan
```

Výstup příkazu vypadá jako na následujícím obrázku:

![Terraform přidat plán sady škálování virtuálního počítače](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Nasaďte další prostředky v Azure: 

```bash
terraform apply 
```

Obsah skupiny prostředků vypadá jako na následujícím obrázku:

![Terraform škálovací sady virtuálních počítačů skupiny prostředků](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Otevřete prohlížeč a připojte se k názvu plně kvalifikované domény, který byl vrácen příkazem. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Přidání jumpbox do existující síť 

Tento volitelný krok umožňuje přístup SSH k instance pomocí jumpbox sad škálování virtuálního počítače.

Následující prostředky přidáte do stávajícího nasazení:
- Síťové rozhraní připojený ke stejné podsíti než škálovací sadu virtuálních počítačů
- Virtuální počítač s toto síťové rozhraní

Přidejte následující kód do konce `vmss.tf` souboru:

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

Upravit `outputs.tf` přidat následující kód, který zobrazí název hostitele jumpbox po dokončení nasazení:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Nasazení jumpbox

Nasaďte jumpbox.

```bash
terraform apply 
```

Po dokončení nasazení obsahu skupiny prostředků vypadá jako na následujícím obrázku:

![Terraform škálovací sady virtuálních počítačů skupiny prostředků](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Přihlášení pomocí hesla je zakázána na jumpbox a škálování virtuálního počítače nastavit, že jste nasadili. Přihlaste se pomocí SSH pro přístup virtuálních počítačů.

## <a name="clean-up-the-environment"></a>Vyčistěte prostředí.

Následující příkazy odstranit prostředky vytvořené v tomto kurzu:

```bash
terraform destroy
```

Typ `yes` Pokud budete vyzváni k potvrzení pro odstranění prostředků. Proces odstraňování může trvat několik minut.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili škálovací sadu virtuálních počítačů a jumpbox v Azure pomocí Terraform. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Inicializace Terraform nasazení
> * Použití proměnných a výstupy Terraform nasazení 
> * Vytvoření a nasazení síťové infrastruktury
> * Vytvoření vlastního virtuálního počítače image pomocí balírna
> * Vytvoření a nasazení škálování virtuálních počítačů, nastavit pomocí vlastní image
> * Vytvoření a nasazení jumpbox 