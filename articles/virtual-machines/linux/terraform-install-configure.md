---
title: Instalace a konfigurace Terraformu pro zřizování prostředků Azure | Microsoft Docs
description: Naučte se instalovat a konfigurovat Terraformu k vytváření prostředků Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: cd3c8d7d862788f626356b4cfcdccccca36227b3
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168730"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Instalace a konfigurace Terraformu pro zřizování prostředků Azure
 
Terraformu poskytuje snadný způsob, jak definovat, zobrazovat náhled a nasazovat cloudovou infrastrukturu pomocí [jednoduchého jazyka šablonování](https://www.terraform.io/docs/configuration/syntax.html). Tento článek popisuje nezbytné kroky pro použití Terraformu ke zřízení prostředků v Azure.

Další informace o tom, jak používat Terraformu s Azure, najdete v [centru terraformu](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraformu se ve výchozím nastavení instaluje v [Cloud Shell](/azure/terraform/terraform-cloud-shell). Pokud se rozhodnete nainstalovat Terraformu místně, dokončete další krok, jinak pokračujte v [Nastavení terraformu přístupu k Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Nainstalovat Terraformu

Pro instalaci Terraformu [Stáhněte](https://www.terraform.io/downloads.html) příslušný balíček pro váš operační systém do samostatného instalačního adresáře. Stažení obsahuje jeden spustitelný soubor, pro který byste také měli definovat globální cestu. Pokyny, jak nastavit cestu pro Linux a Mac, najdete na [této webové stránce](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pokyny, jak nastavit cestu ve Windows, najdete na [této webové stránce](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Ověřte konfiguraci cesty pomocí `terraform` příkazu. Zobrazí se seznam dostupných možností Terraformu, jako v následujícím příkladu výstupu:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Nastavení přístupu k Terraformu do Azure

Pokud chcete povolit Terraformu k zřizování prostředků do Azure, vytvořte [instanční objekt služby Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). Instanční objekt uděluje vašim skriptům Terraformu možnost zřídit prostředky v předplatném Azure.

Pokud máte několik předplatných Azure, nejdřív Dotazujte svůj účet pomocí příkazu [AZ Account list](/cli/azure/account#az-account-list) , abyste získali seznam ID předplatného a ID tenanta:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Chcete-li použít vybrané předplatné, nastavte odběr této relace pomocí [AZ Account set](/cli/azure/account#az-account-set). Nastavte proměnnou `id` prostředí tak, aby obsahovala hodnotu vráceného pole z předplatného, které chcete použít: `SUBSCRIPTION_ID`

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Nyní můžete vytvořit instanční objekt pro použití s Terraformu. Použijte příkaz [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)a nastavte *obor* na vaše předplatné následujícím způsobem:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Vrátí `appId`se `password`vaše ,`sp_name`, a`tenant` . Poznamenejte `appId` si a `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurace proměnných prostředí Terraformu

Pokud chcete nakonfigurovat Terraformu pro použití instančního objektu služby Azure AD, nastavte následující proměnné prostředí, které jsou pak používány [moduly Azure terraformu](https://registry.terraform.io/modules/Azure). Prostředí můžete také nastavit, pokud pracujete s jiným cloudem Azure, než je Azure Public.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

K nastavení těchto proměnných můžete použít následující vzorový skript prostředí:

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

## <a name="run-a-sample-script"></a>Spuštění ukázkového skriptu

Vytvořte soubor v `test.tf` prázdném adresáři a vložte ho do následujícího skriptu.

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Uložte soubor a pak inicializujte nasazení Terraformu. Tento krok stáhne moduly Azure vyžadované k vytvoření skupiny prostředků Azure.

```bash
terraform init
```

Výstup se podobá následujícímu příkladu:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Můžete zobrazit náhled akcí, které má skript Terraformu provést, pomocí `terraform plan`. Až budete připraveni vytvořit skupinu prostředků, použijte plán Terraformu následujícím způsobem:

```bash
terraform apply
```

Výstup se podobá následujícímu příkladu:

```console
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

## <a name="next-steps"></a>Další kroky

V tomto článku jste nainstalovali Terraformu nebo použili Cloud Shell ke konfiguraci přihlašovacích údajů Azure a zahájení vytváření prostředků ve vašem předplatném Azure. Pokud chcete vytvořit úplnější Terraformu nasazení v Azure, přečtěte si následující článek:

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače Azure pomocí Terraformu](terraform-create-complete-vm.md)
