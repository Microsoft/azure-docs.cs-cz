---
title: Přehled Service Health | Microsoft Docs
description: Přečtěte si, jak vám Service Health poskytne přizpůsobitelný řídicí panel, který sleduje stav služeb Azure v oblastech, ve kterých se používají.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 8246b0ab93b95c13858e4ff96d0f24b255d05e55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90967782"
---
# <a name="service-health-overview"></a>Přehled Service Health

Service Health poskytuje přizpůsobitelný řídicí panel, který sleduje stav služeb Azure v oblastech, ve kterých je používáte. V tomto řídicím panelu můžete sledovat aktivní události, jako jsou průběžné problémy se službami, nadcházející plánovaná údržba nebo příslušné poradce pro stav. Když se události stanou neaktivní, uloží se do historie stavu po dobu až 90 dnů. Nakonec můžete pomocí řídicího panelu Service Health vytvářet a spravovat výstrahy týkající se stavu služby, které vás proaktivně upozorní na to, co se vás týkají problémy se službou.

## <a name="service-health-events"></a>Události Service Health

Service Health sleduje čtyři typy událostí stavu, které mohou mít vliv na vaše prostředky:

1. **Problémy se službou** – problémy ve službách Azure, které vás v tuto chvíli ovlivňují. 
2. **Plánovaná údržba** – nadcházející údržba, která může ovlivnit dostupnost vašich služeb v budoucnu.  
3. **Poradce pro stav** – změny ve službách Azure, které vyžadují vaši pozornost. Příklady zahrnují vyřazení funkcí Azure nebo požadavků na upgrade (například upgrade na podporované rozhraní PHP).
4. **Bezpečnostní zpravodaje** – oznámení týkající se zabezpečení a porušení zabezpečení, která mohou mít vliv na dostupnost služeb Azure.

> [!NOTE]
> Chcete-li zobrazit Service Health události, musí být uživatelům [udělena role čtenář](../role-based-access-control/role-assignments-portal.md) v rámci předplatného.

## <a name="get-started-with-service-health"></a>Začínáme s Service Health

Řídicí panel Service Health spustíte tak, že na řídicím panelu portálu vyberete dlaždici Service Health. Pokud jste už tuto dlaždici odebrali nebo jste použili vlastní řídicí panel, vyhledejte Service Health službu v části "další služby" (dole vlevo na řídicím panelu).

![Začínáme s Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Zobrazit aktuální problémy, které mají vliv na vaše služby

Zobrazení **problémy se službou** zobrazuje všechny probíhající problémy ve službách Azure, které mají vliv na vaše prostředky. Můžete pochopit, kdy problém začal a jaké služby a oblasti budou ovlivněny. Můžete si také přečíst nejnovější aktualizaci, abyste zjistili, co Azure dělá k vyřešení tohoto problému. 

[![Správa problému se službou](./media/service-health-overview/azure-service-health-overview-2.png)](./media/service-health-overview/azure-service-health-overview-2.png#lightbox)

Kliknutím na kartu **potenciální dopad** můžete zobrazit konkrétní seznam prostředků, které vlastníte, což může být ovlivněno problémem. Seznam sdílených svazků clusteru můžete stáhnout a sdílet je s týmem.

[![Správa problému se službou – dopad](./media/service-health-overview/azure-service-health-overview-4.png)](./media/service-health-overview/azure-service-health-overview-4.png#lightbox)

## <a name="see-emerging-issues-which-may-impact-your-services"></a>Podívejte se na vznikající problémy, které můžou mít vliv na vaše služby.

V některých situacích se může stát, že se na [stránku stavu Azure](https://status.azure.com) publikují rozšířené problémy se službami, které by mohly být zaslány ovlivněným zákazníkům. Abyste zajistili, že Azure Service Health poskytuje komplexní přehled o problémech, které vás mohou ovlivnit, jsou aktivní problémy se stavovou stránkou Azure v Service Health při *nově vznikajících problémech*. V případě, že je událost aktivní na stránce stavu Azure, bude v Service Health k dispozici informační zpráva o nově vznikajících problémech. Kliknutím na banner si zobrazíte všechny podrobnosti o problému.

![Vznikající problém služby](./media/service-health-overview/azure-service-health-emerging-issue.png)

## <a name="get-links-and-downloadable-explanations"></a>Získat odkazy a vysvětlení ke stažení 

Můžete získat odkaz na problém, který se má použít v systému pro správu problémů. Soubory PDF a soubory CSV si můžete stáhnout a sdílet je s lidmi, kteří nemají přístup k Azure Portal.   

[![Správa problému se službou – Správa problémů](./media/service-health-overview/azure-service-health-overview-3.png)](./media/service-health-overview/azure-service-health-overview-3.png#lightbox)

## <a name="get-support-from-microsoft"></a>Získání podpory od Microsoftu

Pokud je váš prostředek ponechán v nesprávném stavu i po vyřešení problému, obraťte se na podporu.  Použijte odkazy na podporu na pravé straně stránky.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Připnout přizpůsobenou mapu stavu na řídicí panel

Filtrovat Service Health pro zobrazení předplatných, oblastí a typů prostředků, které jsou důležité pro vaši firmu Uložte filtr a připněte si přizpůsobenou mapu světa se stavem služeb na řídicí panel portálu. 

[![Filtrovat přizpůsobenou mapu stavu](./media/service-health-overview/azure-service-health-overview-6a.png)](./media/service-health-overview/azure-service-health-overview-6a.png#lightbox)

![Připnout přizpůsobenou mapu stavu](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Konfigurace upozornění na stav služby

Service Health se integruje s Azure Monitor a upozorní vás prostřednictvím e-mailů, textových zpráv a oznámení Webhooku v případě, že budou ovlivněny vaše důležité firemní prostředky. Nastavte upozornění protokolu aktivit pro příslušnou událost služby Service Health. Směrování této výstrahy na příslušné osoby ve vaší organizaci pomocí skupin akcí. Další informace najdete v tématu [Konfigurace výstrah pro Service Health](./alerts-activity-log-service-notifications-portal.md) .

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Další kroky

Nastavte výstrahy, abyste byli informováni o problémech se stavem. Další informace najdete v tématu [osvědčené postupy pro nastavení výstrah Azure Service Health](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
