---
title: Kurz – vytvoření clusteru virtuálních počítačích Azure s Terraform a HCL
description: V tomto kurzu použijete Terraform a HCL k vytvoření clusteru virtuálních strojů Linux u nástroje pro vyrovnávání zatížení v Azure
keywords: cluster virtuálních strojů azure devops terraform vm
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: ae1b8eac15309ff27297d9472e70d32e68acaaac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945271"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-and-hcl"></a>Kurz: Vytvoření clusteru virtuálních počítačů Azure s Terraform a HCL

V tomto kurzu uvidíte, jak vytvořit malý výpočetní cluster pomocí [HCL](https://www.terraform.io/docs/configuration/syntax.html). 

Dozvíte se, jak provést následující úkoly:

> [!div class="checklist"]
> * Nastavte ověřování Azure.
> * Vytvořte konfigurační soubor Terraform.
> * K vytvoření nástroje pro vyrovnávání zatížení použijte konfigurační soubor Terraform.
> * Pomocí konfiguračního souboru Terraform nasadit dva virtuální počítače SIF V množině dostupnosti.
> * Inicializujte Terraform.
> * Vytvořte plán spuštění Terraform.
> * Použijte plán spuštění Terraform k vytvoření prostředků Azure.

## <a name="1-set-up-azure-authentication"></a>1. Nastavení ověřování Azure

> [!NOTE]
> Pokud [používáte proměnné prostředí nástroje Terraform](terraform-install-configure.md) nebo tento kurz spouštíte ve službě [Azure Cloud Shell](terraform-cloud-shell.md), tuto část přeskočte.

V této části vygenerujete instanční objekt Azure a dva konfigurační soubory Terraformu obsahující přihlašovací údaje z objektu zabezpečení.

1. [Nastavte instanční objekt Azure AD](terraform-install-configure.md#set-up-terraform-access-to-azure), abyste Terraformu umožnili zřizovat prostředky do Azure. Při vytváření objektu zabezpečení si poznamenejte hodnoty ID předplatného, tenanta, appId a hesla.

2. Otevřete příkazový řádek.

3. Vytvořte prázdný adresář, do kterého budete ukládat soubory Terraformu.

4. Vytvořte nový soubor, který bude obsahovat deklarace proměnných. Tento soubor můžete pojmenovat libovolně, ale přípona názvu souboru musí být `.tf`.

5. Do souboru deklarace proměnných zkopírujte následující kód:

   ```hcl
   variable subscription_id {}
   variable tenant_id {}
   variable client_id {}
   variable client_secret {}
  
   provider "azurerm" {
      version = "~>1.40"
     
      subscription_id = var.subscription_id
      tenant_id = var.tenant_id
      client_id = var.client_id
      client_secret = var.client_secret
   }
   ```

6. Vytvořte nový soubor, který bude obsahovat hodnoty proměnných Terraformu. Je běžné pojmenovat soubor `terraform.tfvars` proměnných Terraform jako Terraform `terraform.tfvars` automaticky načte `*.auto.tfvars`libovolný soubor s názvem (nebo podle vzoru) pokud je přítomen v aktuálním adresáři. 

7. Do souboru proměnných zkopírujte následující kód. Nezapomeňte následovně nahradit zástupné hodnoty: U `subscription_id` použijte ID předplatného Azure, které jste zadali při spuštění `az account set`. U `tenant_id` použijte hodnotu `tenant` vrácenou příkazem `az ad sp create-for-rbac`. U `client_id` použijte hodnotu `appId` vrácenou příkazem `az ad sp create-for-rbac`. U `client_secret` použijte hodnotu `password` vrácenou příkazem `az ad sp create-for-rbac`.

   ```hcl
   subscription_id = "<azure-subscription-id>"
   tenant_id = "<tenant-returned-from-creating-a-service-principal>"
   client_id = "<appId-returned-from-creating-a-service-principal>"
   client_secret = "<password-returned-from-creating-a-service-principal>"
   ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Vytvoření konfiguračního souboru Terraform

V této části vytvoříte soubor obsahující definice prostředků pro vaši infrastrukturu.

1. Vytvořte nový soubor s názvem `main.tf`. 

2. Do nově vytvořeného souboru `main.tf` zkopírujte následující ukázkové definice prostředku: 

   ```hcl
   resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
   }

   resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name
   }

   resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = azurerm_resource_group.test.name
    virtual_network_name = azurerm_virtual_network.test.name
    address_prefix       = "10.0.2.0/24"
   }

   resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    allocation_method            = "Static"
   }

   resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = azurerm_public_ip.test.id
    }
   }

   resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = azurerm_resource_group.test.name
    loadbalancer_id     = azurerm_lb.test.id
    name                = "BackEndAddressPool"
   }

   resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = azurerm_resource_group.test.location
    resource_group_name = azurerm_resource_group.test.name

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = azurerm_subnet.test.id
      private_ip_address_allocation = "dynamic"
    }
   }

   resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = azurerm_resource_group.test.location
    resource_group_name  = azurerm_resource_group.test.name
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
   }

   resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = azurerm_resource_group.test.location
    resource_group_name          = azurerm_resource_group.test.name
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
   }

   resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = azurerm_resource_group.test.location
    availability_set_id   = azurerm_availability_set.avset.id
    resource_group_name   = azurerm_resource_group.test.name
    network_interface_ids = [element(azurerm_network_interface.test.*.id, count.index)]
    vm_size               = "Standard_DS1_v2"

    # Uncomment this line to delete the OS disk automatically when deleting the VM
    # delete_os_disk_on_termination = true

    # Uncomment this line to delete the data disks automatically when deleting the VM
    # delete_data_disks_on_termination = true

    storage_image_reference {
      publisher = "Canonical"
      offer     = "UbuntuServer"
      sku       = "16.04-LTS"
      version   = "latest"
    }

    storage_os_disk {
      name              = "myosdisk${count.index}"
      caching           = "ReadWrite"
      create_option     = "FromImage"
      managed_disk_type = "Standard_LRS"
    }

    # Optional data disks
    storage_data_disk {
      name              = "datadisk_new_${count.index}"
      managed_disk_type = "Standard_LRS"
      create_option     = "Empty"
      lun               = 0
      disk_size_gb      = "1023"
    }

    storage_data_disk {
      name            = element(azurerm_managed_disk.test.*.name, count.index)
      managed_disk_id = element(azurerm_managed_disk.test.*.id, count.index)
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = element(azurerm_managed_disk.test.*.disk_size_gb, count.index)
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags = {
      environment = "staging"
    }
   }
   ```

## <a name="3-initialize-terraform"></a>3. Inicializovat Terraform 

[Příkaz terraform init](https://www.terraform.io/docs/commands/init.html) se používá k inicializaci adresáře, který obsahuje konfigurační soubory Terraformu – soubory vytvořené v předchozích částech. Je vhodné vždy spustit `terraform init` příkaz po napsání nové konfigurace Terraform. 

> [!TIP]
> Příkaz `terraform init` je idempotentní, což znamená, že ho můžete opakovaně volat a vždy dojde ke stejnému výsledku. Proto pokud pracujete v prostředí podporujícím spolupráci a myslíte si, že konfigurační soubory mohl někdo změnit, je vždy vhodné před provedením nebo použitím plánu příkaz `terraform init` zavolat.

Terraform inicializujete spuštěním následujícího příkazu:

  ```bash
  terraform init
  ```

  ![Inicializace Terraformu](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Vytvoření plánu spuštění Terraform

[Příkaz terraform plan](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Když chcete vygenerovat plán provádění, Terraform agreguje všechny soubory `.tf` do aktuálního adresáře. 

[Parametr -out](https://www.terraform.io/docs/commands/plan.html#out-path) uloží plán spuštění do výstupního souboru. Tato funkce řeší souběžnost problémy běžné v prostředích s více dev. Jeden takový problém vyřešen výstupním souborem je následující scénář:

1. Dev 1 vytvoří konfigurační soubor.
1. Dev 2 upraví konfigurační soubor.
1. Dev 1 použije (spustí) konfigurační soubor.
1. Dev 1 získá neočekávané výsledky s vědomím, že Dev 2 změnil konfiguraci.

Dev 1 určující výstupní soubor zabrání Dev 2 ovlivnění Dev 1. 

Pokud plán spuštění nepotřebujete uložit, spusťte následující příkaz:

  ```bash
  terraform plan
  ```

Pokud potřebujete uložit plán spuštění, spusťte následující příkaz. Nahraďte zástupné symboly odpovídajícími hodnotami pro vaše prostředí.

  ```bash
  terraform plan -out=<path>
  ```

Dalším užitečným parametrem je [-var-file](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Ve výchozím nastavení se Terraform pokusil najít soubor proměnných takto:
- Soubor s názvem`terraform.tfvars`
- Soubor pojmenovaný pomocí následujícího vzoru:`*.auto.tfvars`

Soubor proměnných však nemusí dodržovat ani jednu ze dvou předchozích konvencí. V takovém případě zadejte název souboru proměnných s parametrem, `-var-file` kde název souboru proměnné neobsahuje příponu. Následující příklad ilustruje tento bod:

```hcl
terraform plan -var-file <my-variables-file>
```

Terraform určuje akce nezbytné k dosažení stavu určeného v konfiguračním souboru.

![Vytvoření plánu provádění Terraformu](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Použít plán provádění Terraform

Posledním krokem tohoto kurzu je spuštění [příkazu terraform apply](https://www.terraform.io/docs/commands/apply.html), který sadu akcí vygenerovanou příkazem `terraform plan` použije.

Pokud chcete nejnovější plán provádění použít, spusťte následující příkaz:

  ```bash
  terraform apply
  ```

Pokud chcete použít dříve uložený plán spuštění, spusťte následující příkaz. Nahraďte zástupné symboly odpovídajícími hodnotami pro vaše prostředí:

  ```bash
  terraform apply <path>
  ```

![Použití plánu provádění Terraformu](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Vytvoření škálovací sady virtuálních strojů Azure pomocí Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)