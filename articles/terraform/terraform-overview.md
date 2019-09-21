---
title: Použití Terraformu v Azure
description: Seznámení s používáním Terraformu k nasazení a nasazení infrastruktury Azure.
services: terraform
ms.service: azure
keywords: terraform, devops, přehled, plánování, použití, automatizace
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 4e51b8b5aa1cd00fcdd2481d73c40458d01d38c4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173065"
---
# <a name="terraform-with-azure"></a>Terraform v Azure

[Terraform od společnosti HashiCorp](https://www.terraform.io/) je opensourcový nástroj sloužící ke zřizování a správě cloudové infrastruktury. Kodifikuje infrastrukturu v konfiguračních souborech, které popisují topologii cloudových prostředků, jako jsou virtuální počítače, účty úložiště a síťová rozhraní. Rozhraní příkazového řádku nástroje Terraform poskytuje jednoduchý mechanismus k nasazování konfiguračních souborů do Azure nebo jiného podporovaného cloudu a správě jejich verzí.

Tento článek popisuje výhody použití Terraformu ke správě infrastruktury Azure.

## <a name="automate-infrastructure-management"></a>Automatizujte správu infrastruktury.

Konfigurační soubory nástroje Terraform založené na šablonách vám umožňují definovat, zřizovat a konfigurovat prostředky opakovatelným a předvídatelným způsobem. Automatizace infrastruktury má několik výhod:

- Snižuje riziko lidských chyb při nasazování a správě infrastruktury.
- Vícekrát nasadí stejnou šablonu a vytvoří tak identická vývojová, testovací a produkční prostředí.
- Snižuje náklady na vývojová a testovací prostředí tím, že je vytváří na vyžádání.

## <a name="understand-infrastructure-changes-before-they-are-applied"></a>Pochopení změn v infrastruktuře před jejich nasazením 

Čím je topologie prostředku složitější, tím obtížnější může být pochopení významu a důsledků změn v infrastruktuře.

Terraform poskytuje rozhraní příkazového řádku, které umožňuje uživatelům ověřit změny v infrastruktuře před samotným nasazením a zobrazit jejich náhled. Náhled na změny v infrastruktuře bezpečným a produktivním způsobem má několik výhod:
- Když členové týmu rychle pochopí navrhované změny a jejich dopad, mohou spolupracovat efektivněji.
- Nežádoucí změny lze zachytit v rané fázi vývoje.


## <a name="deploy-infrastructure-to-multiple-clouds"></a>Nasazení infrastruktury do několika cloudů

Terraform je oblíbeným nástrojem pro multicloudové scénáře, kdy se nasadí podobná infrastruktura do Azure a do dalších cloudů od poskytovatelů cloudových služeb nebo do místních datacenter. Vývojářům to umožňuje používat ke správě infrastruktury u několika poskytovatelů cloudových služeb stejné nástroje a konfigurační soubory.

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled a výhody nástroje Terraform a tady jsou doporučené další kroky:

- Začněte [instalací nástroje Terraform a jeho konfigurací, aby používal Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure).
- [Vytvořte pomocí Terraformu virtuální počítač Azure](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-create-complete-vm).
- Prozkoumejte [modul Azure Resource Manageru pro Terraform](https://www.terraform.io/docs/providers/azurerm/). 