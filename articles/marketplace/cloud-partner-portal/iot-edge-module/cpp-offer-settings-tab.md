---
title: Nabízejí nastavení pro modul služby Azure IoT Edge | Dokumentace Microsoftu
description: Konfigurace nastavení nabídky pro modul IoT Edge.
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
ms.openlocfilehash: 2a2ec41d7e2f21dc1550cd3c56009204842e1641
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430753"
---
# <a name="iot-edge-module-offer-settings-tab"></a>Karta nabízejí nastavení modulu IoT Edge

**Moduly IoT Edge > Nová nabídka** otevře se stránka s fokusem na **nabízejí nastavení** kartu. 

![Nová stránka nabídky pro moduly IoT Edge](./media/iot-edge-module-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Nabídka nastavení Identity

V části **nabízejí Identity**, je třeba zadat informace pro pole popsaná v následující tabulce. Hvězdičku (*) připojeným k názvu pole označuje, že je to požadováno. 

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky**       | Jedinečný identifikátor (v rámci profilu vydavatele) pro tuto nabídku. Tento identifikátor se nebude zobrazovat v sestavách adresy URL a přehledů produktů. Má maximální délku než 50 znaků a můžou používat malé alfanumerické znaky a pomlčky (-). (Identifikátor nemůže končit spojovníkem.) **Poznámka:** toto pole nelze změnit po ukončení nabídky za provozu. <br> Například pokud Contoso publikuje nabídka s ID nabídky **-iot-edge – modul**, jí přiřazena adresa URL Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview`. |
| **Publisher**     | Jedinečný identifikátor vaší organizace na webu Azure Marketplace. Všechny nabídky by měly být přidruženy s vaším ID vydavatele. Tuto hodnotu nelze změnit po jejím uložení. |
| **Název**          | Zobrazovaný název nabídky. Tento název se zobrazí na webu Azure Marketplace a na portál Cloud Partner. Může mít maximálně 50 znaků. Doporučujeme používat pro svůj produkt rozpoznat název značky. Pokud je jak produktu na trh, nezahrnujte název vaší organizace. Pokud jsou marketing této nabídky do jiných webových stránek a publikace, ujistěte se, že název je ve všech publikacích přesně stejné. |
|  |  |

Vyberte **Uložit** nabízejí nastavení uložte.

## <a name="next-steps"></a>Další postup

Použití [SKU](./cpp-skus-tab.md) kartu Konfigurace SKU pro vaši nabídku.
