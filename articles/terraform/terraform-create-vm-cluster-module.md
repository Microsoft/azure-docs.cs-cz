---
title: Kurz – vytvoření clusteru virtuálních počítačích Azure s Terraform pomocí registru modulu
description: V tomto kurzu použijete moduly Terraform k vytvoření clusteru virtuálních strojů Windows v Azure
keywords: Azure Devops terraform vm registry modulu clusteru virtuálního počítače
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 9faeee9bb2f0fb6dc148a3868f6fc0dae3833a2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945291"
---
# <a name="tutorial-create-an-azure-vm-cluster-with-terraform-using-the-module-registry"></a>Kurz: Vytvoření clusteru virtuálních počítačů Azure s Terraform pomocí registru modulu

Tento článek vás provede vytvořením malého clusteru virtuálních počítačů pomocí [výpočetního modulu Terraform Azure](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). Co se v tomto kurzu naučíte: 

> [!div class="checklist"]
> * Nastavit ověřování pomocí Azure
> * Vytvořit šablonu Terraformu
> * Vizualizovat změny pomocí plánu
> * Použít konfiguraci k vytvoření clusteru virtuálních počítačů

Další informace o Terraformu najdete v [dokumentaci Terraformu](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Nastavit ověřování pomocí Azure

> [!TIP]
> Pokud [používáte proměnné prostředí nástroje Terraform](terraform-install-configure.md) nebo tento kurz spouštíte ve službě [Azure Cloud Shell](/azure/cloud-shell/overview), tento krok přeskočte.

 Projděte si článek o [instalaci Terraformu a konfiguraci přístupu k Azure](terraform-install-configure.md) a vytvořte objekt služby Azure. Tento objekt služby použijte k naplnění nového souboru `azureProviderAndCreds.tf` v prázdném adresáři následujícím kódem:

```hcl
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    version = "~>1.40"

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
    is_windows_image = "true"
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

## <a name="visualize-the-changes-with-plan"></a>Vizualizovat změny pomocí plánu

Spusťte `terraform plan`, abyste si zobrazili náhled infrastruktury virtuálního počítače, kterou vytvořila šablona.

![Příkaz terraform plan](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Vytvoření virtuálních počítačů pomocí příkazu apply

Virtuální počítače v Azure zřídíte spuštěním příkazu `terraform apply`.

![Příkaz terraform apply](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Procházení seznamu modulů Azure Terraform](https://registry.terraform.io/modules/Azure)