---
title: Kurz – konfigurace Azure Cloud Shell pro Terraformu
description: Použijte Terraform se službou Azure Cloud Shell a zjednodušte si ověřování a konfiguraci šablon.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 693ed462fb1ba3dfed079e8ae97152732c771253
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969591"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Kurz: konfigurace Azure Cloud Shell pro Terraformu

Terraformu funguje dobře z příkazového řádku bash v macOS, Windows nebo Linux. Spouštění konfigurací Terraformu v prostředí bash pro [Azure Cloud Shell](/azure/cloud-shell/overview) má jedinečné výhody. V tomto kurzu se dozvíte, jak psát skripty Terraformu, které se nasazují do Azure pomocí Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Terraform je v Cloud Shellu nainstalovaný a okamžitě dostupný. Když se přihlásíte do Cloud Shellu, abyste spravovali infrastrukturu bez jakékoli další konfigurace, ověřují se skripty Terraformu v Azure. Automatické ověřování obchází dva ruční procesy:
- Vytvoření instančního objektu služby Active Directory.
- konfigurace proměnných poskytovatele služby Azure Terraformu.


## <a name="using-modules-and-providers"></a>Použití modulů a poskytovatelů

Moduly Azure Terraformu vyžadují přihlašovací údaje pro přístup k prostředkům Azure a jejich úpravy. Chcete-li v Cloud Shell použít moduly Terraformu, přidejte následující kód:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell předá zprostředkovateli `azurerm` požadované hodnoty prostřednictvím proměnných prostředí při použití libovolného příkazu `terraform` rozhraní příkazového řádku.

## <a name="other-cloud-shell-developer-tools"></a>Ostatní vývojářské nástroje Cloud Shellu

Soubory a stavy prostředí mezi relacemi služby Azure Storage a Cloud Shellu přetrvávají. Ke zkopírování a nahrání souborů do Cloud Shellu z místního počítače použijte [Průzkumník služby Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Ve službě Cloud Shell je k dispozici Azure CLI. Je to skvělý nástroj pro testování konfigurací a kontrolu práce po dokončení příkazů `terraform apply` nebo `terraform destroy`.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření malého clusteru virtuálních počítačů pomocí registru modulu](terraform-create-vm-cluster-module.md)