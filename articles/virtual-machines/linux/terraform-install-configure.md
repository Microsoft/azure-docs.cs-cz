---
title: Instalace a konfigurace Terraformu pro použití s Azure | Dokumentace Microsoftu
description: Zjistěte, jak instalace a konfigurace Terraformu k vytvoření prostředků Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/19/2018
ms.author: echuvyrov
ms.openlocfilehash: 1af96b686a1502d638b4335e22259b79169d1065
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173243"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalace a konfigurace Terraformu ke zřízení virtuálních počítačů a další infrastruktury do Azure
 
Poskytuje snadný způsob, jak definovat, ve verzi preview a nasadit cloudovou infrastrukturu s využitím Terraformu [jednoduchých šablon jazyka](https://www.terraform.io/docs/configuration/syntax.html). Tento článek popisuje nezbytné kroky k Terraformu zřizovat prostředky v Azure.

Další informace o tom, jak pomocí Terraformu v Azure, najdete [Terraformu centra](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform je nainstalovaný ve výchozím nastavení [Cloud Shell](/azure/terraform/terraform-cloud-shell). Pokud se rozhodnete nainstalovat Terraformu místně, dokončení dalšího kroku, v opačném případě pokračujte [nastavit přístup Terraformu pro Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Nainstalujte Terraformu

Chcete-li nainstalovat Terraformu, [Stáhnout](https://www.terraform.io/downloads.html) odpovídající balíček pro váš operační systém do samostatné instalační adresář. Položka ke stažení obsahuje jeden spustitelný soubor, pro kterou byste měli definovat také globální cesta. Pokyny k nastavení cesty na Linuxu a Macu, přejděte na [tuto webovou stránku](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pokyny k nastavení cesty ve Windows, přejděte na [tuto webovou stránku](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Zkontrolujte konfiguraci cesty s `terraform` příkazu. Seznam dostupných možností Terraform je zobrazen jako následující příklad výstupu:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Nastavení přístupu k Terraformu pro Azure

Pokud chcete povolit Terraformu pro zřízení prostředků do Azure, vytvořte [instanční objekt služby Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). Instanční objekt uděluje skripty Terraformu ke zřízení prostředků ve vašem předplatném Azure.

Pokud máte více předplatných Azure, nejprve dotaz vašeho účtu pomocí [zobrazit účet az](/cli/azure/account#az-account-show) k získání ID a tenanta hodnoty ID seznamu předplatného:

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Použít vybrané předplatné, nastavte předplatné pro tuto relaci s [az účet sady](/cli/azure/account#az-account-set). Nastavte `SUBSCRIPTION_ID` prostředí proměnnou pro uchování hodnoty vrácené `id` pole z předplatného, které chcete použít:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nyní můžete vytvořit instanční objekt pro použití s využitím Terraformu. Použití [az ad sp create-for-rbac]/cli/azure/ad/sp#az-ad-sp-create-for-rbac) a nastavte *oboru* do vašeho předplatného následujícím způsobem:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vaše *appId*, *heslo*, *sp_name*, a *tenanta* jsou vráceny. Poznamenejte si *appId* a *password*.

## <a name="configure-terraform-environment-variables"></a>Konfigurace proměnných prostředí Terraformu

Nakonfigurujte Terraform, který pomocí instančního objektu služby Azure AD, nastavte následující proměnné prostředí, které jsou pak používány [moduly Terraformu pro Azure](https://registry.terraform.io/modules/Azure). Můžete také nastavit prostředí práci s cloudu Azure jiné než Azure public.

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Následující ukázkový skript prostředí můžete použít k nastavení těchto proměnných:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Spustit ukázkový skript

Vytvořte soubor `test.tf` prázdný adresář a vložte následující skript.

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Uložení souboru, kterou následně inicializujete Terraformu nasazení. Tento krok stáhne moduly Azure, který je potřebný k vytvoření skupiny prostředků Azure.

```bash
terraform init
```

Výstup se podobá následujícímu příkladu:

```bash
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Můžete zobrazit náhled akce provést skript Terraform s `terraform plan`. Až budete připraveni vytvořit skupinu prostředků, použije plán Terraformu takto:

```bash
terraform apply
```

Výstup se podobá následujícímu příkladu:

```bash
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Další postup

V tomto článku jste nainstalovali Terraformu nebo službě Cloud Shell umožňuje nakonfigurovat přihlašovací údaje Azure a začněte vytvářet prostředky v předplatném Azure. Vytvoření kompletní nasazení Terraformu v Azure, najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače Azure s využitím Terraformu](terraform-create-complete-vm.md)