---
title: Použití Terraformu v Azure
description: Seznámení s používáním Terraformu k nasazení a nasazení infrastruktury Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472158"
---
# <a name="terraform-with-azure"></a>Terraform v Azure

[Terraform od společnosti HashiCorp](https://www.terraform.io/) je opensourcový nástroj sloužící ke zřizování a správě cloudové infrastruktury. Kodifikovaly infrastrukturu v konfiguračních souborech, které popisují topologii cloudových prostředků. Mezi tyto prostředky patří virtuální počítače, účty úložiště a síťová rozhraní. Terraformu CLI poskytuje jednoduchý mechanismus pro nasazování a správu konfiguračních souborů do Azure.

Tento článek popisuje výhody použití Terraformu ke správě infrastruktury Azure.

## <a name="automate-infrastructure-management"></a>Automatizujte správu infrastruktury.

Konfigurační soubory nástroje Terraform založené na šablonách vám umožňují definovat, zřizovat a konfigurovat prostředky opakovatelným a předvídatelným způsobem. Automatizace infrastruktury má několik výhod:

- Snižuje riziko lidských chyb při nasazování a správě infrastruktury.
- Vícekrát nasadí stejnou šablonu a vytvoří tak identická vývojová, testovací a produkční prostředí.
- Snižuje náklady na vývojová a testovací prostředí tím, že je vytváří na vyžádání.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Porozumění změnám infrastruktury před použitím

Čím je topologie prostředku složitější, tím obtížnější může být pochopení významu a důsledků změn v infrastruktuře.

Rozhraní příkazového řádku Terraformu umožňuje uživatelům ověřit a zobrazit náhled změn infrastruktury před aplikací. Bezpečný přístup ke změnám infrastruktury má několik výhod:
- Když členové týmu rychle pochopí navrhované změny a jejich dopad, mohou spolupracovat efektivněji.
- Nežádoucí změny lze zachytit v rané fázi vývoje.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Nasazení infrastruktury do několika cloudů

Terraformu je dobře při nasazení infrastruktury napříč více poskytovateli cloudu. Umožňuje vývojářům pomocí konzistentních nástrojů spravovat jednotlivé definice infrastruktury.

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled a výhody nástroje Terraform a tady jsou doporučené další kroky:

- Začněte [instalací nástroje Terraform a jeho konfigurací, aby používal Azure](terraform-install-configure.md).
- [Vytvořte pomocí Terraformu virtuální počítač Azure](terraform-create-complete-vm.md).
- Prozkoumejte [modul Azure Resource Manageru pro Terraform](https://www.terraform.io/docs/providers/azurerm/). 