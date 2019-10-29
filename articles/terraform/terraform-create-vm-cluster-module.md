---
title: Kurz – vytvoření clusteru virtuálních počítačů Azure s Terraformu pomocí registru modulu
description: Zjistěte, jak použít moduly Terraformu k vytvoření clusteru virtuálních počítačů s Windows v Azure.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: ba99f9cdc20448398b339041aeab41fb75495e5d
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969483"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Kurz: Vytvoření clusteru virtuálních počítačů Azure s Terraformu pomocí registru modulu

Tento článek vás provede vytvořením malého clusteru virtuálních počítačů pomocí [výpočetního modulu Terraform Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Co se v tomto kurzu naučíte: 

> [!div class="checklist"]
> * Nastavení ověřování pomocí Azure
> * Vytvořit šablonu Terraformu
> * Vizualizace změn pomocí plánu
> * Použít konfiguraci k vytvoření clusteru virtuálních počítačů

Další informace o Terraformu najdete v [dokumentaci Terraformu](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Nastavení ověřování pomocí Azure

> [!TIP]
> Pokud [používáte proměnné prostředí nástroje Terraform](/azure/virtual-machines/linux/terraform-install-configure) nebo tento kurz spouštíte ve službě [Azure Cloud Shell](/azure/cloud-shell/overview), tento krok přeskočte.

 Projděte si článek o [instalaci Terraformu a konfiguraci přístupu k Azure](/azure/virtual-machines/linux/terraform-install-configure) a vytvořte objekt služby Azure. Tento objekt služby použijte k naplnění nového souboru `azureProviderAndCreds.tf` v prázdném adresáři následujícím kódem:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>Vytvoření šablony

Pomocí následujícího kódu vytvořte novou šablonu Terraform s názvem `main.tf`:

```hcl
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = module.network.vnet_subnets[0]
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = module.mycompute.public_ip_dns_name
}

output "vm_public_ip" {
    value = module.mycompute.public_ip_address
}

output "vm_private_ips" {
    value = module.mycompute.network_interface_private_ip
}
```

V adresáři konfigurace spusťte `terraform init`. Pokud používáte Terraform alespoň ve verzi 0.10.6, zobrazí se následující výstup:

![Příkaz terraform init](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Vizualizace změn pomocí plánu

Spusťte `terraform plan`, abyste si zobrazili náhled infrastruktury virtuálního počítače, kterou vytvořila šablona.

![Příkaz terraform plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Vytvoření virtuálních počítačů pomocí příkazu apply

Virtuální počítače v Azure zřídíte spuštěním příkazu `terraform apply`.

![Příkaz terraform apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Procházet seznam modulů Azure Terraformu](https://registry.terraform.io/modules/Azure)