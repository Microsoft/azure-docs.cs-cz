---
title: Nabídka nastavení aplikace Azure | Dokumentace Microsoftu
description: Nabídka nastavení pro nabídku aplikací Azure.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 85cdd05de3415637620500cde10adf717bb1e173
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196661"
---
# <a name="azure-application-offer-settings-tab"></a>Karta nabízejí nastavení aplikace Azure

Tento článek popisuje, jak nakonfigurovat nastavení nabídky pro aplikaci Azure.

**Aplikací Azure > Nová nabídka** otevře se stránka s fokusem na **nabízejí nastavení** kartu. Hvězdičku (*) připojeným k názvu pole označuje, že je to požadováno.

![Nabídka Identity formuláře](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>Nabídka nastavení Identity

V části **nabízejí Identity**, je třeba zadat informace pro pole popsaná v následující tabulce.  

|  **Pole**       |     **Popis**                                                          |
|  ---------       |     ---------------                                                          |
| **ID nabídky**       | Jedinečný identifikátor (v rámci profilu vydavatele) pro tuto nabídku. Tento identifikátor se nebude zobrazovat v sestavách adresy URL a přehledů produktů. Má maximální délku než 50 znaků a můžou používat malé alfanumerické znaky a pomlčky (-). (Identifikátor nemůže končit spojovníkem.) **Poznámka:** Toto pole nelze změnit po ukončení nabídky za provozu. <br> Například pokud Contoso publikuje nabídka s ID nabídky **ukázkový kontejner**, jí přiřazena adresa URL Azure Marketplace `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview`. |
| **ID vydavatele**     | Jedinečný identifikátor vaší organizace na webu Azure Marketplace. Všechny nabídky by měly být přidruženy s vaším ID vydavatele. Tuto hodnotu nelze změnit po jejím uložení. |
| **Název**          | Zobrazovaný název nabídky. Tento název se zobrazí na webu Azure Marketplace a na portál Cloud Partner. Může mít maximálně 50 znaků. Doporučujeme používat pro svůj produkt rozpoznat název značky. Pokud je jak produktu na trh, nezahrnujte název vaší organizace. Pokud jsou marketing této nabídky do jiných webových stránek a publikace, ujistěte se, že název je ve všech publikacích přesně stejné. |
|  |  |

Vyberte **Uložit** nabízejí nastavení uložte.

## <a name="next-steps"></a>Další postup

Použití [SKU](./cpp-skus-tab.md) kartu Konfigurace SKU pro vaši nabídku.