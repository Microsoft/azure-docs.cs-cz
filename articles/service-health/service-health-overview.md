---
title: Přehled stavu služby | Microsoft Docs
description: Přizpůsobené informace o tom, jak aplikace Azure ovlivněny problémy aktuálních a budoucích služby Azure a údržby.
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
ms.openlocfilehash: f0efe96684d77fb83b69a4da12d312872da2f768
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="service-health"></a>Stav služby
Stav služby poskytuje přizpůsobitelné řídicí panel, který sleduje stav služeb Azure v oblastech, kde je používat. V tomto řídicím panelu můžete sledovat active událostmi, jako je problémy s probíhající služby, nadcházející plánované maintence nebo zpravodaje relevantní stavu. Když neaktivní událostí, jejich získat umístěny v historii stavu po dobu 90 dnů. Nakonec můžete pomocí řídicího panelu stavu služby vytvořit a spravovat služby stavu výstrahy, které proaktivně vás upozornit, když problémů služby mají vliv na můžete.

## <a name="service-health-events"></a>Události stavu služby
Stav služby sleduje tři druhy zdraví události, které může mít vliv na vaše prostředky:
1. **Služba problémy** -problémy na portálu služby Azure, které teď můžete ovlivnit. 
2. **Plánované údržby** -nadcházející údržby, který může ovlivnit dostupnost vašich služeb v budoucnu.  
3. **Stav zpravodaje** -změny v služby Azure, které vyžadují vaši pozornost. Mezi příklady patří, když jsou zastaralé funkce Azure, nebo pokud překročí kvótu využití.

## <a name="get-started-with-service-health"></a>Začínáme s stav služby
Pokud chcete spustit řídicí panel stavu služby, vyberte dlaždici stav služby na řídicího panelu portálu. Pokud jste dříve odebrali dlaždice nebo používáte vlastní řídicí panel, vyhledejte službu Service Health v "Další služby" (vpravo na řídicím panelu dolů).

![Začínáme s stav služby](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Najdete v části aktuální problémy, což ovlivňuje vašim službám
**Služby problémy** jsou zobrazeny všechny probíhající problémy v služby Azure, které, které mají vliv vašich prostředků. Rozumíte zahájení problém a jaké služby a oblastí, dopad. Můžete si také přečíst nejnovější aktualizace pochopit, co je to Azure k vyřešení problému. 

![Správa služby problém](./media/service-health-overview/azure-service-health-overview-2.png)

Vyberte **potenciální dopad** zjistit konkrétní seznam prostředky, které vlastníte, které může mít vliv na problém. Můžete si stáhnout seznam CSV tyto prostředky do sdílené složky se svým týmem.

![Spravovat potíže služby – dopad](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Získat odkazy a vysvětlení ke stažení 
Odkaz na problém pro použití v systému správy problému můžete získat. Můžete si stáhnout PDF a někdy CSV soubory sdílet s lidmi, kteří nemají přístup k portálu Azure.   

![Správa služby potíže – Správa problémů](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Získat podporu od společnosti Microsoft
Pokud prostředek je ve špatném stavu i po byl problém vyřešen, obraťte se na podporu.  Pomocí podporu odkazů na pravé straně stránky.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Připnout mapu přizpůsobené stavu do řídicího panelu
Filtrujte stavu služby pro zobrazení vaší důležitých odběry, oblasti a typy prostředků. Uložení se filtr a připnout mapu world přizpůsobené stavu do řídicího panelu portálu. 

![Filtr přizpůsobené stavu mapy](./media/service-health-overview/azure-service-health-overview-6a.png)

![PIN kód mapu přizpůsobené stavu](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurace výstrah stavu služeb
Stav služby se integruje s Azure monitorování k upozornění prostřednictvím e-mailů, textové zprávy a webhooku oznámení, když dopad na vaše podnikové prostředky. Nastavte výstrahy aktivity protokolu pro událost příslušné službě stavu. Výstrahy směrujte na příslušné osoby ve vaší organizaci pomocí akce skupin. Další informace najdete v tématu [konfigurovat výstrahy pro stav služby](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

# <a name="next-steps"></a>Další kroky
Nastavení výstrah, takže je znázorněna problémy v oblasti stavu. Další informace najdete v tématu [konfigurovat výstrahy pro stav služby](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 
