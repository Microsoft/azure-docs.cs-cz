---
title: Kurz – konfigurace prostředí Azure Cloud Shell pro Terraform
description: V tomto kurzu pomocí Terraform s Azure Cloud Shell zjednodušit ověřování a konfiguraci šablony.
keywords: azure devops terraform cloud shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945339"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Kurz: Konfigurace prostředí Azure Cloud Shell pro Terraform

Terraform funguje dobře z příkazového řádku Bash v systému macOS, Windows nebo Linuxu. Spuštění konfigurace Terraform v bash prostředí [Azure Cloud Shell](/azure/cloud-shell/overview) má některé jedinečné výhody. Tento kurz ukazuje, jak psát terraformskripty, které se nasazují do Azure pomocí Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Terraform je v Cloud Shellu nainstalovaný a okamžitě dostupný. Terraform skripty ověřit s Azure při přihlášení do Cloud Shell pro správu infrastruktury bez jakékoli další konfigurace. Automatické ověřování obchází dva ruční procesy:
- Vytvoření objektu zabezpečení služby Active Directory
- Konfigurace proměnných zprostředkovatele Azure Terraform


## <a name="use-modules-and-providers"></a>Použití modulů a poskytovatelů

Moduly Azure Terraform vyžadují přihlašovací údaje pro přístup a úpravy prostředků Azure. Chcete-li v prostředí Cloud Shell používat moduly Terraform, přidejte následující kód:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell předává `azurerm` požadované hodnoty pro zprostředkovatele prostřednictvím `terraform` proměnných prostředí při použití některého z příkazů příkazového příkazu.

## <a name="other-cloud-shell-developer-tools"></a>Ostatní vývojářské nástroje Cloud Shellu

Soubory a stavy prostředí mezi relacemi služby Azure Storage a Cloud Shellu přetrvávají. Pomocí [Průzkumníka úložiště Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) zkopírujte a nahrajte soubory do Cloud Shellu z místního počítače.

Rozhraní příkazového odpočitatelů Azure je k dispozici v prostředí `terraform apply` `terraform destroy` Cloud Shell a je skvělý nástroj pro testování konfigurací a kontrolu vaší práce po dokončení nebo dokončení.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření malého clusteru virtuálních montovny pomocí registru modulu](terraform-create-vm-cluster-module.md)