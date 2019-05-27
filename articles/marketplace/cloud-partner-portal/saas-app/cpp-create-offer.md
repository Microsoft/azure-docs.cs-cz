---
title: Vytvořte nabídku aplikace Azure SaaS | Azure Marketplace
description: Postup vytvoření nabídky aplikací SaaS na Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: pbutlerm
ms.openlocfilehash: 646e9bf844477b3d8e1c4c42fb5956e015805433
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833505"
---
# <a name="create-a-new-saas-application-offer"></a>Vytvořit novou nabídku aplikace SaaS

Tento článek popisuje postup vytvoření a publikování položky nabídky SaaS aplikace (aplikace) pro Azure Marketplace.

> [!IMPORTANT] 
> SaaS nabízejí funkce migruje na [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory).  Všechny nové zdroje musí pomocí partnerského centra pro vytvoření nové nabídky SaaS a správu existující nabídky.  Aktuální vydavatele se nabídky SaaS se batchwise migrují z portál partnerů cloudu do partnerského centra.  Portál partnerů cloudu se zobrazí stavové zprávy k označení, když se konkrétní stávající nabídky migrovaly.


## <a name="offer-process"></a>Nabídka procesu

Následující diagram znázorňuje proces vytvoření nabídku aplikace SaaS.

![Proces vytvoření nabídky SaaS](./media/saas-offer-process-overview.png)

## <a name="offer-components"></a>Nabídka komponenty

Nabídky SaaS aplikace se skládá z pěti oddílů, jsou popsané v následující tabulce:

|  **Skupiny prostředků**   |  **Popis**  |
|  ---------------   |  ---------------  |
|    Nabídka nastavení  |  Slouží ke konfiguraci jedinečné identity pro aplikace SaaS.                 |
|  Technické informace    |  Použít ke konfiguraci typu řešení SaaS a poskytují podrobné informace o připojení pro vaši aplikaci.                |
|  Informace o kanálu      |   Zadejte informace o kanálu, například GTM materiály a kontakty.                |
|  Test Drive        |   Volitelný oddíl pro definování služba, která vám umožní zákazníkům testovat vaši nabídku před může zakoupit.                |
|  Prezentace podrobnosti       | Obsahuje marketing, právních i průběžné správy prostředků a specifikace.  <ul><li> Marketingové prostředky zahrnují název nabídky, popis a loga</li> <li> Právní prostředky zahrnují další právní dokumentace, zásady ochrany osobních údajů a podmínky použití</li>  <li> Můžete určit způsob zpracování vede z portálu pro koncové uživatele webu Azure Marketplace umožňuje zásad průběžné správy.</li> </ul> |
| Kontakty            | Obsahuje informace o podpoře kontaktní údaje a zásady |

## <a name="new-offer-form"></a>Formulář nové nabídky

Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/)a pak vyberte **+ nová nabídka** na panelu nabídky vlevo. Na nové nabídky nabídku, vyberte **aplikace SaaS** nová nabídka formulář pro zobrazení a zahájit proces definice prostředků pro nové nabídky aplikace SaaS.

![Nabídka Nová nabídka pro aplikace SaaS](./media/azure-new-saas-offer.png)

## <a name="next-steps"></a>Další postup

Na stránce Nová nabídka pro typ nabídky SaaS poskytuje sadu karet a pole formuláře, které použijete k vytvoření nové nabídky. Každý z následujících článků vysvětluje, jak karta slouží k definování skupin prostředků a služeb podpory pro nové nabídky.

- [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
- [Karta Technické informace](./cpp-technical-info-tab.md)
- [Karta informací o kanálech](./cpp-channel-info-tab.md)
- [Karta Testovací verze](./cpp-testdrive-tab.md)
- [Karta podrobností o prodejních místech](./cpp-storefront-tab.md)
- [Karta Kontakty](./cpp-contacts-tab.md)
