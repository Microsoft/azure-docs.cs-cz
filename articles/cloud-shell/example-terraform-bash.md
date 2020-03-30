---
title: Nasazení s Terraformem z Azure Cloud Shell | Dokumenty společnosti Microsoft
description: Nasazení pomocí Terraform z Azure Cloud Shellu
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
ms.openlocfilehash: 8bacadd8941131f608411e61cc15c120c1b2bc60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458150"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Nasazení s Terraformem z Bash v Prostředí Azure Cloud Shell
Tento článek vás provede vytvořením skupiny prostředků s [poskytovatelem Terraform AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp Terraform](https://www.terraform.io/) je open source nástroj, který kodifikuje API do deklarativních konfiguračních souborů, které mohou být sdíleny mezi členy týmu, které mají být upraveny, přezkoumány a verzí. Poskytovatel Microsoft AzureRM se používá k interakci s prostředky podporovanými Správcem prostředků Azure prostřednictvím rozhraní API AzureRM.

## <a name="automatic-authentication"></a>Automatické ověřování
Terraform je ve výchozím nastavení nainstalován v bash v cloudovém prostředí. Cloud shell navíc automaticky ověřuje vaše výchozí předplatné Rozhraní příkazového systému Azure k nasazení prostředků prostřednictvím modulů Terraform Azure.

Terraform používá výchozí předplatné Azure CLI, které je nastaveno. Chcete-li aktualizovat výchozí odběry, spusťte:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Názorný postup
### <a name="launch-bash-in-cloud-shell"></a>Spuštění Bash v Cloud Shell
1. Spuštění prostředí Cloud Shell z preferovaného místa
2. Ověřte, zda je nastaveno upřednostňované předplatné.

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Vytvoření šablony Terraform
Vytvořte novou šablonu Terraform s názvem main.tf pomocí upřednostňovaného textového editoru.

```
vim main.tf
```

Zkopírujte/vložte následující kód do prostředí Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Uložte soubor a ukončete textový editor.

### <a name="terraform-init"></a>Terraform init
Začněte `terraform init`spuštěním .

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

[Příkaz terraform init](https://www.terraform.io/docs/commands/init.html) slouží k inicializaci pracovního adresáře obsahujícího konfigurační soubory Terraform. Příkaz `terraform init` je první příkaz, který by měl být spuštěn po napsání nové konfigurace Terraform nebo klonování existující z správy verzí. Je bezpečné spustit tento příkaz vícekrát.

### <a name="terraform-plan"></a>Příkaz terraform plan
Zobrazit náhled prostředků, které mají být `terraform plan`vytvořeny šablonou Terraform s .

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

[Příkaz terraform plan](https://www.terraform.io/docs/commands/plan.html) se používá k vytvoření plánu provádění. Terraform provede aktualizaci, pokud není explicitně zakázána, a pak určí, jaké akce jsou nezbytné k dosažení požadovaného stavu zadaného v konfiguračních souborech. Plán lze uložit pomocí -out a poté se použije terraform, aby bylo zajištěno, že budou provedeny pouze předem naplánované akce.

### <a name="terraform-apply"></a>Příkaz terraform apply
Zřídit prostředky `terraform apply`Azure s .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Příkaz terraform apply](https://www.terraform.io/docs/commands/apply.html) se používá k použití změn potřebných k dosažení požadovaného stavu konfigurace.

### <a name="verify-deployment-with-azure-cli"></a>Ověření nasazení pomocí rozhraní příkazového příkazu Azure
Spuštěním `az group show -n myRgName` ověřte, zda byl prostředek úspěšně zřazen.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Vyčistěte s terraform zničit
Vyčistěte skupinu prostředků vytvořenou [pomocí příkazu Terraform destroy](https://www.terraform.io/docs/commands/destroy.html) a vyčistěte infrastrukturu vytvořenou Terraformem.

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

Úspěšně jste vytvořili prostředek Azure prostřednictvím Terraform. Navštivte další kroky, abyste se mohli dál učit o Cloud Shellu.

## <a name="next-steps"></a>Další kroky
[Další informace o poskytovateli Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash v rychlém startu Cloud Shellu](quickstart.md)
