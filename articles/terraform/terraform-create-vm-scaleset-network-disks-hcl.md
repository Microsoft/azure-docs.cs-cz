---
title: Použití Terraformu k vytvoření škálovací sady virtuálních počítačů Azure
description: Kurz o použití Terraformu ke konfiguraci a správě verzí škálovací sady virtuálních počítačů Azure s virtuální sítí a spravovanými připojenými disky
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, Azure, škálovací sada, síť, úložiště, moduly
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 06/04/2018
ms.openlocfilehash: 9b6a7187827622443c3cb29566dfb5eef840d595
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666191"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set"></a>Použití Terraformu k vytvoření škálovací sady virtuálních počítačů Azure

[Škálovací sady virtuálních počítačů Azure](/azure/virtual-machine-scale-sets)-vám umožňují vytvářet a spravovat skupiny identických virtuálních počítačů s vyrovnáváním zatížení, u kterých se může počet instancí virtuálních počítačů podle zatížení či definovaného plánu automaticky zvýšit nebo snížit. 

V tomto kurzu zjistíte, jak pomocí služby [Azure Cloud Shell](/azure/cloud-shell/overview) provést následující úlohy:

> [!div class="checklist"]
> * Nastavit nasazení Terraformu
> * Použít proměnné a výstupy u nasazení Terraformu 
> * Vytvořit a nasadit síťovou infrastrukturu
> * Vytvořit a nasadit škálovací sadu virtuálních počítačů a připojit ji k síti
> * Vytvořit a nasadit jumpbox pro připojení k virtuálním počítačům prostřednictvím protokolu SSH

> [!NOTE]
> Nejnovější verzi konfiguračních souborů Terraformu použitých v tomto článku najdete v [úložišti Awesome Terraform na GitHubu](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nainstalovaný nástroj Terraform**: Postupujte podle pokynů v článku o [instalaci Terraformu a konfiguraci přístupu k Azure](/azure/virtual-machines/linux/terraform-install-configure).

- **Vytvořený pár klíčů SSH**: Pokud ještě pár klíčů SSH nemáte, postupujte podle pokynů v článku o [vytvoření a použití páru veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek služby Cloud Shell](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `vmss`.

    ```bash
    mkdir vmss
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd vmss
    ```

## <a name="create-the-variables-definitions-file"></a>Vytvoření souboru definic proměnných
V této části definujete proměnné, které přizpůsobí prostředky vytvořené nástrojem Terraform.

Ve službě Azure Cloud Shell proveďte následující kroky:

1. Vytvořte soubor s názvem `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

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

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

## <a name="create-the-output-definitions-file"></a>Vytvoření výstupního souboru definic
V této části vytvoříte soubor, který po nasazení popisuje výstup.

Ve službě Azure Cloud Shell proveďte následující kroky:

1. Vytvořte soubor s názvem `output.tf`.

    ```bash
    vi output.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód, abyste virtuálním počítačům zveřejnili plně kvalifikovaný název domény (FQDN). :

  ```JSON
    output "vmss_public_ip" {
        value = "${azurerm_public_ip.vmss.fqdn}"
    }
  ```

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování infrastruktury sítě v šabloně
V této části vytvoříte v nové skupině prostředků Azure následující síťovou infrastrukturu: 

  - Jednu virtuální síť (VNET) s adresním prostorem 10.0.0.0/16 
  - Jednu podsíť s adresním prostorem 10.0.2.0/24
  - Dvě veřejné IP adresy – jednu pro nástroj pro vyrovnávání zatížení škálovací sady virtuálních počítačů a druhou pro připojení k jumpboxu SSH

Ve službě Azure Cloud Shell proveďte následující kroky:

1. Vytvořte soubor s názvem `vmss.tf`, který bude popisovat infrastrukturu škálovací sady virtuálních počítačů.

    ```bash
    vi vmss.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Na konec tohoto souboru vložte následující kód, abyste virtuálním počítačům zveřejnili plně kvalifikovaný název domény (FQDN). 

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

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

  ```bash
  :wq
  ```

## <a name="provision-the-network-infrastructure"></a>Zřízení síťové infrastruktury
Pomocí služby Azure Cloud Shell proveďte v adresáři, ve kterém jste vytvořili konfigurační soubory (.tf), následující kroky:

1. Inicializujte Terraform.

  ```bash
  terraform init 
  ```

1. Spuštěním následujícího příkazu nasaďte definovanou infrastrukturu do Azure.

  ```bash
  terraform apply
  ```

  Vzhledem k tomu, že proměnná **location** (umístění) je v souboru `variables.tf` definovaná, ale není nikdy nastavená, vyzve vás Terraform k jejímu zadání. Můžete zadat libovolné platné umístění – například „Západní USA“ – a potom stisknout Enter. (U každé hodnoty s mezerami používejte závorky.)

1. Terraform zobrazí výstup definovaný v souboru `output.tf`. Jak můžete vidět na následujícím snímku obrazovky, plně kvalifikovaný název domény má tuto podobu: &lt;id>.&lt;umístění>.cloudapp.azure.com. Hodnota id je vypočítanou hodnotou a umístění má stejnou hodnotu, jako jste zadali při spuštění Terraformu.

  ![Plně kvalifikovaný název domény škálovací sady virtuálních počítačů pro veřejnou IP adresu](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Na webu Azure Portal vyberte z hlavní nabídky **Skupiny prostředků**.

1. Na kartě **Skupiny prostředků** vyberte **myResourceGroup** a zobrazte si prostředky vytvořené nástrojem Terraform.
  ![Síťové prostředky škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Přidání škálovací sady virtuálních počítačů

V této části se dozvíte, jak do šablony přidat následující prostředky:

- Nástroj pro vyrovnávání zatížení Azure a pravidla, která budou obsluhovat aplikaci a připojí ji k veřejné IP adrese nakonfigurované dříve v tomto článku.
- Backendový fond adres Azure, který přiřadíte k nástroji pro vyrovnávání zatížení. 
- Port sondy stavu, který používá aplikace a konfiguruje se v nástroji pro vyrovnávání zatížení. 
- Škálovací sada virtuálních počítačů, která se nachází za nástrojem pro vyrovnávání zatížení a běží na virtuální síti nasazené dříve v tomto článku.
- Server [Nginx](http://nginx.org/) na uzlech škálovací sady virtuálních počítačů používající [cloud-init](http://cloudinit.readthedocs.io/en/latest/).

V Cloud Shellu proveďte následující kroky:

1. Otevřete konfigurační soubor `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Na konec souboru vložte následující kód:

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

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Vytvořte soubor s názvem `web.conf`, který bude sloužit jako konfigurace cloud-init pro virtuální počítače, které jsou součástí škálovací sady. 

    ```bash
    vi web.conf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

  ```JSON
  #cloud-config
  packages:
    - nginx
  ```

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Otevřete konfigurační soubor `variables.tf`.

  ```bash
  vi variables.tf
  ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Nasazení přizpůsobte vložením následujícího kódu na konec souboru:

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

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Vytvořte plán Terraformu k vizualizaci nasazení škálovací sady virtuálních počítačů. (Budete muset zadat vlastní heslo a umístění pro vaše prostředky.)

  ```bash
  terraform plan
  ```

  Výstup příkazu by se měl podobat následujícímu snímku obrazovky:

  ![Výstup z vytváření škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-hcl/add-mvss-plan.png)

1. Nasaďte tyto nové prostředky do Azure.

  ```bash
  terraform apply 
  ```

  Výstup příkazu by se měl podobat následujícímu snímku obrazovky:

  ![Skupina prostředků škálovací sady virtuálních počítačů Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents.png)

1. Otevřete prohlížeč a připojte se k plně kvalifikovanému názvu domény vrácenému příkazem. 

  ![Výsledky přejití na plně kvalifikovaný název domény](./media/terraform-create-vm-scaleset-network-disks-hcl/browser-fqdn.png)

## <a name="add-an-ssh-jumpbox"></a>Přidání jumpboxu SSH
*Jumpbox* SSH je samostatný server, kterým můžete „proskočit“ a získat přístup k ostatním serverům v síti. V tomto kroku nakonfigurujete následující prostředky:

- Síťové rozhraní (neboli jumpbox) připojené ke stejné podsíti jako škálovací sada virtuálních počítačů

- Virtuální počítač připojený k tomuto síťovému rozhraní Tento „jumpbox“ je vzdáleně přístupný. Jakmile se připojíte, můžete přes SSH získat přístup k libovolnému virtuálnímu počítači ve škálovací sadě.

1. Otevřete konfigurační soubor `vmss.tf`.

  ```bash
  vi vmss.tf
  ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Na konec souboru vložte následující kód:

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

1. Otevřete konfigurační soubor `output.tf`.

  ```bash
  vi output.tf
  ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Na konec souboru vložte následující kód, aby se po dokončení nasazení zobrazil název hostitele jumpboxu:

  ```
  output "jumpbox_public_ip" {
      value = "${azurerm_public_ip.jumpbox.fqdn}"
  }
  ```

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Nasaďte jumpbox.

  ```bash
  terraform apply 
  ```

Po dokončení nasazení se bude obsah skupiny prostředků podobat následujícímu snímku obrazovky:

![Skupina prostředků škálovací sady virtuálních počítačů Terraform](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-contents-final.png)

> [!NOTE]
> Možnost přihlásit se pomocí hesla je u nasazeného jumpboxu a škálovací sady virtuálních počítačů zakázaná. Přihlaste se pomocí SSH, abyste získali přístup k virtuálnímu počítači / virtuálním počítačům.

## <a name="environment-cleanup"></a>Vyčištění prostředí 

Pokud chcete odstranit prostředky Terraformu, které jste v tomto kurzu vytvořili, zadejte do služby Cloud Shell následující příkaz:

```bash
terraform destroy
```

Odstranění můžete trvat i několik minut.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak použit Terraform k vytvoření škálovací sady virtuálních počítačů Azure. Pokud chcete o nástroji Terraform v Azure získat více informací, můžou vám pomoct následující prostředky: 

 [Dokumentace k Terraformu v Azure](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentace k Terraformu zprostředkovatele Azure](http://aka.ms/terraform)  
 [Zdrojová data k Terraformu zprostředkovatele Azure](http://aka.ms/tfgit)  
 [Moduly Terraformu pro Azure](http://aka.ms/tfmodules)