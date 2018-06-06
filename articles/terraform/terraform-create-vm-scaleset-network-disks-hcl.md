---
title: Nastavit Terraform použijte k vytvoření škálování virtuálního počítače Azure
description: Kurz týkající se použití Terraform ke konfiguraci a verze škálování virtuálního počítače Azure nastavit dokončení s virtuální sítí a spravované připojenými disky
keywords: terraform, devops, virtuální počítače, Azure, škálovat sady, sítě, úložiště, moduly
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/04/2018
ms.topic: article
ms.openlocfilehash: b7cd9ad90198ead7c68d838547232429dbd1289f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757316"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale"></a>Použít Terraform k vytvoření škálování virtuálního počítače Azure

[Sady škálování virtuálního počítače Azure](/azure/virtual-machine-scale-sets) umožňují vytvářet a spravovat skupiny identické, zatížení vyrovnáváním virtuálních počítačů, kde je počet instancí virtuálního počítače můžete automaticky zvyšte nebo snižte v reakci na vyžádání nebo definovaný plán. 

V tomto kurzu zjistíte, jak používat [prostředí cloudu Azure](/azure/cloud-shell/overview) k provádění následujících úloh:

> [!div class="checklist"]
> * Nastavení nasazení Terraform
> * Použití proměnných a výstupy Terraform nasazení 
> * Vytvoření a nasazení síťové infrastruktury
> * Vytvářet a nasazovat škálovací sadu virtuálních počítačů a jeho připojení k síti
> * Vytvoření a nasazení jumpbox pro připojení k virtuálním počítačům pomocí protokolu SSH

> [!NOTE]
> Nejnovější verzi Terraform soubory konfigurace použité v tomto článku jsou v [Super Terraform úložišti na Githubu](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nainstalujte Terraform**: postupujte podle pokynů v článku, [Terraform a konfigurace přístupu k Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Vytvořte dvojici klíčů SSH**: Pokud ještě nemáte SSH dvojice klíčů, postupujte podle pokynů v článku, [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Vytvořit strukturu adresáře

1. Vyhledejte [portál Azure](http://portal.azure.com).

1. Otevřete [prostředí cloudu Azure](/azure/cloud-shell/overview). Pokud jste nevybrali prostředí dříve, vyberte **Bash** jako vaše prostředí.

    ![Řádku prostředí cloudu](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive` adresáře.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `vmss`.

    ```bash
    mkdir vmss
    ```

1. Změňte adresáře do nového adresáře:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Vytvoření souboru definice proměnné
V této části definujete proměnné, které prostředky vytvořené Terraform přizpůsobit.

V prostředí cloudu Azure proveďte následující kroky:

1. Vytvořte soubor s názvem `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru:

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

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Vytvoření výstupního souboru definice
V této části vytvoříte soubor, který popisuje výstup po nasazení.

V prostředí cloudu Azure proveďte následující kroky:

1. Vytvořte soubor s názvem `output.tf`.

    ```bash
    vi output.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru ke zveřejnění plně kvalifikovaný název domény (FQDN) pro virtuální počítače. :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování síťové infrastruktury v šabloně
V této části vytvoříte následující síťové infrastruktury do nové skupiny prostředků Azure: 

  - Jedna virtuální síť (VNET) s adresním prostorem 10.0.0.0/16 
  - Jednu podsíť s adresním prostorem 10.0.2.0/24
  - Dvě veřejné IP adresy. Jeden používá virtuální počítač škálování sadu Vyrovnávání zatížení dalších používaný pro připojení k SSH jumpbox.

V prostředí cloudu Azure proveďte následující kroky:

1. Vytvořte soubor s názvem `vmss.tf` k popisu škálování virtuálních počítačů nastavení infrastruktury.

    ```bash
    vi vmss.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do konce souboru ke zveřejnění plně kvalifikovaný název domény (FQDN) pro virtuální počítače. 

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

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Zřízení síťové infrastruktury
Pomocí prostředí cloudu Azure z adresáře, kde jste vytvořili konfigurační soubory (.tf) proveďte následující kroky:

1. Inicializujte Terraform.

  ```bash
  terraform init 
  ```

1. Spusťte následující příkaz pro nasazení infrastruktury definované v Azure.

  ```bash
  terraform apply
  ```

  Terraform vás vyzve k zadání hodnotu "umístění" jako **umístění** proměnná je definována v `variables.tf`, ale nikdy nastavena. Můžete zadat všechny platné umístění – například "Západní USA" a potom výběrem Enter. (Použít jakoukoli hodnotu v závorkách prostory.)

1. Terraform vytiskne výstup, jak jsou definovány v `output.tf` souboru. Jak je znázorněno na následujícím snímku obrazovky, plně kvalifikovaný název domény má podobu &lt;id >.&lt; umístění >. cloudapp.azure.com. Hodnota id je vypočtená hodnota a hodnota, kterou zadáte při spuštění Terraform je umístění.

  ![Plně kvalifikovaný název domény pro veřejnou IP adresu sady škálování virtuálního počítače](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. V nabídce portálu Azure vyberte **skupiny prostředků** z hlavní nabídky.

1. Na **skupiny prostředků** vyberte **myResourceGroup** k zobrazení prostředků, které byly vytvořeny Terraform.
  ![Škálovací sady virtuálních počítačů síťové prostředky](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Přidat škálovací sadu virtuálních počítačů

V této části se naučíte, jak do šablony přidat následující prostředky:

- K nástroji pro vyrovnávání zatížení Azure a pravidla aplikace a připojte ji k veřejnou IP adresou nakonfigurovanou dříve v tomto článku
- Azure back-end fondu adres a přiřaďte ho ke službě Vyrovnávání zatížení 
- Port testu stavu používá aplikace a nakonfigurovat na Vyrovnávání zatížení 
- Škálování virtuálního počítače nastavit uložený za vyrovnávání zatížení, která běží na virtuální síť nasazené dříve v tomto článku
- [Nginx](http://nginx.org/) na uzly na škálování virtuálního počítače pomocí [cloudu init](http://cloudinit.readthedocs.io/en/latest/).

V prostředí cloudu proveďte následující kroky:

1. Otevřete `vmss.tf` konfigurační soubor.

  ```bash
  vi vmss.tf
  ```

1. Přejděte na konec souboru a zadejte režim připojení tak, že vyberete A klíč.

1. Vložte následující kód do konce souboru:

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

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Vytvořte soubor s názvem `web.conf` sloužit jako konfigurace cloudu init pro virtuální počítače, které jsou součástí sady škálování. 

    ```bash
    vi web.conf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Otevřete `variables.tf` konfigurační soubor.

  ```bash
  vi variables.tf
  ```

1. Přejděte na konec souboru a zadejte režim připojení tak, že vyberete A klíč.

1. Přizpůsobení nasazení vložením následující kód do konce souboru:

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

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Vytvoření plánu Terraform k vizualizaci nasazení sady škálování virtuálního počítače. (Je třeba zadat heslo, které obsahuje váš výběr, stejně jako umístění pro vaše prostředky.)

  ```bash
  terraform plan
  ```

  Výstup příkazu by měl vypadat podobně jako na následujícím snímku obrazovky:

  ![Výstup z vytváření škálovací sadu virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Nasaďte nové prostředky v Azure.

  ```bash
  terraform apply 
  ```

  Výstup příkazu by měl vypadat podobně jako na následujícím snímku obrazovky:

  ![Terraform škálovací sady virtuálních počítačů skupiny prostředků](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Otevřete prohlížeč a připojte se k plně kvalifikovaný název domény, který byl vrácen příkazem. 

  ![Výsledky procházení k plně kvalifikovaný název domény](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Přidat jumpbox SSH
SSH *jumpbox* je jediný server, který je "Přeskočit" prostřednictvím aby měli přístup ostatní servery v síti. V tomto kroku nakonfigurujete v následujících zdrojích informací:

- Síťové rozhraní (nebo jumpbox) připojené ke stejné podsíti jako škálovací sadu virtuálních počítačů.

- Virtuální počítač připojen k toto síťové rozhraní. Tento jumpbox je vzdáleně přístupný. Po připojení můžete SSH k některému z virtuálních počítačů v sadě škálování.

1. Otevřete `vmss.tf` konfigurační soubor.

  ```bash
  vi vmss.tf
  ```

1. Přejděte na konec souboru a zadejte režim připojení tak, že vyberete A klíč.

1. Vložte následující kód do konce souboru:

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

1. Otevřete `output.tf` konfigurační soubor.

  ```bash
  vi output.tf
  ```

1. Přejděte na konec souboru a zadejte režim připojení tak, že vyberete A klíč.

1. Vložte následující kód na konec souboru, který se zobrazí název hostitele jumpbox po dokončení nasazení:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Ukončení režimu vkládání výběrem klávesy Esc.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

1. Nasaďte jumpbox.

  ```bash
  terraform apply 
  ```

Po dokončení nasazení obsahu skupiny prostředků podobá zobrazená na následujícím snímku obrazovky:

![Terraform škálovací sady virtuálních počítačů skupiny prostředků](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Možnost k přihlášení pomocí hesla je zakázána na jumpbox a škálování virtuálního počítače nastavit, že jste nasadili. Přihlaste se pomocí SSH pro přístup k virtuálním počítačům.

## <a name="environment-cleanup"></a>Vyčištění prostředí 

Pokud chcete odstranit Terraform prostředky, které byly vytvořeny v tomto kurzu, zadejte následující příkaz do prostředí cloudu:

```bash
terraform destroy
```

Proces odstraňování může trvat několik minut na dokončení.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak vytvořit sadu škálování virtuálního počítače Azure pomocí Terraform. Zde jsou některé další zdroje, které obsahují další informace o Terraform v Azure: 

 [Centrum Terraform v doméně Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentaci k Terraform Azure zprostředkovatele](http://aka.ms/terraform)  
 [Zprostředkovatel zdroje Terraform Azure](http://aka.ms/tfgit)  
 [Moduly Terraform Azure](http://aka.ms/tfmodules)