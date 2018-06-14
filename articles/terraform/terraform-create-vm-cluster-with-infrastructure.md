---
title: Vytvoření clusteru s podporou virtuálních počítačů s Terraform a kompatibilního hardwaru
description: Použití Terraform a jazyk kompatibilního (HashiCorp konfigurace hardwaru), chcete-li vytvořit virtuální počítač s Linuxem clusteru pro vyrovnávání zatížení v Azure
keywords: terraform, devops, virtuálního počítače, sítě, moduly
author: tomarcher
manager: routlaw
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 11/13/2017
ms.author: tarcher
ms.openlocfilehash: 2435d694e6a1671a234d02f90860e5cafe98c2df
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2017
ms.locfileid: "24518796"
---
# <a name="create-a-vm-cluster-with-terraform-and-hcl"></a>Vytvoření clusteru s podporou virtuálních počítačů s Terraform a kompatibilního hardwaru

Tento kurz představuje vytváření malé výpočetního clusteru pomocí [HashiCorp konfigurace jazyk](https://www.terraform.io/docs/configuration/syntax.html) (kompatibilního hardwaru). Vytvoří nástroj pro vyrovnávání zatížení, dva virtuální počítače s Linuxem v konfiguraci [skupinu dostupnosti](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)a všechny potřebné síťové prostředky.

V tomto kurzu jste:

> [!div class="checklist"]
> * Nastavení ověřování Azure
> * Vytvoření konfiguračního souboru Terraform
> * Inicializace Terraform
> * Vytvoření plánu Terraform provádění
> * Použít plán spuštění Terraform

## <a name="1-set-up-azure-authentication"></a>1. Nastavení ověřování Azure

> [!NOTE]
> Pokud jste [použití proměnných prostředí Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables), nebo spustit tomto kurzu [prostředí cloudu Azure](terraform-cloud-shell.md), tuto část přeskočit.

V této části můžete generovat služby Azure hlavní a dva soubory Terraform konfigurace obsahující pověření z objektu zabezpečení.

1. [Nastavení služby Azure AD instanční objekt](/azure/virtual-machines/linux/terraform-install-configure#set-up-terraform-access-to-azure) umožňující Terraform zřídit prostředky do Azure. Při vytváření objektu zabezpečení, poznamenejte si hodnoty pro ID předplatného, klienty, appId a heslo.

2. Otevřete příkazový řádek.

3. Vytvoření prázdného adresáře pro uložení souborů Terraform.

4. Vytvořte nový soubor, který obsahuje vaše deklarace proměnné. Tento soubor můžete pojmenovat jakkoli chcete s `.tf` rozšíření.

5. Zkopírujte následující kód do souboru deklarace proměnné:

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

6. Vytvořte nový soubor, který obsahuje hodnoty pro Terraform proměnných. Je běžné název proměnné souboru Terraform `terraform.tfvars` jako Terraform automaticky načte všechny soubory s názvem `terraform.tfvars` (nebo následující vzorec `*.auto.tfvars`) Pokud je dostupná v aktuálním adresáři. 

7. Zkopírujte následující kód do souboru proměnné. Ujistěte se, zda jste nahradili zástupné symboly následujícím způsobem: pro `subscription_id`, použijte ID předplatného Azure, který jste zadali při spuštění `az account set`. Pro `tenant_id`, použijte `tenant` hodnota vrácená z `az ad sp create-for-rbac`. Pro `client_id`, použijte `appId` hodnota vrácená z `az ad sp create-for-rbac`. Pro `client_secret`, použijte `password` hodnota vrácená z `az ad sp create-for-rbac`.

  ```tf
  subscription_id = "<azure-subscription-id>"
  tenant_id = "<tenant-returned-from-creating-a-service-principal>"
  client_id = "<appId-returned-from-creating-a-service-principal>"
  client_secret = "<password-returned-from-creating-a-service-principal>"
  ```

## <a name="2-create-a-terraform-configuration-file"></a>2. Vytvoření konfiguračního souboru Terraform

V této části vytvoříte soubor, který obsahuje definice prostředků pro vaši infrastrukturu.

1. Vytvořte nový soubor s názvem `main.tf`. 

2. Následující ukázka definice prostředků do nově vytvořený kopie `main.tf` souboru: 

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

## <a name="3-initialize-terraform"></a>3. Inicializace Terraform 

[Terraform init příkaz](https://www.terraform.io/docs/commands/init.html) slouží k inicializaci adresář, který obsahuje konfigurační soubory Terraform – soubory, které jste vytvořili v předchozích částech. Vždy byste měli spustit `terraform init` příkaz po napsání novou konfiguraci Terraform. 

> [!TIP]
> `terraform init` Příkaz je idempotent, což znamená, že může být volána opakovaně při vytváření stejného výsledku. Proto pokud pracujete v prostředí založeném na a si myslíte, že konfigurační soubory, které může být změněna, je vždy vhodné volání `terraform init` příkazu před provedením nebo použití plánu.

K chybě při inicializaci Terraform, spusťte následující příkaz:

  ```cmd
  terraform init
  ```

  ![Inicializace Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-init.png)

## <a name="4-create-a-terraform-execution-plan"></a>4. Vytvoření plánu Terraform provádění

[Terraform plán příkaz](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Generovat plán spuštění, Terraform slučuje všechny `.tf` soubory v aktuálním adresáři. 

Pokud pracujete v prostředí spolupráce, kde mohou změnit konfiguraci mezi časem vytvoříte plán spuštění a čas použít plán spuštění, měli byste použít [příkaz plán terraform-vnější parametr](https://www.terraform.io/docs/commands/plan.html#out-path)plán spuštění uložit do souboru. Jinak, pokud pracujete v prostředí s jednou osobou, můžete vynechat `-out` parametr.

Pokud není název souboru proměnné Terraform `terraform.tfvars` a nemá postupujte podle pokynů `*.auto.tfvars` vzor, je třeba zadat název souboru pomocí [parametr - var-souboru terraform plán příkaz](https://www.terraform.io/docs/commands/plan.html#var-file-foo) při spuštění `terraform plan`příkaz.

Při zpracování `terraform plan` příkaz Terraform provede aktualizaci a určuje, jaké akce jsou nezbytné k dosažení požadované stav zadaný v konfiguračních souborech.

Pokud není nutné pro uložení plánu provádění, spusťte následující příkaz:

  ```cmd
  terraform plan
  ```

Pokud je nutné uložit plán spuštění, spusťte následující příkaz (nahrazení &lt;cesta > zástupný symbol požadovaný výstupní cesta):

  ```cmd
  terraform plan -out=<path>
  ```

![Vytváření plánu Terraform provádění](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)

## <a name="5-apply-the-terraform-execution-plan"></a>5. Použít plán spuštění Terraform

Posledním krokem v tomto kurzu se má používat [terraform použít příkaz](https://www.terraform.io/docs/commands/apply.html) použít sadu akcí vygenerovaných `terraform plan` příkaz.

Pokud chcete použít nejnovější plán spuštění, spusťte následující příkaz:

  ```cmd
  terraform apply
  ```

Pokud chcete použít plán spuštění dříve uložili, spusťte následující příkaz (nahrazení &lt;cesta > zástupný symbol cestu, která obsahuje plán uložené spuštění):

  ```cmd
  terraform apply <path>
  ```

![Použití plánu Terraform provádění](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Další kroky

- Procházet seznam [Azure Terraform moduly](https://registry.terraform.io/modules/Azure)
- Vytvoření [škálovací sady virtuálních počítačů s Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)