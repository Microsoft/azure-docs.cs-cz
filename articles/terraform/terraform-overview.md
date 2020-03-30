---
title: Použití Terraformu v Azure
description: Úvod k použití Terraform pro verzi a nasazení infrastruktury Azure.
ms.topic: overview
ms.date: 10/26/2019
ms.openlocfilehash: 05b92fdf8c0a0f84d2f29b4aa7479850b2721441
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77472158"
---
# <a name="terraform-with-azure"></a>Terraform v Azure

[Terraform od společnosti HashiCorp](https://www.terraform.io/) je opensourcový nástroj sloužící ke zřizování a správě cloudové infrastruktury. Kodifikuje infrastrukturu v konfiguračních souborech, které popisují topologii cloudových prostředků. Tyto prostředky zahrnují virtuální počítače, účty úložiště a síťová rozhraní. Terraform CLI poskytuje jednoduchý mechanismus pro nasazení a verzi konfiguračních souborů do Azure.

Tento článek popisuje výhody použití Terraformu ke správě infrastruktury Azure.

## <a name="automate-infrastructure-management"></a>Automatizujte správu infrastruktury.

Konfigurační soubory nástroje Terraform založené na šablonách vám umožňují definovat, zřizovat a konfigurovat prostředky opakovatelným a předvídatelným způsobem. Automatizace infrastruktury má několik výhod:

- Snižuje riziko lidských chyb při nasazování a správě infrastruktury.
- Vícekrát nasadí stejnou šablonu a vytvoří tak identická vývojová, testovací a produkční prostředí.
- Snižuje náklady na vývojová a testovací prostředí tím, že je vytváří na vyžádání.

## <a name="understand-infrastructure-changes-before-being-applied"></a>Vysvětlení změn infrastruktury před použitím

Čím je topologie prostředku složitější, tím obtížnější může být pochopení významu a důsledků změn v infrastruktuře.

Terraform CLI umožňuje uživatelům ověřit a zobrazit náhled změny infrastruktury před aplikací. Bezpečné zobrazení náhledu změn infrastruktury má několik výhod:
- Když členové týmu rychle pochopí navrhované změny a jejich dopad, mohou spolupracovat efektivněji.
- Nežádoucí změny lze zachytit v rané fázi vývoje.

## <a name="deploy-infrastructure-to-multiple-clouds"></a>Nasazení infrastruktury do několika cloudů

Terraform je zběhlý v nasazování infrastruktury mezi více poskytovateli cloudu. Umožňuje vývojářům používat konzistentní nástroje pro správu jednotlivých definic infrastruktury.

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled a výhody nástroje Terraform a tady jsou doporučené další kroky:

- Začněte [instalací nástroje Terraform a jeho konfigurací, aby používal Azure](terraform-install-configure.md).
- [Vytvořte pomocí Terraformu virtuální počítač Azure](terraform-create-complete-vm.md).
- Prozkoumejte [modul Azure Resource Manageru pro Terraform](https://www.terraform.io/docs/providers/azurerm/). 