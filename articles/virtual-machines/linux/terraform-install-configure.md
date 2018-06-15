---
title: Instalace a konfigurace Terraform ke zřízení virtuálních počítačů a další infrastrukturou v Azure | Microsoft Docs
description: Zjistěte, jak nainstalovat a nakonfigurovat Terraform vytváření prostředků Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: dada9c70eef2adb2704e276a5401509581e37538
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399164"
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalace a konfigurace Terraform ke zřízení virtuálních počítačů a další infrastrukturou do Azure
 
Terraform poskytuje snadný způsob, jak definovat, náhled a nasazení infrastruktury cloudu pomocí [jednoduché ukázka jazyk](https://www.terraform.io/docs/configuration/syntax.html). Tento článek popisuje potřebné kroky k Terraform přidělení prostředků v Azure. 

> [!TIP]
Další informace o tom, jak používat Terraform se službou Azure, najdete [Terraform rozbočovače](/azure/terraform). Terraform je nainstalována ve výchozím nastavení [cloudové prostředí](/azure/terraform/terraform-cloud-shell). Pomocí cloudové prostředí, můžete přeskočit instalace nebo části tohoto dokumentu.

## <a name="install-terraform"></a>Nainstalujte Terraform

Chcete-li nainstalovat Terraform, [Stáhnout](https://www.terraform.io/downloads.html) balíčku pro váš operační systém do samostatné instalační adresář. Stahování obsahuje jeden spustitelný soubor, pro které byste měli také definovat globální cestu. Pokyny o tom, jak nastavit cestu, na Linuxu a Macu, přejděte na [tato webová stránka](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Pokyny o tom, jak nastavit cestu, v systému Windows, přejděte na [tato webová stránka](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Ověřte konfiguraci cestu `terraform` příkaz. Zobrazí seznam dostupných možností Terraform jako výstup:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Nastavení přístupu k Terraform do Azure

Konfigurace [objektu služby Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli) umožňující Terraform zřídit prostředky do Azure. Instanční objekt uděluje Terraform skripty pomocí pověření pro zřízení prostředků ve vašem předplatném Azure.

Existuje několik způsobů, jak vytvořit objekt aplikaci Azure AD a službou Azure AD. Nejjednodušší a nejrychlejší způsob dnes je použití Azure CLI 2.0, který [můžete stáhnout a nainstalovat na Windows, Linuxu nebo Macu](/cli/azure/install-azure-cli).

Přihlaste se ke správě vašeho předplatného Azure po vydání následujícího příkazu:

   `az login`

Pokud máte víc předplatných Azure, jsou jejich podrobnosti vrácený `az login` příkaz. Nastavte `SUBSCRIPTION_ID` proměnnou prostředí pro uložení hodnota vrácený `id` pole z předplatného, kterou chcete použít. 

Nastavte předplatné, které chcete použít pro tuto relaci.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Dotaz na účet, který chcete získat předplatné hodnoty ID ID a klienta.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Dále vytvořte samostatné přihlašovací údaje pro Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

AppId, heslo, sp_name a klienta se vrátí. Poznamenejte si ID aplikace a heslo.

K testování přihlašovacích údajů, otevřete nové prostředí a spusťte následující příkaz, pomocí vrácených hodnot pro sp_name, heslo a klienta:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Konfigurace Terraform proměnné prostředí

Nakonfigurujte Terraform použít ID klienta, ID předplatného, ID klienta a tajný z objektu služby klienta při vytváření prostředků Azure. Prostředí můžete také nastavit, pokud práce Azure cloud jiné než Azure veřejné. Nastavte následující proměnné prostředí, které se používají automaticky pomocí [moduly Azure Terraform](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID
- ARM_ENVIRONMENT

Chcete-li nastavit tyto proměnné můžete použít tento ukázkový skript prostředí:

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

Vytvořte soubor `test.tf` do prázdného adresáře a vložte následující skript. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Uložte tento soubor a pak spusťte `terraform init`. Tento příkaz stáhne Azure moduly potřebnými pro vytvoření skupiny prostředků Azure. Zobrazí se následující výstup:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Náhled skript s `terraform plan`a poté vytvořit `testResouceGroup` skupina prostředků se `terraform apply`:

```
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

Instalaci Terraform a nakonfigurovat přihlašovací údaje Azure, takže můžete začít nasazovat infrastruktury do vašeho předplatného Azure. Potom otestovat instalaci aplikace tak, že vytvoříte skupinu prázdný prostředků Azure.

> [!div class="nextstepaction"]
> [Vytvořte virtuální počítač Azure s Terraform](terraform-create-complete-vm.md)

