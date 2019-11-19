---
title: Kurz – konfigurace Azure Cloud Shell pro Terraformu
description: Použijte Terraform se službou Azure Cloud Shell a zjednodušte si ověřování a konfiguraci šablon.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159123"
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
}
```

Když použijete kterýkoli z příkazů rozhraní příkazového řádku `terraform`, Cloud Shell předá pomocí proměnných prostředí požadované hodnoty pro poskytovatele `azurerm`.

## <a name="other-cloud-shell-developer-tools"></a>Ostatní vývojářské nástroje Cloud Shellu

Soubory a stavy prostředí mezi relacemi služby Azure Storage a Cloud Shellu přetrvávají. Pomocí [Průzkumník služby Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer) můžete kopírovat a nahrávat soubory pro Cloud Shell z místního počítače.

Rozhraní příkazového řádku Azure je dostupné v Cloud Shell a je skvělým nástrojem pro testování konfigurací a kontrole práce po dokončení `terraform apply` nebo `terraform destroy`.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření malého clusteru virtuálních počítačů pomocí registru modulu](terraform-create-vm-cluster-module.md)
