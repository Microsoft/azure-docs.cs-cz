---
title: Terraform pomocí prostředí cloudu Azure
description: Použití Terraform s prostředí cloudu Azure ke zjednodušení ověřování a konfigurace šablony.
keywords: terraform, devops, škálovat nastaveno, virtuální počítače, sítě, úložiště, moduly
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5157066086f1bdfa580c1946942bda4505e48935
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29121521"
---
# <a name="terraform-cloud-shell-development"></a>Vývoj Terraform cloudové prostředí 

Terraform o praktickou z příkazového řádku Bash například systému macOS Terminálové nebo Bash v systému Windows nebo Linux. Spuštění vaší Terraform činnost konfigurace v Bash s [prostředí cloudu Azure](/azure/cloud-shell/overview) má několik výhod jedinečný pro urychlení vývojový cyklus.

Tento článek koncepty se zabývá cloudové prostředí, funkce, které vám pomůžou zápisu Terraform skripty, které nasazení do Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Konfiguraci automatického přihlašovacích údajů

Terraform je nainstalovaná a okamžitě k dispozici v prostředí cloudu. Skripty Terraform ověření pomocí Azure při přihlášení k prostředí cloudu ke správě infrastruktury bez jakékoli dodatečné konfigurace. Automatické ověření obchází potřeba ručně vytvořit objekt služby Active Directory a nakonfigurovat proměnné zprostředkovatele Azure Terraform.


## <a name="using-modules-and-providers"></a>Použití modulů a zprostředkovatelů

Moduly Azure Terraform vyžadovat přihlašovací údaje pro přístup a provést změny k prostředkům ve vašem předplatném Azure. Při práci v prostředí cloudu, přidejte následující kód do skriptů pro použití Azure Terraform moduly v prostředí cloudu:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Prostředí cloudu předá požadované hodnoty `azurerm` zprostředkovatele prostřednictvím proměnné prostředí při použití některého z `terraform` rozhraní příkazového řádku.

## <a name="other-cloud-shell-developer-tools"></a>Další nástroje pro vývojáře cloudové prostředí

Soubory a stavy prostředí uchová mezi relacemi cloudové prostředí ve službě Azure Storage. Použití [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) pro kopírování a nahrání souborů do cloudu prostředí z místního počítače.

Azure CLI 2.0 je k dispozici v prostředí cloudu a je skvělý nástroj pro testování konfigurace a práce po kontrole `terraform apply` nebo `terraform destroy` dokončení.


## <a name="next-steps"></a>Další postup

[Vytvořit cluster malých virtuálních počítačů pomocí klíče registru modulu](terraform-create-vm-cluster-module.md)
[vytvořit cluster malých virtuálních počítačů pomocí vlastní kompatibilního hardwaru](terraform-create-vm-cluster-with-infrastructure.md)
