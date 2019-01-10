---
title: Nasazení s využitím Terraformu v prostředí Bash ve službě Azure Cloud Shell | Dokumentace Microsoftu
description: Nasazení s využitím Terraformu v prostředí Bash ve službě Azure Cloud Shell
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
ms.openlocfilehash: a08a4e7df6cf0493ab1aa6aced1abf888a61072a
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159260"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Nasazení s využitím Terraformu v prostředí Bash ve službě Azure Cloud Shell
Tento článek vás provede procesem vytvoření skupiny prostředků pomocí [Terraformu AzureRM poskytovatele](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) je open source nástroj, který kodifikuje rozhraní API do deklarativní konfigurační soubory, které může být sdílená mezi členy týmu, chcete-li upravit, zkontroluje a systémovou správou verzí. Zprostředkovatel Microsoft AzureRM se používá k interakci s prostředky pomocí Azure Resource Manageru pomocí rozhraní API AzureRM nepodporuje. 

## <a name="automatic-authentication"></a>Automatické ověřování
Terraform je ve výchozím nastavení nainstalované v Bashi ve službě Cloud Shell. Cloud Shell se navíc automaticky ověřuje výchozí předplatné Azure CLI k nasazení prostředků pomocí modulů Terraformu pro Azure.

Terraform používá výchozí předplatné Azure CLI, který je nastaven. Pokud chcete aktualizovat výchozí předplatné, spusťte:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Názorný postup
### <a name="launch-bash-in-cloud-shell"></a>Spusťte prostředí Bash ve službě Cloud Shell
1. Spustit Cloud Shell z upřednostňované umístění
2. Ověřte, zda že upřednostňované odběr se nastaví

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Vytvoření šablony Terraformu
Vytvořte novou šablonu Terraform s názvem main.tf v upřednostňovaném textovém editoru.

```
vim main.tf
```

Kopírovat/vložit tento kód do služby Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Uložte soubor a ukončete textový editor.

### <a name="terraform-init"></a>Init Terraformu
Začněte tím, že spuštění `terraform init`.

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

[Terraformu init příkaz](https://www.terraform.io/docs/commands/init.html) slouží k inicializaci pracovní adresář obsahující soubory konfigurace Terraformu. `terraform init` Příkaz je první příkaz, který by měly být spuštěny po zápisu nové konfigurace Terraformu nebo klonování existující ze správy verzí. Je bezpečné spuštění tohoto příkazu více než jednou.

### <a name="terraform-plan"></a>Příkaz terraform plan
Náhled zdroje, které chcete vytvořit šablonu Terraform s `terraform plan`.

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

[Příkaz terraform plan](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Terraform provádí obnovení, pokud explicitně zakázáno a pak určuje akce, které jsou potřebné k dosažení požadovaného stavu zadané v konfiguračních souborech. Plán můžete uložit pomocí-out a pak k dispozici terraformu použít k zajištění jsou spouštěny pouze předplánovaných akce.

### <a name="terraform-apply"></a>Příkaz terraform apply
Zřízení prostředků Azure pomocí `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Terraformu použít příkaz](https://www.terraform.io/docs/commands/apply.html) se používá k aplikování změn potřebné k dosažení požadovaný stav konfigurace.

### <a name="verify-deployment-with-azure-cli"></a>Ověření nasazení pomocí Azure CLI
Spustit `az group show -n myRgName` chcete ověřit zdroj proběhla úspěšně, zřizování.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Vyčistit zničit pomocí terraformu
Vyčistit skupiny prostředků, vytvoří se [Terraformu zničit příkaz](https://www.terraform.io/docs/commands/destroy.html) vyčistit vytvořené využitím Terraformu infrastruktury.

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

Úspěšně jste vytvořili prostředek Azure pomocí Terraformu. Navštivte další kroky a pokračujte ve čtení o službě Cloud Shell.

## <a name="next-steps"></a>Další postup
[Další informace o poskytovateli Terraformu pro Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash ve službě Cloud Shell rychlý start](quickstart.md)
