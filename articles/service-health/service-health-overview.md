---
title: Přehled Service Health | Microsoft Docs
description: Individuální informace o tom, jak jsou vaše aplikace Azure ovlivněny aktuálními a budoucími problémy a údržbou služeb Azure.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: db9d74e2883e755c3e5c3dbbe9d6570f1e78c4a6
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77654014"
---
# <a name="service-health-overview"></a>Přehled Service Health

Service Health poskytuje přizpůsobitelný řídicí panel, který sleduje stav služeb Azure v oblastech, ve kterých je používáte. V tomto řídicím panelu můžete sledovat aktivní události, jako jsou průběžné problémy se službami, nadcházející plánovaná údržba nebo příslušné poradce pro stav. Když se události stanou neaktivní, uloží se do historie stavu po dobu až 90 dnů. Nakonec můžete pomocí řídicího panelu Service Health vytvářet a spravovat výstrahy týkající se stavu služby, které vás proaktivně upozorní na to, co se vás týkají problémy se službou.

## <a name="service-health-events"></a>Události Service Health

Service Health sleduje tři typy událostí stavu, které mohou mít vliv na vaše prostředky:

1. **Problémy se službou** – problémy ve službách Azure, které vás v tuto chvíli ovlivňují. 
2. **Plánovaná údržba** – nadcházející údržba, která může ovlivnit dostupnost vašich služeb v budoucnu.  
3. **Poradce pro stav** – změny ve službách Azure, které vyžadují vaši pozornost. Mezi příklady patří i v případě, že funkce Azure jsou zastaralé nebo překročíte kvótu využití.

> [!NOTE]
> Chcete-li zobrazit Service Health události, musí být uživatelům [udělena role čtenář](../role-based-access-control/role-assignments-portal.md) v rámci předplatného.

## <a name="get-started-with-service-health"></a>Začínáme s Service Health

Řídicí panel Service Health spustíte tak, že na řídicím panelu portálu vyberete dlaždici Service Health. Pokud jste už tuto dlaždici odebrali nebo jste použili vlastní řídicí panel, vyhledejte Service Health službu v části "další služby" (dole vlevo na řídicím panelu).

![Začínáme s Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Zobrazit aktuální problémy, které mají vliv na vaše služby

Zobrazení **problémy se službou** zobrazuje všechny probíhající problémy ve službách Azure, které mají vliv na vaše prostředky. Můžete pochopit, kdy problém začal a jaké služby a oblasti budou ovlivněny. Můžete si také přečíst nejnovější aktualizaci, abyste zjistili, co Azure dělá k vyřešení tohoto problému. 

![Správa problému se službou](./media/service-health-overview/azure-service-health-overview-2.png)

Kliknutím na kartu **potenciální dopad** můžete zobrazit konkrétní seznam prostředků, které vlastníte, což může být ovlivněno problémem. Seznam sdílených svazků clusteru můžete stáhnout a sdílet je s týmem.

![Správa problému se službou – dopad](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Získat odkazy a vysvětlení ke stažení 

Můžete získat odkaz na problém, který se má použít v systému pro správu problémů. Soubory PDF a soubory CSV si můžete stáhnout a sdílet je s lidmi, kteří nemají přístup k Azure Portal.   

![Správa problému se službou – Správa problémů](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Získat podporu od Microsoftu

Pokud je váš prostředek ponechán v nesprávném stavu i po vyřešení problému, obraťte se na podporu.  Použijte odkazy na podporu na pravé straně stránky.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Připnout přizpůsobenou mapu stavu na řídicí panel

Filtrovat Service Health pro zobrazení předplatných, oblastí a typů prostředků, které jsou důležité pro vaši firmu Uložte filtr a připněte na řídicí panel portálu přizpůsobenou mapu světové ochrany stavů. 

![Filtrovat přizpůsobenou mapu stavu](./media/service-health-overview/azure-service-health-overview-6a.png)

![Připnout přizpůsobenou mapu stavu](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurace upozornění na stav služby

Service Health se integruje s Azure Monitor a upozorní vás prostřednictvím e-mailů, textových zpráv a oznámení Webhooku v případě, že budou ovlivněny vaše důležité firemní prostředky. Nastavte upozornění protokolu aktivit pro příslušnou událost služby Service Health. Směrování této výstrahy na příslušné osoby ve vaší organizaci pomocí skupin akcí. Další informace najdete v tématu [Konfigurace výstrah pro Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md) .

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Další kroky

Nastavte výstrahy, abyste byli informováni o problémech se stavem. Další informace najdete v tématu [osvědčené postupy pro nastavení výstrah Azure Service Health](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
