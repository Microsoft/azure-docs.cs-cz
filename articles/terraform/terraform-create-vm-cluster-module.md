---
title: Použijte moduly Terraform k vytvoření clusteru virtuálních počítačů v Azure
description: Další informace o použití modulů Terraform k vytvoření virtuálního počítače clusteru systému Windows v Azure
keywords: terraform, devops, virtuálního počítače, sítě, moduly
author: rloutlaw
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 10/19/2017
ms.custom: devops
ms.author: routlaw
ms.openlocfilehash: e33aef252413eeb243b03543f171d5f1e2385b48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29952213"
---
# <a name="create-a-vm-cluster-with-terraform-using-the-module-registry"></a>Vytvoření clusteru s podporou virtuálních počítačů s Terraform pomocí klíče registru modulu

Tento článek vás provede procesem vytvoření clusteru s podporou malých virtuálních počítačů s Terraform [Azure výpočetní modulu](https://registry.terraform.io/modules/Azure/compute/azurerm/1.0.2). V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Nastavení ověřování s Azure
> * Vytvoření šablony Terraform
> * Vizualizace změny s plánem
> * Použít konfiguraci k vytvoření clusteru virtuálních počítačů

Další informace o Terraform najdete v tématu [Terraform dokumentaci](https://www.terraform.io/docs/index.html).

## <a name="set-up-authentication-with-azure"></a>Nastavení ověřování s Azure

> [!TIP]
> Pokud jste [použití proměnných prostředí Terraform](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables) nebo spustit tomto kurzu [prostředí cloudu Azure](/azure/cloud-shell/overview), tento krok přeskočit.

 Zkontrolujte [Terraform instalovat a konfigurovat přístup k Azure](/azure/virtual-machines/linux/terraform-install-configure) k vytvoření objektu služby Azure. Použití tohoto objektu služby k naplnění nový soubor `azureProviderAndCreds.tf` v prázdného adresáře s následujícím kódem:

```tf
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

Vytvořte novou šablonu Terraform s názvem `main.tf` následujícím kódem:

```tf
module mycompute {
    source = "Azure/compute/azurerm"
    resource_group_name = "myResourceGroup"
    location = "East US 2"
    admin_password = "ComplxP@assw0rd!"
    vm_os_simple = "WindowsServer"
    remote_port = "3389"
    nb_instances = 2
    public_ip_dns = ["unique_dns_name"]
    vnet_subnet_id = "${module.network.vnet_subnets[0]}"
}

module "network" {
    source = "Azure/network/azurerm"
    location = "East US 2"
    resource_group_name = "myResourceGroup"
}

output "vm_public_name" {
    value = "${module.mycompute.public_ip_dns_name}"
}

output "vm_public_ip" {
    value = "${module.mycompute.public_ip_address}"
}

output "vm_private_ips" {
    value = "${module.mycompute.network_interface_private_ip}"
}
```

Spustit `terraform init` ve vašem adresáři konfigurace. Používáte verzi Terraform alespoň 0.10.6 uvádí následující výstup:

![Init – Terraform](media/terraformInitWithModules.png)

## <a name="visualize-the-changes-with-plan"></a>Vizualizace změny s plánem

Spustit `terraform plan` náhled infrastrukturu virtuálních počítačů, které jsou vytvořené pomocí šablony.

![Plán Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-plan.png)


## <a name="create-the-virtual-machines-with-apply"></a>Vytvoření virtuálních počítačů s použít

Spustit `terraform apply` ke zřízení virtuálních počítačů v Azure.

![Použít Terraform](media/terraform-create-vm-cluster-with-infrastructure/terraform-apply.png)

## <a name="next-steps"></a>Další kroky

- Procházet seznam [Azure Terraform moduly](https://registry.terraform.io/modules/Azure)
- Vytvoření [škálovací sady virtuálních počítačů s Terraform](terraform-create-vm-scaleset-network-disks-hcl.md)
