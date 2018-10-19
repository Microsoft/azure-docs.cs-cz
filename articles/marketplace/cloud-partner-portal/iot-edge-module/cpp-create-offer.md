---
title: Vytvoření nabídky modulu Azure IoT Edge | Dokumentace Microsoftu
description: Jak publikovat nový modul IoT Edge pro web Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: d7943119ed29e03afb6b089a913d4ba2baddc166
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430732"
---
# <a name="create-a-new-iot-edge-module-offer-with-the-cloud-partner-portal"></a>Vytvořit novou nabídku modul IoT Edge s portálem Cloud Partner

Tento článek popisuje, jak vytvářet a publikovat položku nabídky modul IoT Edge pro Azure Marketplace. Každé nabídky se zobrazí jako vlastní entity na webu Azure Marketplace a je přidružený nejmíň jeden skladové položky.  V rámci nabídky modul IoT Edge se skládá z následujících seskupení prostředků a služeb podpory:

|  **Skupiny prostředků**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    Skladové položky            |  Nejmenší nasaditelný jednotka nabídky. Jediné nabídky (produktu třídy) může mít více jednotek SKU spojené s nabídkou. SKU můžete použít k rozlišení mezi podporované funkcemi a modely fakturace. |
|  Marketplace       | Obsahuje marketing, právních i průběžné správy prostředků a specifikace.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga</li> <li> Právní prostředky zahrnují další právní dokumentace, zásady ochrany osobních údajů a podmínky použití</li>  <li> Můžete určit způsob zpracování vede z portálu pro koncové uživatele webu Azure Marketplace umožňuje zásad průběžné správy.</li> </ul> |
| Podpora            | Obsahuje informace o podpoře kontaktní údaje a zásady |


## <a name="new-offer-form"></a>Formulář nové nabídky 

Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com/)a pak vyberte **+ nová nabídka** na panelu nabídky vlevo. V nabídce Nová nabídka vyberte **moduly IoT Edge** zobrazíte **nová nabídka** formuláře a zahájit proces definice prostředků pro nové nabídky ioT Edge Module. 

![Nový modul IoT Edge nabízí uživatelské rozhraní výběru](./media/new-iot-edge-module-offer.png)

## <a name="next-steps"></a>Další postup

**Nová nabídka** stránky pro typ nabídky modul IoT Edge poskytuje sadu karet a pole formuláře, které použijete k vytvoření nové nabídky. Každý z následujících článků vysvětluje, jak karta slouží k definování skupin prostředků a služeb podpory pro nové nabídky modul IoT Edge.

- [Karta nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta skladové položky](./cpp-skus-tab.md)
- [Karta Marketplace](./cpp-marketplace-tab.md)
- [Kartu Podpora](./cpp-support-tab.md)
