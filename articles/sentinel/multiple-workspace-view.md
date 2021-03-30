---
title: Práce s incidenty Sentinel Azure v mnoha pracovních prostorech najednou | Microsoft Docs
description: Jak zobrazit incidenty ve více pracovních prostorech současně ve službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2020
ms.author: yelevin
ms.openlocfilehash: 448998328ff15b74b0aa0b17e2435a7ff55c54a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83124163"
---
# <a name="work-with-incidents-in-many-workspaces-at-once"></a>Práce s incidenty v mnoha pracovních prostorech najednou 

 Aby bylo možné plně využít možnosti služby Azure Sentinel, Microsoft doporučuje použití prostředí s jedním pracovním prostorem. Existují však případy použití, které vyžadují, abyste měli několik pracovních prostorů, v některých případech – například u [poskytovatele spravovaného zabezpečení (MSSP)](./multiple-tenants-service-providers.md) i jeho zákazníky – napříč více klienty. **Zobrazení více pracovních prostorů** umožňuje zobrazit a pracovat s incidenty zabezpečení napříč několika pracovními prostory ve stejnou dobu, dokonce i v rámci klientů, což vám umožní udržet plnou viditelnost a kontrolu nad odezvou zabezpečení vaší organizace.

## <a name="entering-multiple-workspace-view"></a>Zadání více zobrazení pracovního prostoru

Když otevřete Azure Sentinel, zobrazí se seznam všech pracovních prostorů, ke kterým máte přístupová práva, ve všech vybraných klientech a předplatných. Vlevo od názvu pracovního prostoru je zaškrtávací políčko. Kliknutím na název jednoho pracovního prostoru vás přinesete do tohoto pracovního prostoru. Pokud chcete vybrat několik pracovních prostorů, klikněte na všechna příslušná zaškrtávací políčka a potom v horní části stránky klikněte na tlačítko **zobrazení více pracovních prostorů** .

> [!IMPORTANT]
> Více zobrazení pracovního prostoru aktuálně podporuje maximálně 10 souběžně zobrazených pracovních prostorů. 
> 
> Pokud zkontrolujete více než 10 pracovních prostorů, zobrazí se varovná zpráva.

Všimněte si, že v seznamu pracovních prostorů vidíte adresář, předplatné, umístění a skupinu prostředků přidružené k jednotlivým pracovním prostorům. Adresář odpovídá tenantovi.

   ![Výběr několika pracovních prostorů](./media/multiple-workspace-view/workspaces.png)

## <a name="working-with-incidents"></a>Práce s incidenty

V **zobrazení více pracovních prostorů** je teď k dispozici jenom obrazovka **incidentů** . Vypadá a funguje podobně jako obrazovka běžné **incidenty** . K dispozici je několik důležitých rozdílů, i když:

   ![Zobrazení incidentů ve více pracovních prostorech](./media/multiple-workspace-view/incidents.png)

- Čítače v horní části *incidentů otevření* stránky, *nových incidentů* *, probíhajících* atd. – zobrazí souhrnná čísla všech vybraných pracovních prostorů.

- V jednom sjednoceném seznamu se zobrazí incidenty ze všech vybraných pracovních prostorů a adresářů (klientů). Kromě filtrů z obrazovky běžné **incidenty** můžete filtrovat seznam podle pracovního prostoru a adresáře.

- Musíte mít oprávnění ke čtení a zápisu ve všech pracovních prostorech, ze kterých jste vybrali incidenty. Pokud máte v některých pracovních prostorech jenom oprávnění ke čtení, zobrazí se zpráva s upozorněním, pokud v těchto pracovních prostorech vyberete incidenty. Nebudete moct upravovat tyto incidenty ani jiné, které jste si zvolili společně, a to ani v případě, že máte oprávnění pro ostatní.

- Pokud zvolíte jeden incident a kliknete na **Zobrazit úplné podrobnosti** nebo **prozkoumat**, pak budete mít v kontextu dat pracovního prostoru dané incidenty a žádné další.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se naučili, jak zobrazit a pracovat s incidenty ve více pracovních prostorech služby Azure Sentinel současně. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

