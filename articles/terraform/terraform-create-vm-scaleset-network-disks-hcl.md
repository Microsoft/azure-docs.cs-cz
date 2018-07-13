---
title: Nastavit použití Terraformu k vytvoření škálování virtuálních počítačů Azure
description: Kurz používání Terraformu ke konfiguraci a verze měřítku virtuálních počítačů Azure nastavit kompletní s virtuální sítí a spravovat připojených disků
keywords: terraform, devops, virtuální počítače, Azure, škálovat set, síť, úložiště, moduly
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: 5922bad24c50a9d315aae42ce11a33801b9dbcaf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971829"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Nastavit použití Terraformu k vytvoření škálování virtuálních počítačů Azure

[Škálovací sady virtuálních počítačů Azure](/azure/virtual-machine-scale-sets) umožňují vytvářet a spravovat skupiny identických, zatížení virtuálních počítačů s vyrovnáváním kde počet instancí virtuálních počítačů můžete automaticky zvýšit nebo snížit v reakci na požadavky nebo definovaný plán. 

V tomto kurzu se dozvíte, jak používat [Azure Cloud Shell](/azure/cloud-shell/overview) provádět následující úlohy:

> [!div class="checklist"]
> * Nastavení nasazení Terraformu
> * Použití proměnných a výstupy pro nasazení Terraformu 
> * Vytvoření a nasazení síťové infrastruktury
> * Vytvoření a nasazení škálovací sady virtuálního počítače a připojení k síti
> * Vytvoření a nasazení jumpboxu k připojení k virtuálním počítačům pomocí protokolu SSH

> [!NOTE]
> Překopírujte nejnovější verzi Terraformu soubory konfigurace použité v tomto článku jsou v [Super Terraformu úložišti na Githubu](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nainstalujte Terraformu**: postupujte podle pokynů v článku, [Terraform a konfigurovat přístup k Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Vytvoření páru klíčů SSH**: Pokud ještě nemáte SSH pár klíčů, postupujte podle pokynů v článku, [postupy vytváření a používání veřejných a privátních pár klíčů SSH pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Vytvořit strukturu adresářů

1. Přejděte [webu Azure portal](http://portal.azure.com).

1. Otevřít [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste nevybrali prostředí dříve, vyberte **Bash** jako vaše prostředí.

    ![Příkazovém řádku služby cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive` adresáře.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář `vmss`.

    ```bash
    mkdir vmss
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Vytvořte soubor definice proměnných
V této části definujte proměnné, které vlastní prostředky vytvořené v rámci Terraformu.

V rámci Azure Cloud Shell proveďte následující kroky:

1. Vytvořte soubor s názvem `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Režim vložení výběrem I klíč.

1. Do editoru vložte následující kód:

  ```JSON
  variable "location" {
    description = "The location where resources will be created"
  }

  variable "tags" {
    description = "A map of the tags to use for the resources that are deployed"
    type        = "map"

    default = {
      environment = "codelab"
    }
  }

  variable "resource_group_name" {
    description = "The name of the resource group in which the resources will be created"
    default     = "myResourceGroup"
  }
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Vytvořit výstupní soubor definice
V této části vytvoříte soubor, který popisuje výstup po nasazení.

V rámci Azure Cloud Shell proveďte následující kroky:

1. Vytvořte soubor s názvem `output.tf`.

    ```bash
    vi output.tf
    ```

1. Režim vložení výběrem I klíč.

1. Vložte následující kód do editoru vystavit plně kvalifikovaný název domény (FQDN) pro virtuální počítače. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování síťové infrastruktury v šabloně
V této části vytvoříte následující síťové infrastruktury do nové skupiny prostředků Azure: 

  - Jedna virtuální síť (VNET) se adresní prostor 10.0.0.0/16 
  - Jednu podsíť s adresním prostorem 10.0.2.0/24
  - Dvě veřejné IP adresy. Jeden používané virtuálního počítače škálovací sady pro vyrovnávání zatížení, druhé používá pro připojení k jumpboxu SSH.

V rámci Azure Cloud Shell proveďte následující kroky:

1. Vytvořte soubor s názvem `vmss.tf` k popisu škálování virtuálního počítače nastavte infrastruktury.

    ```bash
    vi vmss.tf
    ```

1. Režim vložení výběrem I klíč.

1. Vložte následující kód na konec souboru, který má zveřejnit plně kvalifikovaný název domény (FQDN) pro virtuální počítače. 

  ```JSON
  resource "azurerm_resource_group" "vmss" {
    name     = "${var.resource_group_name}"
    location = "${var.location}"
    tags     = "${var.tags}"
  }

  resource "random_string" "fqdn" {
    length  = 6
    special = false
    upper   = false
    number  = false
  }

  resource "azurerm_virtual_network" "vmss" {
    name                = "vmss-vnet"
    address_space       = ["10.0.0.0/16"]
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"
    tags                = "${var.tags}"
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
    domain_name_label            = "${random_string.fqdn.result}"
    tags                         = "${var.tags}"
  }
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Zřízení síťové infrastruktury
Použití Azure Cloud Shell z adresáře, ve které jste vytvořili konfiguračních souborů (.tf) proveďte následující kroky:

1. Inicializujte Terraformu.

  ```bash
  terraform init 
  ```

1. Spuštěním následujícího příkazu nasaďte definované infrastruktury v Azure.

  ```bash
  terraform apply
  ```

  Terraform vás vyzve k zadání hodnoty "umístění" jako **umístění** proměnná je definována v `variables.tf`, ale nikdy je nastaven. Můžete zadat jakékoli platné umístění, třeba "Západní USA" a potom vyberte Enter. (Uzavřete do závorek libovolná hodnota obsahující mezery.)

1. Terraform vytiskne výstup, jak jsou definovány v `output.tf` souboru. Jak je znázorněno na následujícím snímku obrazovky, plně kvalifikovaný název domény má podobu &lt;id >.&lt; umístění >. cloudapp.azure.com. Hodnota id je vypočítaná hodnota a hodnota, kterou zadáte při spuštění Terraform je umístění.

  ![Škálovací sady virtuálního počítače plně kvalifikovaným názvem domény pro veřejnou IP adresu](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. V nabídce portálu Azure vyberte **skupiny prostředků** v hlavní nabídce.

1. Na **skupiny prostředků** kartu, vyberte možnost **myResourceGroup** k zobrazení prostředků, které byly vytvořeny pomocí Terraformu.
  ![Škálovací sady virtuálních počítačů síťových prostředků](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Přidat škálovací sadu virtuálních počítačů

V této části se dozvíte, jak do šablony přidat následující prostředky:

- Azure load balancer a pravidla, která slouží k aplikaci a připojit ho k veřejné IP adresy nakonfigurován výše v tomto článku
- Fond adres back-endu Azure a přiřaďte ho k nástroji pro vyrovnávání zatížení 
- Port testu stavu využívané aplikací a nakonfigurovat v nástroji pro vyrovnávání zatížení 
- Virtuální počítač škálovací sadě za bránou nástroj pro vyrovnávání zatížení, která se spouští ve virtuální síti nasadit výše v tomto článku
- [Server Nginx](http://nginx.org/) na uzlech škálování virtuálního počítače pomocí [cloud-init](http://cloudinit.readthedocs.io/en/latest/).

Ve službě Cloud Shell proveďte následující kroky:

1. Otevřít `vmss.tf` konfigurační soubor.

  ```bash
  vi vmss.tf
  ```

1. Přejděte na konec souboru a zadejte tak, že vyberete klíč A režimu připojení.

1. Vložte následující kód na konec souboru:

  ```JSON
  resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = "${var.location}"
    resource_group_name = "${azurerm_resource_group.vmss.name}"

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.vmss.id}"
    }

    tags = "${var.tags}"
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
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_profile_os_disk {
      name              = "osdisk"
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
      admin_username       = "${var.admin_user}"
      admin_password       = "${var.admin_password}"
      custom_data          = "${file("web.conf")}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = "${var.tags}"
}
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Vytvořte soubor s názvem `web.conf` sloužit jako konfigurace cloud-init pro virtuální počítače, které jsou součástí škálovací sady. 

    ```bash
    vi web.conf
    ```

1. Režim vložení výběrem I klíč.

1. Do editoru vložte následující kód:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Otevřít `variables.tf` konfigurační soubor.

  ```bash
  vi variables.tf
  ```

1. Přejděte na konec souboru a zadejte tak, že vyberete klíč A režimu připojení.

1. Přizpůsobte nasazení vložíte následující kód na konec souboru:

  ```JSON
  variable "application_port" {
      description = "The port that you want to expose to the external load balancer"
      default     = 80
  }

  variable "admin_user" {
      description = "User name to use as the admin account on the VMs that will be part of the VM Scale Set"
      default     = "azureuser"
  }

  variable "admin_password" {
      description = "Default password for admin account"
  }
  ``` 

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Vytvoření plánu Terraformu k vizualizaci nasazování škálovací sady virtuálních počítačů. (Budete muset zadat heslo, které si vyberete, stejně jako umístění pro vaše prostředky.)

  ```bash
  terraform plan
  ```

  Výstup příkazu by měl být podobně jako na následujícím snímku obrazovky:

  ![Výstup z vytvoření škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Nasadíte nové prostředky v Azure.

  ```bash
  terraform apply 
  ```

  Výstup příkazu by měl být podobně jako na následujícím snímku obrazovky:

  ![Terraform škálovací sady virtuálních počítačů skupiny prostředků](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Otevřete prohlížeč a připojte se k plně kvalifikovaný název domény, vrácený rutinou příkazu. 

  ![Výsledky přechodu na plně kvalifikovaný název domény](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Přidat jumpboxu SSH
SSH *jumpbox* je jediný server, který je "jump" prostřednictvím za účelem přístupu k dalších serverů v síti. V tomto kroku můžete konfigurovat následující prostředky:

- Síťové rozhraní (nebo jumpbox) připojené do stejné podsítě jako škálovací sada virtuálních počítačů.

- Virtuální počítač připojený k tomuto rozhraní sítě. Tento jumpbox je vzdáleně přístupný. Jakmile budete připojeni, získat přístup přes SSH k některé z virtuálních počítačů ve škálovací sadě.

1. Otevřít `vmss.tf` konfigurační soubor.

  ```bash
  vi vmss.tf
  ```

1. Přejděte na konec souboru a zadejte tak, že vyberete klíč A režimu připojení.

1. Vložte následující kód na konec souboru:

  ```JSON
  resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = "${var.location}"
    resource_group_name          = "${azurerm_resource_group.vmss.name}"
    public_ip_address_allocation = "static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = "${var.tags}"
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

    tags = "${var.tags}"
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
      admin_username = "${var.admin_user}"
      admin_password = "${var.admin_password}"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = "${var.tags}"
  }
  ```

1. Otevřít `output.tf` konfigurační soubor.

  ```bash
  vi output.tf
  ```

1. Přejděte na konec souboru a zadejte tak, že vyberete klíč A režimu připojení.

1. Vložte následující kód na konec souboru, který se zobrazí název hostitele jumpbox po dokončení nasazení:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Nasazení jumpbox.

  ```bash
  terraform apply 
  ```

Po dokončení nasazení se podobá obsah skupiny prostředků, které je znázorněno na následujícím snímku obrazovky:

![Terraform škálovací sady virtuálních počítačů skupiny prostředků](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Možnost přihlásit pomocí hesla je zakázána na jumpbox a škálovací sady virtuálních počítačů, že jste nasadili. Přihlaste se pomocí SSH pro přístup k virtuální počítače.

## <a name="environment-cleanup"></a>Vyčištění prostředí 

Pokud chcete odstranit Terraformu prostředky, které byly vytvořeny v tomto kurzu, zadejte následující příkaz do služby Cloud Shell:

```bash
terraform destroy
```

Proces ničení může trvat několik minut.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak použití Terraformu k vytvoření škálovací sady virtuálních počítačů Azure. Tady jsou některé další prostředky, které vám pomohou získat informace o Terraformu v Azure: 

 [Terraform centru na webu Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentaci poskytovatele Terraformu pro Azure](http://aka.ms/terraform)  
 [Zdrojový poskytovatel Terraformu pro Azure](http://aka.ms/tfgit)  
 [Moduly Terraformu pro Azure](http://aka.ms/tfmodules)