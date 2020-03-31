---
title: Přehled stavu služby | Dokumenty společnosti Microsoft
description: Přizpůsobené informace o tom, jak jsou vaše aplikace Azure ovlivněny aktuálními a budoucími problémy se službami Azure a údržbou.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898440"
---
# <a name="service-health-overview"></a>Přehled stavu služby

Služba Service Health vám poskytuje přizpůsobitelný řídicí panel, který sleduje stav vašich služeb Azure v oblastech, kde je používáte. Na tomto řídicím panelu můžete sledovat aktivní události, jako jsou probíhající problémy se službami, nadcházející plánovaná údržba nebo příslušné zdravotní doporučení. Když se události stanou neaktivními, umístí se do vaší zdravotní historie po dobu až 90 dnů. Nakonec můžete pomocí řídicího panelu Stav služby vytvořit a spravovat výstrahy stavu služby, které vás proaktivně upozorní, když vás problémy se službou ovlivňují.

## <a name="service-health-events"></a>Události stavu služby

Stav služby sleduje čtyři typy událostí stavu, které mohou mít vliv na vaše prostředky:

1. **Problémy se službami** – problémy ve službách Azure, které se vás teď týkají. 
2. **Plánovaná údržba** – nadcházející údržba, která může ovlivnit dostupnost vašich služeb v budoucnu.  
3. **Doporučení stavu** – změny ve službách Azure, které vyžadují vaši pozornost. Mezi příklady patří, když jsou funkce Azure zastaralé nebo pokud překročíte kvótu využití.
4. **Informační zpravodaje zabezpečení (preview)** – oznámení související se zabezpečením, která mohou ovlivnit dostupnost vašich služeb Azure.

> [!NOTE]
> Chcete-li zobrazit události stavu služby, uživatelé musí [být udělena role čtečky](../role-based-access-control/role-assignments-portal.md) na předplatné.

## <a name="get-started-with-service-health"></a>Začínáme se stavem služby

Pokud chcete spustit řídicí panel Stavu služby, vyberte na řídicím panelu portálu dlaždici Stav služby. Pokud jste dlaždici dříve odebrali nebo používáte vlastní řídicí panel, vyhledejte službu Service Health v části Další služby (vlevo dole na řídicím panelu).

![Začínáme se stavem služby](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Zobrazení aktuálních problémů, které ovlivňují vaše služby

Zobrazení **Problémy se službou** zobrazuje všechny probíhající problémy ve službách Azure, které mají vliv na vaše prostředky. Můžete pochopit, kdy problém začal a jaké služby a oblasti jsou ovlivněny. Můžete si také přečíst nejnovější aktualizaci, abyste pochopili, co Azure dělá k vyřešení problému. 

![Správa problému se službou](./media/service-health-overview/azure-service-health-overview-2.png)

Zvolte kartu **Potenciální dopad,** chcete-li zobrazit konkrétní seznam prostředků, které vlastníte a které by mohly být ovlivněny problémem. Můžete si stáhnout seznam CSV těchto zdrojů, které chcete sdílet se svým týmem.

![Správa problému se službami – dopad](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Získejte odkazy a vysvětlení ke stažení 

Můžete získat odkaz na problém použít ve vašem systému správy problémů. Můžete si stáhnout soubory PDF a někdy i soubory CSV a sdílet je s lidmi, kteří nemají přístup k portálu Azure.   

![Správa problému se službami – správa problémů](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Získejte podporu od společnosti Microsoft

Obraťte se na podporu, pokud je prostředek ponechán ve špatném stavu i po vyřešení problému.  Použijte odkazy podpory na pravé straně stránky.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Připnutí personalizované mapy stavu na řídicí panel

Funkce Filter Service Health zobrazí vaše důležitá podniková předplatná, oblasti a typy prostředků. Uložte filtr a připněte si přizpůsobenou mapu světa se stavem služeb na řídicí panel portálu. 

![Filtrování mapy přizpůsobeného stavu](./media/service-health-overview/azure-service-health-overview-6a.png)

![Připnutí personalizované mapy stavu](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurace výstrah stavu služby

Služba Health se integruje s Azure Monitor, aby vás upozornila prostřednictvím e-mailů, textových zpráv a oznámení webhooku, když jsou ovlivněny vaše důležité obchodní prostředky. Nastavte výstrahu protokolu aktivit pro příslušnou událost stavu služby. Směrujte tuto výstrahu příslušným osobám ve vaší organizaci pomocí skupin akcí. Další informace naleznete v [tématu Konfigurace výstrah pro stav služby](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Další kroky

Nastavte výstrahy tak, abyste byli upozorněni na problémy se stavem. Další informace najdete [v tématu Doporučené postupy pro nastavení výstrah stavu služby Azure](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
