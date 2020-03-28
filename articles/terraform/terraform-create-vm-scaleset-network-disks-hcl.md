---
title: Kurz – vytvoření škálovací sady virtuálních strojů Azure pomocí Terraform
description: Naučte se používat Terraform ke konfiguraci a verzi škálovací sady virtuálních strojů Azure.
ms.topic: tutorial
ms.date: 11/07/2019
ms.openlocfilehash: 4e445d5e6ae4b7fc4528c6d61ee2bc86870827b1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472226"
---
# <a name="tutorial-create-an-azure-virtual-machine-scale-set-using-terraform"></a>Kurz: Vytvoření škálovací sady virtuálních strojů Azure pomocí Terraform

[Škálovací sady virtuálních strojů Azure](/azure/virtual-machine-scale-sets) umožňují konfigurovat identické virtuální počítače. Počet instancí virtuálních počítačů můžete upravit na základě poptávky nebo plánu. Další informace najdete [v tématu Automatické škálování škálovací sady virtuálních strojů na webu Azure Portal](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-portal).

V tomto kurzu se dozvíte, jak pomocí [Služby Azure Cloud Shell](/azure/cloud-shell/overview) provést následující úkoly:

> [!div class="checklist"]
> * Nastavit nasazení Terraformu
> * Použít proměnné a výstupy u nasazení Terraformu
> * Vytvořit a nasadit síťovou infrastrukturu
> * Vytvořit a nasadit škálovací sadu virtuálních počítačů a připojit ji k síti
> * Vytvořit a nasadit jumpbox pro připojení k virtuálním počítačům prostřednictvím protokolu SSH

> [!NOTE]
> Nejnovější verze konfiguračních souborů Terraform použitých v tomto článku jsou v [úložišti Awesome Terraform na GitHubu](https://github.com/Azure/awesome-terraform/tree/master/codelab-vmss).

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nainstalovaný nástroj Terraform**: Postupujte podle pokynů v článku o [instalaci Terraformu a konfiguraci přístupu k Azure](terraform-install-configure.md).

- **Vytvoření páru klíčů SSH**: Další informace najdete v tématu [Jak vytvořit a použít dvojici s veřejným a soukromým klíčem SSH pro virtuální počítače s Linuxem v Azure](/azure/virtual-machines/linux/mac-create-ssh-keys).

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

1. Přejděte na [portál Azure](https://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-create-vm-scaleset-network-disks-hcl/azure-portal-cloud-shell-button-min.png)

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

V rámci Prostředí Cloud Shell Azure proveďte následující kroky:

1. Vytvořte soubor s názvem `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Do editoru vložte následující kód:

   ```hcl
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

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="create-the-output-definitions-file"></a>Vytvoření výstupního souboru definic
V této části vytvoříte soubor, který po nasazení popisuje výstup.

V rámci Prostředí Cloud Shell Azure proveďte následující kroky:

1. Vytvořte soubor s názvem `output.tf`.

    ```bash
    code output.tf
    ```

1. Do editoru vložte následující kód, abyste virtuálním počítačům zveřejnili plně kvalifikovaný název domény (FQDN).
   :

   ```hcl
    output "vmss_public_ip" {
        value = azurerm_public_ip.vmss.fqdn
    }
   ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="define-the-network-infrastructure-in-a-template"></a>Definování infrastruktury sítě v šabloně
V této části vytvoříte v nové skupině prostředků Azure následující síťovou infrastrukturu:

  - Jednu virtuální síť (VNET) s adresním prostorem 10.0.0.0/16
  - Jednu podsíť s adresním prostorem 10.0.2.0/24
  - Dvě veřejné IP adresy – jednu pro nástroj pro vyrovnávání zatížení škálovací sady virtuálních počítačů a druhou pro připojení k jumpboxu SSH

V rámci Prostředí Cloud Shell Azure proveďte následující kroky:

1. Vytvořte soubor s názvem `vmss.tf`, který bude popisovat infrastrukturu škálovací sady virtuálních počítačů.

    ```bash
    code vmss.tf
    ```

1. Na konec tohoto souboru vložte následující kód, abyste virtuálním počítačům zveřejnili plně kvalifikovaný název domény (FQDN).

   ```hcl
   resource "azurerm_resource_group" "vmss" {
    name     = var.resource_group_name
    location = var.location
    tags     = var.tags
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
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name
    tags                = var.tags
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
    allocation_method = "Static"
    domain_name_label            = random_string.fqdn.result
    tags                         = var.tags
   }
   ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="provision-the-network-infrastructure"></a>Zřízení síťové infrastruktury
Pomocí prostředí Azure Cloud Shell z adresáře, ve kterém jste vytvořili konfigurační soubory (.tf), proveďte následující kroky:

1. Inicializujte Terraform.

   ```bash
   terraform init
   ```

1. Spuštěním následujícího příkazu nasaďte definovanou infrastrukturu do Azure.

   ```bash
   terraform apply
   ```

   Terraform vás vyzve `location` k zadání `location` hodnoty, `variables.tf`protože proměnná je definována v , ale nikdy není nastavena. Můžete zadat libovolné platné umístění – například „Západní USA“ – a potom stisknout Enter. (U každé hodnoty s mezerami používejte závorky.)

1. Terraform zobrazí výstup definovaný v souboru `output.tf`. Jak je znázorněno na následujícím snímku obrazovky, hlavní `<ID>.<location>.cloudapp.azure.com`obrazový soubor musí mít následující podobu: . ID je vypočítaná hodnota a umístění je hodnota poskytnout při spuštění Terraform.

   ![Škálovací sada virtuálních počítačů plně kvalifikovaný název domény pro veřejnou IP adresu](./media/terraform-create-vm-scaleset-network-disks-hcl/fqdn.png)

1. Na webu Azure Portal vyberte z hlavní nabídky **Skupiny prostředků**.

1. Na kartě **Skupiny prostředků** vyberte **myResourceGroup** a zobrazte si prostředky vytvořené nástrojem Terraform.
   ![Síťové prostředky škálovací sady virtuálních počítačů](./media/terraform-create-vm-scaleset-network-disks-hcl/resource-group-resources.png)

## <a name="add-a-virtual-machine-scale-set"></a>Přidání škálovací sady virtuálních počítačů

V této části se dozvíte, jak do šablony přidat následující prostředky:

- Nástroj pro vyrovnávání zatížení Azure a pravidla, která budou obsluhovat aplikaci a připojí ji k veřejné IP adrese nakonfigurované dříve v tomto článku.
- Backendový fond adres Azure, který přiřadíte k nástroji pro vyrovnávání zatížení.
- Port sondy stavu, který používá aplikace a konfiguruje se v nástroji pro vyrovnávání zatížení.
- Škálovací sada virtuálních počítačů, která se nachází za nástrojem pro vyrovnávání zatížení a běží na virtuální síti nasazené dříve v tomto článku.
- Server [Nginx](https://nginx.org/) na uzlech škálovací sady virtuálních počítačů používající [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

V prostředí Cloud Shell postupujte takto:

1. Otevřete konfigurační soubor `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Na konec souboru vložte následující kód:

   ```hcl
   resource "azurerm_lb" "vmss" {
    name                = "vmss-lb"
    location            = var.location
    resource_group_name = azurerm_resource_group.vmss.name

    frontend_ip_configuration {
      name                 = "PublicIPAddress"
      public_ip_address_id = azurerm_public_ip.vmss.id
    }

    tags = var.tags
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
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
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
      admin_username       = var.admin_user
      admin_password       = var.admin_password
      custom_data          = file("web.conf")
    }

    os_profile_linux_config {
      disable_password_authentication = false
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

    tags = var.tags
   }
   ```

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Vytvořte soubor s názvem `web.conf`, který bude sloužit jako konfigurace cloud-init pro virtuální počítače, které jsou součástí škálovací sady.

    ```bash
    code web.conf
    ```

1. Do editoru vložte následující kód:

   ```hcl
   #cloud-config
   packages:
    - nginx
   ```

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

     ```bash
     :wq
     ```

1. Otevřete konfigurační soubor `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Nasazení přizpůsobte vložením následujícího kódu na konec souboru:

    ```hcl
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

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

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
SSH *jumpbox* je jeden server, který "skok" přes přístup k jiným serverům v síti. V tomto kroku nakonfigurujete následující prostředky:

- Síťové rozhraní (neboli jumpbox) připojené ke stejné podsíti jako škálovací sada virtuálních počítačů

- Virtuální počítač připojený k tomuto síťovému rozhraní Tento „jumpbox“ je vzdáleně přístupný. Jakmile se připojíte, můžete přes SSH získat přístup k libovolnému virtuálnímu počítači ve škálovací sadě.

1. Otevřete konfigurační soubor `vmss.tf`.

   ```bash
   code vmss.tf
   ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Na konec souboru vložte následující kód:

   ```hcl
   resource "azurerm_public_ip" "jumpbox" {
    name                         = "jumpbox-public-ip"
    location                     = var.location
    resource_group_name          = azurerm_resource_group.vmss.name
    allocation_method = "Static"
    domain_name_label            = "${random_string.fqdn.result}-ssh"
    tags                         = var.tags
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

    tags = var.tags
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
      admin_username = var.admin_user
      admin_password = var.admin_password
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = var.tags
   }
   ```

1. Otevřete konfigurační soubor `output.tf`.

   ```bash
   code output.tf
   ```

1. Přejděte na konec souboru a vybráním klávesy A přejděte do režimu přidávání.

1. Na konec souboru vložte následující kód, aby se po dokončení nasazení zobrazil název hostitele jumpboxu:

   ```hcl
   output "jumpbox_public_ip" {
      value = azurerm_public_ip.jumpbox.fqdn
   }
   ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

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

> [!div class="nextstepaction"] 
> [Další informace o používání Terraform v Azure](/azure/terraform)
