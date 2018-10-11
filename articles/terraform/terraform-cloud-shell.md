---
title: Použití Terraformu se službou Azure Cloud Shell
description: Použijte Terraform se službou Azure Cloud Shell a zjednodušte si ověřování a konfiguraci šablon.
services: terraform
ms.service: terraform
keywords: terraform, devops, škálovací sada, virtuální počítač, síť, úložiště, moduly
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: a5f44edf201ce9a2e2c0da6b5da9031958a11fdc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959304"
---
# <a name="terraform-cloud-shell-development"></a>Vývoj v Cloud Shellu pomocí Terraformu 

Terraform skvěle spolupracuje s příkazovým řádkem prostředí Bash, jako je Terminál na macOS nebo Bash na Windows nebo v Linuxu. Spuštění konfigurací Terraformu v prostředí Bash služby [Azure Cloud Shell](/azure/cloud-shell/overview) má jedinečné výhody, které urychlí cyklus vývoje.

Tento článek s koncepty se týká funkcí Cloud Shellu, které vám pomohou psát skripty Terraformu k nasazení do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatická konfigurace přihlašovacích údajů

Terraform je v Cloud Shellu nainstalovaný a okamžitě dostupný. Když se přihlásíte do Cloud Shellu, abyste spravovali infrastrukturu bez jakékoli další konfigurace, ověřují se skripty Terraformu v Azure. Automatické ověřování obchází potřebu ručně vytvářet instanční objekt služby Active Directory a konfigurovat proměnné zprostředkovatele Azure Terraform.


## <a name="using-modules-and-providers"></a>Použití modulů a poskytovatelů

Moduly Azure Terraform vyžadují pro získání přístupu k prostředkům v předplatném Azure a pro jejich úpravu přihlašovací údaje. Když pracujete v Cloud Shellu, přidejte do svých skriptů následující kód, díky kterému se budou v Cloud Shellu používat moduly Azure Terraform:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell předá zprostředkovateli `azurerm` požadované hodnoty prostřednictvím proměnných prostředí při použití libovolného příkazu `terraform` rozhraní příkazového řádku.

## <a name="other-cloud-shell-developer-tools"></a>Ostatní vývojářské nástroje Cloud Shellu

Soubory a stavy prostředí mezi relacemi služby Azure Storage a Cloud Shellu přetrvávají. Ke zkopírování a nahrání souborů do Cloud Shellu z místního počítače použijte [Průzkumník služby Azure Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Ve službě Cloud Shell je k dispozici Azure CLI. Je to skvělý nástroj pro testování konfigurací a kontrolu práce po dokončení příkazů `terraform apply` nebo `terraform destroy`.


## <a name="next-steps"></a>Další kroky

[Vytvoření malého clusteru virtuálních počítačů pomocí Registru modulů](terraform-create-vm-cluster-module.md)
[Vytvoření malého clusteru virtuálních počítačů pomocí vlastního HCL](terraform-create-vm-cluster-with-infrastructure.md)
