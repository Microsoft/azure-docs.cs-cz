---
title: Vytvořit nabídku aplikace Power BI – Azure Marketplace | Dokumentace Microsoftu
description: Postup vytvoření nabídky aplikace Power BI pro webu Microsoft Marketplace AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: 6a4f7daa337618278c3652fad3053c20557a9e28
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414684"
---
# <a name="create-a-power-bi-app-offer"></a>Vytvořte nabídku aplikace Power BI

V tomto článku jsou uvedené kroky pro vytvoření nové nabídky aplikace Power BI pro [AppSource](https://appsource.microsoft.com). Každé nabídky se zobrazí jako vlastní entity v AppSource. Když vytvoříte novou nabídku v [portál partnerů cloudu](https://cloudpartner.azure.com/), je nutné zadat čtyři skupiny prostředků pro vaši nabídku.

Skupiny prostředků jsou popsány v následující tabulce:

|   Skupiny prostředků      | Popis                                                                         |
| ----------------   | ----------------                                                                    |
| Nabídka nastavení     | Primární identifikace a název nabídky.                                      |
| Technické informace     | Adresa URL instalační program, který používáte k instalaci aplikace v pracovním prostoru klienta Power BI. Další informace o tom, jak vygenerovat tuto adresu URL najdete v tématu [dokumentace k aplikaci Power BI](https://go.microsoft.com/fwlink/?linkid=2028636). |
| Prezentace podrobnosti | Obsahuje marketing, právní a může vést správy prostředků. Marketingové prostředky i popis nabídky a loga. Právní prostředky zahrnují další právní dokumentace, zásady ochrany osobních údajů a podmínky použití. Zásady správy zájemce umožňuje určit způsob zpracování vede na portálu user portal AppSource. |
| Kontakty           | Obsahuje informace o podpoře kontaktní údaje a zásady.                                     |

## <a name="new-offer-form"></a>Formulář nové nabídky

Po přihlášení na portál partnerů cloudu vyberte **nová nabídka** v levém podokně. Vyberte formulář Nová nabídka pro zobrazení a zahájit proces definice prostředků pro nové nabídky aplikace, **aplikace Power BI**.

![Položka nabídky Nabídka Power BI](./media/new-offer-menu.png)

> [!NOTE] 
> Pokud **aplikace Power BI** možnost není zobrazena, nebo není povoleno, váš účet nemá oprávnění vytvořit tento typ nabídky. Ujistěte se, že jste splnili veškeré [požadavky](./cpp-prerequisites.md) pro tento typ nabídky, včetně registrace pro účet pro vývojáře.


## <a name="next-steps"></a>Další postup

Následující články popisují karty na **nová nabídka** stránky pro typ nabídky aplikace Power BI. Každý článek popisuje skupiny prostředků a služeb podpory pro nové nabídky aplikace Power BI.

-  [Karta Nastavení nabídky](./cpp-offer-settings-tab.md)
-  [Karta Technické informace](./cpp-technical-info-tab.md)
-  [Karta podrobností o prodejních místech](./cpp-storefront-details-tab.md)
-  [Karta Kontakty](./cpp-contacts-tab.md)
