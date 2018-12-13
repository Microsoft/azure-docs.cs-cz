---
title: Přehled Service Health | Dokumentace Microsoftu
description: Individuální informace o vlivu aplikacemi Azure tak, že problémů s aktuální a budoucí Azure služby a údržbu.
services: Resource health
documentationcenter: ''
author: rboucher
manager: ''
editor: ''
ms.assetid: ''
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: robb
ms.openlocfilehash: aa7a925255b0c30b84c3b32bb53ba3f31c11fa6b
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162286"
---
# <a name="service-health"></a>Service Health
Service Health vám poskytne přizpůsobitelný řídicí panel, který sleduje stav služby Azure v oblastech, kde je používáte. V tomto řídicím panelu můžete sledovat aktivní události, jako jsou problémy s probíhající služby, nadcházející plánované údržbě nebo poradci pro stav relevantní. Když nenechávali události, získejte vystavili v historii stavu po dobu 90 dnů. A konečně mohou pomocí řídicího panelu stavu služby k vytváření a správě výstrah stavu služby, které proaktivně upozorňují na problémy se službou ovlivňují.

## <a name="service-health-events"></a>Události služby Service Health
Service Health sleduje tři typy událostí stavu, které může mít vliv na vaše prostředky:
1. **Služba problémy** – problémy ve službách Azure, které teď se vás týkají. 
2. **Plánovaná údržba** -nadcházející údržby, které můžou ovlivnit dostupnost vašich služeb v budoucnu.  
3. **Poradci pro stav** – změny ve službách Azure, které vyžadují vaši pozornost. Mezi příklady patří, když jsou zastaralé funkce Azure, nebo pokud překročí kvótu využití.

## <a name="get-started-with-service-health"></a>Začínáme se službou Service Health
Spustit řídicí panel stavu služby, vyberte dlaždici Service Health na řídicí panel portálu. Pokud jste předtím odebrali dlaždice nebo používáte vlastní řídicí panel, vyhledejte službu Health service v "Další služby" (dolní levé straně na řídicí panel).

![Začínáme se službou Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Podívejte se na aktuální problémy, které bude mít vliv na vaše služby
**Služby problémy** zobrazí všechny probíhající problémy ve službách Azure, které mají vliv na vaše prostředky. Můžete pochopit, kdy problém začal a jaké služby a oblasti se to týká. Můžete si také přečíst nejnovější aktualizaci, abyste zjistili, co je Azure dělají řešení tohoto problému. 

![Správa problém se službou](./media/service-health-overview/azure-service-health-overview-2.png)

Zvolte **potenciální dopad** kartu pro zobrazení konkrétního seznamu prostředků, které vlastníte, které by mohly být ovlivněny problém. Můžete stáhnout CSV seznam těchto prostředků sdílet se svým týmem.

![Správa služby problém – dopad](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Získejte odkazy a vysvětlení ke stažení 
Můžete získat odkaz na problém pro použití v systému pro správu problémů. Můžete stáhnout soubor PDF a někdy soubory CSV sdílet s lidmi, kteří nemají přístup k webu Azure portal.   

![Správa služby problém – Správa problémů](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Získat podporu od Microsoftu
Pokud váš prostředek zůstane ve špatném stavu, i když dojde k vyřešení problému, obraťte se na podporu.  Pomocí podporu odkazů na pravé straně stránky.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Připněte mapu individuální stavu do řídicího panelu
Filtrovat Service Health a zobrazit důležité obchodní informace předplatná, oblasti a typy prostředků. Uložení se filtr a připněte individuální zdravotní mapu světa na řídicí panel portálu. 

![Mapa individuální stavu filtru](./media/service-health-overview/azure-service-health-overview-6a.png)

![PIN kód individuální zdravotní mapy](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurace výstrah stavu služby
Service Health se integruje se službou Azure Monitor k upozornění prostřednictvím e-mailů, textových zpráv a oznámení webhooků, když vašich důležitých prostředků zajišťuje. Nastavení upozornění protokolu aktivit pro událost příslušné službě stavu. Toto upozornění směrujte na příslušné osoby ve vaší organizaci pomocí skupin akcí. Další informace najdete v tématu [konfigurace výstrah pro službu Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

# <a name="next-steps"></a>Další kroky
Nastavení výstrah, takže se zobrazí oznámení problémů se stavem. Další informace najdete v tématu [konfigurace výstrah pro službu Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
