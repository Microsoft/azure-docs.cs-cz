---
title: Kurz – konfigurace Azure Cloud Shell pro Terraformu
description: V tomto kurzu pomocí Terraformu s Azure Cloud Shell zjednodušete ověřování a konfiguraci šablon.
keywords: Cloud Shell Azure DevOps terraformu
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945339"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Kurz: konfigurace Azure Cloud Shell pro Terraformu

Terraformu funguje dobře z příkazového řádku bash v macOS, Windows nebo Linux. Spouštění konfigurací Terraformu v prostředí bash pro [Azure Cloud Shell](/azure/cloud-shell/overview) má jedinečné výhody. V tomto kurzu se dozvíte, jak psát skripty Terraformu, které se nasazují do Azure pomocí Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Terraform je v Cloud Shellu nainstalovaný a okamžitě dostupný. Terraformu skripty se ověřují v Azure po přihlášení k Cloud Shell ke správě infrastruktury bez jakékoli další konfigurace. Automatické ověřování obchází dva ruční procesy:
- Vytvoření instančního objektu služby Active Directory
- Konfigurace proměnných poskytovatele služby Azure Terraformu


## <a name="use-modules-and-providers"></a>Použití modulů a zprostředkovatelů

Moduly Azure Terraformu vyžadují přihlašovací údaje pro přístup k prostředkům Azure a jejich úpravy. Chcete-li v Cloud Shell použít moduly Terraformu, přidejte následující kód:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Když použijete kterýkoli z příkazů rozhraní příkazového řádku `terraform`, Cloud Shell předá pomocí proměnných prostředí požadované hodnoty pro poskytovatele `azurerm`.

## <a name="other-cloud-shell-developer-tools"></a>Ostatní vývojářské nástroje Cloud Shellu

Soubory a stavy prostředí mezi relacemi služby Azure Storage a Cloud Shellu přetrvávají. Pomocí [Průzkumník služby Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer) můžete kopírovat a nahrávat soubory pro Cloud Shell z místního počítače.

Rozhraní příkazového řádku Azure je dostupné v Cloud Shell a je skvělým nástrojem pro testování konfigurací a kontrole práce po dokončení `terraform apply` nebo `terraform destroy`.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření malého clusteru virtuálních počítačů pomocí registru modulu](terraform-create-vm-cluster-module.md)