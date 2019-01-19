---
title: Vytvoření clusteru virtuálních počítačů pomocí Terraformu a HCL
description: Použijte Terraform a jazyk konfigurace společnosti HashiCorp (HCL) k vytvoření clusteru virtuálních počítačů s Linuxem a nástrojem pro vyrovnávání zatížení v Azure.
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, síť, moduly
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 11/13/2017
ms.openlocfilehash: a53fee8ee492de4d9eaa8b45a8d4a88e692da02d
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410366"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Vytvoření clusteru virtuálních počítačů pomocí Terraformu a HCL

Tento kurz ukazuje, jak pomocí [jazyka konfigurace společnosti HashiCorp](https://www.terraform.io/docs/configuration/syntax.html) (HCL) vytvořit malý výpočetní cluster. Konfigurace vytvoří nástroj pro vyrovnávání zatížení, dva virtuální počítače s Linuxem ve [skupině dostupnosti](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) a všechny potřebné síťové prostředky.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení ověřování Azure
> * Vytvořit konfigurační soubor Terraformu
> * Inicializovat Terraform
> * Vytvořit plán provádění Terraformu
> * Použití plánu provádění Terraformu

## <a name="1-set-up-azure-authentication"></a>1. Nastavení ověřování Azure

> [!NOTE]
> Pokud [používáte proměnné prostředí nástroje Terraform](/azure/virtual-machines/linux/terraform-install-configure) nebo tento kurz spouštíte ve službě [Azure Cloud Shell](terraform-cloud-shell.md), tuto část přeskočte.

V této části vygenerujete instanční objekt Azure a dva konfigurační soubory Terraformu obsahující přihlašovací údaje z objektu zabezpečení.

1. [Nastavte instanční objekt Azure AD](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure), abyste Terraformu umožnili zřizovat prostředky do Azure. Při vytváření objektu zabezpečení si poznamenejte hodnoty ID předplatného, tenanta, appId a hesla.

2. Otevřete příkazový řádek.

3. Vytvořte prázdný adresář, do kterého budete ukládat soubory Terraformu.

4. Vytvořte nový soubor, který bude obsahovat deklarace proměnných. Tento soubor můžete pojmenovat libovolně, ale přípona názvu souboru musí být `.tf`.

5. Do souboru deklarace proměnných zkopírujte následující kód:

  ```tf
  variable subscription_id {}
  variable tenant_id {}
  variable client_id {}
  variable client_secret {}
  
  provider "azurerm" {
      subscription_id = "${var.subscription_id}"
      tenant_id = "${var.tenant_id}"
      client_id = "${var.client_id}"
      client_secret = "${var.client_secret}"
  }
  ```

6. Vytvořte nový soubor, který bude obsahovat hodnoty proměnných Terraformu. Běžně se soubor proměnných Terraformu pojmenovává `terraform.tfvars`, protože Terraform soubor pojmenovaný `terraform.tfvars` (nebo s tvarem `*.auto.tfvars`) načte automaticky, pokud se nachází v aktuálním adresáři. 

7. Do souboru proměnných zkopírujte následující kód. Nezapomeňte nahradit zástupné symboly následujícím způsobem: Pro `subscription_id`, použijte ID předplatného Azure, který jste zadali při spuštění `az account set`. U `tenant_id` použijte hodnotu `tenant` vrácenou příkazem `az ad sp create-for-rbac`. U `client_id` použijte hodnotu `appId` vrácenou příkazem `az ad sp create-for-rbac`. U `client_secret` použijte hodnotu `password` vrácenou příkazem `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Vytvoření konfiguračního souboru Terraformu

V této části vytvoříte soubor obsahující definice prostředků pro vaši infrastrukturu.

1. Vytvořte nový soubor s názvem `main.tf`. 

2. Do nově vytvořeného souboru `main.tf` zkopírujte následující ukázkové definice prostředku: 

  ```tf
  resource "azurerm_resource_group" "test" {
    name     = "acctestrg"
    location = "West US 2"
  }

  resource "azurerm_virtual_network" "test" {
    name                = "acctvn"
    address_space       = ["10.0.0.0/16"]
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"
  }

  resource "azurerm_subnet" "test" {
    name                 = "acctsub"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    virtual_network_name = "${azurerm_virtual_network.test.name}"
    address_prefix       = "10.0.2.0/24"
  }

  resource "azurerm_public_ip" "test" {
    name                         = "publicIPForLB"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    public_ip_address_allocation = "static"
  }

  resource "azurerm_lb" "test" {
    name                = "loadBalancer"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    frontend_ip_configuration {
      name                 = "publicIPAddress"
      public_ip_address_id = "${azurerm_public_ip.test.id}"
    }
  }

  resource "azurerm_lb_backend_address_pool" "test" {
    resource_group_name = "${azurerm_resource_group.test.name}"
    loadbalancer_id     = "${azurerm_lb.test.id}"
    name                = "BackEndAddressPool"
  }

  resource "azurerm_network_interface" "test" {
    count               = 2
    name                = "acctni${count.index}"
    location            = "${azurerm_resource_group.test.location}"
    resource_group_name = "${azurerm_resource_group.test.name}"

    ip_configuration {
      name                          = "testConfiguration"
      subnet_id                     = "${azurerm_subnet.test.id}"
      private_ip_address_allocation = "dynamic"
      load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
    }
  }

  resource "azurerm_managed_disk" "test" {
    count                = 2
    name                 = "datadisk_existing_${count.index}"
    location             = "${azurerm_resource_group.test.location}"
    resource_group_name  = "${azurerm_resource_group.test.name}"
    storage_account_type = "Standard_LRS"
    create_option        = "Empty"
    disk_size_gb         = "1023"
  }

  resource "azurerm_availability_set" "avset" {
    name                         = "avset"
    location                     = "${azurerm_resource_group.test.location}"
    resource_group_name          = "${azurerm_resource_group.test.name}"
    platform_fault_domain_count  = 2
    platform_update_domain_count = 2
    managed                      = true
  }

  resource "azurerm_virtual_machine" "test" {
    count                 = 2
    name                  = "acctvm${count.index}"
    location              = "${azurerm_resource_group.test.location}"
    availability_set_id   = "${azurerm_availability_set.avset.id}"
    resource_group_name   = "${azurerm_resource_group.test.name}"
    network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
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
      name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
      managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
      create_option   = "Attach"
      lun             = 1
      disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
    }

    os_profile {
      computer_name  = "hostname"
      admin_username = "testadmin"
      admin_password = "Password1234!"
    }

    os_profile_linux_config {
      disable_password_authentication = false
    }

    tags {
      environment = "staging"
    }
  }
  ```

## <a name="3-initialize-terraform"></a>3. Inicializovat Terraform 

[Příkaz terraform init](https://www.terraform.io/docs/commands/init.html) se používá k inicializaci adresáře, který obsahuje konfigurační soubory Terraformu – soubory vytvořené v předchozích částech. Příkaz `terraform init` je dobrým zvykem spouštět vždy po zapsání nové konfigurace Terraformu. 

> [!TIP]
> Příkaz `terraform init` je idempotentní, což znamená, že ho můžete opakovaně volat a vždy dojde ke stejnému výsledku. Proto pokud pracujete v prostředí podporujícím spolupráci a myslíte si, že konfigurační soubory mohl někdo změnit, je vždy vhodné před provedením nebo použitím plánu příkaz `terraform init` zavolat.

Terraform inicializujete spuštěním následujícího příkazu:

  ```cmd
  terraform init
  ```

  ![Inicializace Terraformu](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Vytvoření plánu provádění Terraformu

[Příkaz terraform plan](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Když chcete vygenerovat plán provádění, Terraform agreguje všechny soubory `.tf` do aktuálního adresáře. 

Pokud pracujete v prostředí podporujícím spolupráci, kde se konfigurace může změnit mezi dobou, kdy plán provádění vytvoříte a dobou, kdy plán provádění použijete, měli byste u [příkazu terraform plan použít parametr -out](https://www.terraform.io/docs/commands/plan.html#out-path), abyste plán provádění uložili do souboru. Pokud ale v prostředí pracujete sami, můžete parametr `-out` vynechat.

Pokud se soubor proměnných Terraformu nejmenuje `terraform.tfvars` a není ve formátu `*.auto.tfvars`, musíte název souboru při spuštění příkazu `terraform plan` zadat pomocí [parametru -var-file příkazu terraform plan](https://www.terraform.io/docs/commands/plan.html#var-file-foo).

Když se příkaz `terraform plan` zpracovává, Terraform provede aktualizaci a určí, jaké akce jsou potřeba k dosažení požadovaného stavu zadaného v konfiguračních souborech.

Pokud svůj plán provádění ukládat nepotřebujete, spusťte následující příkaz:

  ```cmd
  terraform plan
  ```

Pokud plán provádění uložit potřebujete, spusťte tento příkaz (zástupný text &lt;path> nahraďte požadovanou výstupní cestou):

  ```cmd
  terraform plan -out=<path>
  ```

![Vytvoření plánu provádění Terraformu](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Použití plánu provádění Terraformu

Posledním krokem tohoto kurzu je spuštění [příkazu terraform apply](https://www.terraform.io/docs/commands/apply.html), který sadu akcí vygenerovanou příkazem `terraform plan` použije.

Pokud chcete nejnovější plán provádění použít, spusťte následující příkaz:

  ```cmd
  terraform apply
  ```

Pokud chcete použít dříve uložený plán provádění, spusťte tento příkaz (zástupný text &lt;path> nahraďte cestou, kde se uložený plán provádění nachází):

  ```cmd
  terraform apply <path>
  ```

![Použití plánu provádění Terraformu](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Další postup

- Projděte si seznam [modulů Terraformu pro Azure](https://registry.terraform.io/modules/Azure).
- Vytvořte [pomocí Terraformu škálovací sadu virtuálních počítačů](terraform-create-vm-scaleset-network-disks-hcl.md).
