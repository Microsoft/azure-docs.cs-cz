---
title: Nasazení s Terraform z Bash v prostředí cloudu Azure | Microsoft Docs
description: Nasazení s Terraform z Bash v prostředí cloudu Azure
services: Azure
documentationcenter: ''
author: tomarcher
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.openlocfilehash: 6df6a3a5242e0a5fc5c03136e1cd20967a93487a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29386516"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Nasazení s Terraform z Bash v prostředí cloudu Azure
Tento článek vás provede procesem vytvoření skupiny prostředků pomocí [Terraform AzureRM zprostředkovatele](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) je nástroj s otevřeným zdrojem, který codifies rozhraní API do deklarativní konfigurační soubory, které může být sdílelo se členové týmu chcete upravit, zkontrolovat a verzí. Zprostředkovatel Microsoft AzureRM se používá k interakci s prostředky, které jsou podporované nástrojem Azure Resource Manager prostřednictvím rozhraní API AzureRM. 

## <a name="automatic-authentication"></a>Automatické ověření
Terraform je ve výchozím nastavení nainstalován v Bash v prostředí cloudu. Kromě toho cloudové prostředí automaticky ověřuje předplatného výchozí 2.0 rozhraní příkazového řádku Azure k nasazení prostředků prostřednictvím modulů Terraform Azure.

Terraform používá výchozí předplatné 2.0 rozhraní příkazového řádku Azure, který je nastavený. Aktualizace odběrů výchozí, spusťte příkaz:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Názorný postup
### <a name="launch-bash-in-cloud-shell"></a>Spusťte Bash v prostředí cloudu
1. Spusťte prostředí cloudu z upřednostňované umístění
2. Ověřte, že je nastaven upřednostňovaný předplatného

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Vytvořit šablonu Terraform
Vytvořte novou šablonu Terraform s názvem main.tf se upřednostňované textový editor.

```
vim main.tf
```

Zkopírujte a vložte následující kód do prostředí cloudu.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Uložte soubor a ukončete textový editor.

### <a name="terraform-init"></a>Init – Terraform
Začněte spuštěním `terraform init`.

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

[Terraform init příkaz](https://www.terraform.io/docs/commands/init.html) slouží k inicializaci pracovní adresář obsahující Terraform konfigurační soubory. `terraform init` Příkaz je první příkaz, který by měly být spuštěny po zápisu novou konfiguraci Terraform nebo klonování stávající z verzí. Je bezpečné pro spuštění tohoto příkazu vícekrát.

### <a name="terraform-plan"></a>Plán Terraform
Náhled prostředků, které mají být vytvořeny šablonou Terraform s `terraform plan`.

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

[Terraform plán příkaz](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Terraform provádí obnovení, pokud není výslovně zakázáno a určuje, jaké akce jsou nezbytné k dosažení požadované stav zadaný v konfiguračních souborech. Plán můžete uložit pomocí-na víc systémů a pak poskytované terraform použít k zajištění jsou vykonány pouze předem plánované akce.

### <a name="terraform-apply"></a>Použít Terraform
Zřízení prostředků Azure s `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Terraform použít příkaz](https://www.terraform.io/docs/commands/apply.html) se používá k aplikování změny požadované k dosažení požadovaný stav konfigurace.

### <a name="verify-deployment-with-azure-cli-20"></a>Ověření nasazení s Azure CLI 2.0
Spustit `az group show -n myRgName` chcete ověřit prostředek proběhla úspěšně zřizování.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Vyčištění s terraform destroy
Vyčištění skupiny prostředků vytvořené pomocí [Terraform destroy příkaz](https://www.terraform.io/docs/commands/destroy.html) vyčistěte vytvořené Terraform infrastruktury.

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

Úspěšně jste vytvořili pomocí Terraform prostředek služby Azure. Najdete další kroky a pokračujte ve čtení o cloudové prostředí.

## <a name="next-steps"></a>Další postup
[Další informace o poskytovateli Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash v prostředí cloudu rychlý start](quickstart.md)