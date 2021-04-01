---
title: Nasazení pomocí Terraformu z Azure Cloud Shell | Microsoft Docs
description: Nasazení pomocí Terraformu z Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform
ms.openlocfilehash: e13e6d9ac2f4600e41f221efd23997f712dffc54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92745432"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Nasazení pomocí Terraformu z bash v Azure Cloud Shell
Tento článek vás provede vytvořením skupiny prostředků s [poskytovatelem Terraformu AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp terraformu](https://www.terraform.io/) je open source nástroj, který kodifikovaly rozhraní API na deklarativní konfigurační soubory, které je možné sdílet mezi členy týmu, aby je bylo možné upravovat, kontrolovat a ve verzi. Zprostředkovatel Microsoft AzureRM se používá k interakci s prostředky, které podporuje Azure Resource Manager prostřednictvím rozhraní API AzureRM.

## <a name="automatic-authentication"></a>Automatické ověřování
Terraformu je ve výchozím nastavení nainstalován v bash ve Cloud Shell. Navíc Cloud Shell automaticky ověřuje vaše výchozí předplatné Azure CLI pro nasazení prostředků prostřednictvím modulů Azure v Terraformu.

Terraformu používá výchozí předplatné Azure CLI, které je nastavené. Chcete-li aktualizovat výchozí odběry, spusťte příkaz:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Názorný postup
### <a name="launch-bash-in-cloud-shell"></a>Spustit bash v Cloud Shell
1. Spustit Cloud Shell z preferovaného umístění
2. Ověřte, jestli je nastavené preferované předplatné.

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Vytvoření šablony Terraformu
Vytvořte novou šablonu Terraformu s názvem main.tf s preferovaným textovým editorem.

```
vim main.tf
```

Zkopírujte nebo vložte následující kód do Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Uložte soubor a ukončete textový editor.

### <a name="terraform-init"></a>Terraformu init
Začněte spuštěním `terraform init` .

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

K inicializaci pracovního adresáře obsahujícího konfigurační soubory Terraformu se používá [příkaz terraformu init](https://www.terraform.io/docs/commands/init.html) . `terraform init`Příkaz je prvním příkazem, který by měl být spuštěn po zápisu nové konfigurace terraformu nebo klonování existujícího ze správy verzí. Spuštění tohoto příkazu se dá bezpečně provést víckrát.

### <a name="terraform-plan"></a>Příkaz terraform plan
Zobrazte náhled prostředků, které má šablona Terraformu vytvořit s `terraform plan` .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

[Příkaz terraform plan](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Terraformu provede aktualizaci, pokud není explicitně zakázaná, a pak určí, jaké akce je potřeba k dosažení požadovaného stavu zadaného v konfiguračních souborech. Plán se dá uložit s použitím a pak se terraformu použít k tomu, aby se zajistilo, že se spustí jenom předem plánované akce.

### <a name="terraform-apply"></a>Příkaz terraform apply
Zřizování prostředků Azure pomocí `terraform apply` .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Příkaz terraformu Apply](https://www.terraform.io/docs/commands/apply.html) se používá k aplikování změn požadovaných k dosažení požadovaného stavu konfigurace.

### <a name="verify-deployment-with-azure-cli"></a>Ověření nasazení pomocí Azure CLI
Spusťte `az group show -n myRgName` , abyste ověřili, že se prostředek úspěšně zřídil.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Vyčištění pomocí terraformu zničení
Vyčistěte skupinu prostředků vytvořenou [příkazem terraformu Destroy](https://www.terraform.io/docs/commands/destroy.html) a vyčistěte infrastrukturu vytvořenou v terraformu.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Úspěšně jste vytvořili prostředek Azure prostřednictvím Terraformu. Pokud chcete pokračovat ve získávání Cloud Shell, přejděte na další kroky.

## <a name="next-steps"></a>Další kroky
[Přečtěte si o poskytovateli Azure Terraformu](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash v rychlém startu Cloud Shell](quickstart.md)
