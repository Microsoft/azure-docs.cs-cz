---
title: Spravovat náklady na data ve službě Azure Log Analytics | Dokumentace Microsoftu
description: Zjistěte, jak změnit cenový tarif a spravovat zásady objemu a uchovávání dat pro váš pracovní prostor Log Analytics v Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 744a0f683f58aed98cea7bdef0b2a36af68ad2f1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53097569"
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-in-log-analytics"></a>Spravovat náklady pomocí řízení objemu dat a uchovávání dat v Log Analytics

> [!NOTE]
> Tento článek popisuje, jak řídit své náklady v Log Analytics tak, že nastavíte dobu uchování dat.  Přečtěte si následující články související informace.
> - [Analýza využití dat v Log Analytics](manage-cost-storage.md) popisuje, jak analyzovat a upozornění na data využití.
> - [Monitorování využití a odhadované náklady](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Log Analytics je navržená tak, aby škálování a podporu shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje ve vašem podniku nebo nasazené v Azure.  To může být hlavním důvod pro vaši organizaci, nákladovou efektivnost v konečném důsledku je základní ovladač. Za tímto účelem je důležité pochopit, že náklady na pracovní prostor Log Analytics není jen na základě objemu shromážděných dat, je také závislý na vybraného plánu a jak dlouho jste se rozhodli ukládat data generovaná z připojených zdrojů.  

V tomto článku jsme zkontrolujte, jak můžete aktivně monitorovat růst objemu a úložiště dat a definování omezení pro řízení těchto související náklady. 

Náklady na data může být značné, v závislosti na následujících faktorech: 

- Počet systémů, komponent infrastruktury, cloudové prostředky, atd., které shromažďujete od 
- Typ data vytvořená zdroji, jako jsou fronty zpráv, protokoly, události, data související se zabezpečením nebo metriky výkonu 
- Objem dat generovaných tyto zdroje a ingestuje se do pracovního prostoru 
- Období data se uchovávají v pracovním prostoru  
- Počet řešení pro správu povolena, zdroj dat a četnost shromažďování dat 

> [!NOTE]
> Naleznete v dokumentaci pro jednotlivá řešení, protože poskytuje odhad, kolik dat shromažďuje.   

Zákazníci se smlouvou Enterprise podepsaná před 1. červencem 2018 nebo kteří už vytvořili pracovní prostor Log Analytics v rámci předplatného, budete mít dál přístup k *Free* plánu. Pokud vaše předplatné není vázaný na existující registraci smlouvy Enterprise, *Free* úroveň není k dispozici, když vytvoříte pracovní prostor v rámci nového předplatného po 2. dubna 2018.  Je omezený na 7denní doba uchování dat *Free* vrstvy.  Pro *samostatné* nebo *Paid* vrstvy, shromážděných dat je k dispozici pro posledních 31 dní. *Free* úroveň má denního limitu příjmu 500 MB, a pokud najdete konzistentně překročení částky povolené svazku, můžete změnit pracovního prostoru na placený tarif ke shromažďování dat nad tento limit. 

> [!NOTE]
> Pokud budete chtít vybrat delší doba uchovávání dat pro placenou úroveň účtovány poplatky. Můžete změnit svůj datový tarif, kdykoli a další informace o cenách naleznete v tématu [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/log-analytics/). 

Jsou dva způsoby, ve kterých daný objem dat omezena a pomoct řídit vaše náklady, jedná se o denního limitu a uchovávání dat.  

## <a name="review-estimated-cost"></a>Zkontrolujte odhadované náklady
Log Analytics umožňuje snadno pochopit, jaké jsou náklady pravděpodobně možné na základě nedávné způsobů využití.  Chcete-li to provést, postupujte následovně.  

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com). 
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](media/manage-cost-storage/azure-portal-01.png)<br><br>  
3. V podokně předplatná Log Analytics vyberte pracovní prostor a pak klikněte na tlačítko **využití a odhadované náklady** v levém podokně.<br><br> ![Využití a odhadované náklady na stránce](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Odsud můžete zkontrolovat objem dat za měsíc. To zahrnuje všechna data přijatá a uchovávají ve vašem pracovním prostoru Log Analytics.  Klikněte na tlačítko **podrobnosti o použití** z horní části stránky, chcete-li zobrazit řídicí panel s informacemi o využití na trendy objemu dat podle zdroje, počítačů a nabídky. K zobrazení a nastavte denní limit nebo dobu uchování upravit, klikněte na tlačítko **Správa objemu dat**.
 
Poplatky za log Analytics se přidají do vašeho vyúčtování služeb Azure. Můžete si prohlédnout podrobnosti Azure fakturovat části fakturace na portálu Azure Portal nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Denní limit
Při vytváření pracovního prostoru Log Analytics z Azure Portalu a tam zvolte *Free* plán, je nastavena na 500 MB za den, limit. Neexistuje žádné omezení pro cenové plány. Můžete nakonfigurovat denní limit a omezit tak denní příjem dat pro váš pracovní prostor, ale použijte pozornost jak vaším cílem by neměl být k dosažení denního limitu.  V opačném případě můžete přijít o data po zbytek dne, který může mít vliv na ostatní služby Azure a řešení, jehož funkce může záviset na aktuální data, která je k dispozici v pracovním prostoru.  V důsledku toho možnost sledovat a přijímat upozorní, když se to týká podmínky stavu prostředků podporující IT služby.  Denní limit je určena pro použití jako způsob, jak spravovat neočekávané nárůst objemu dat ze spravovaných prostředků a zůstat v rámci svého limitu, nebo pokud chcete jednoduše omezení neplánovaných poplatků pro váš pracovní prostor.  

Po dosažení denního limitu pro zbytek dne zastaví kolekci fakturovatelné datových typů. Zobrazí se upozornění banner v horní části stránky pro vybraný pracovní prostor Log Analytics a operace událost je odeslána do *operace* tabulky v rámci **LogManagement** kategorie. Shromažďování dat bude pokračovat po resetování času definované v části *denní limit se nastaví na*. Doporučujeme definovat pravidlo výstrahy založené na této události operace nakonfigurovaný tak, aby upozornit po dosažení denního datového limitu. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifikujte jaké denního limitu pro definování dat 
Kontrola [využití Log Analytics a odhadované náklady](../../azure-monitor/platform/data-usage.md) pochopit trend příjem dat a k čemu slouží k definování denní limit objemu. Je nutné zvážit opatrně, protože nebude možné po dosažení limitu monitorování prostředků. 

### <a name="manage-the-maximum-daily-data-volume"></a>Správa maximální denní objem dat 
Následující kroky popisují, jak nakonfigurovat limit Správa objemu dat, která Log Analytics bude ingestovat za den.  

1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. Na **využití a odhadované náklady** stránek pro vybraný pracovní prostor, klikněte na tlačítko **Správa objemu dat** z horní části stránky. 
5. Denní limit je **OFF** ve výchozím nastavení – klikněte na tlačítko **ON** ji povolit, a pak nastavit limit objemu dat v GB za den.<br><br> ![Konfigurace omezení dat log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Pošle upozornění, když byl dosažen limit
Zatímco Představujeme vizuální upozornění na webu Azure Portal, pokud je dodržena prahová hodnota pro omezení vašich dat, toto chování není nutně zarovnat spravujete provozní problémy, které vyžadují okamžitou pozornost.  Pokud chcete dostávat oznámení výstrah, můžete vytvořit nové pravidlo výstrah ve službě Azure Monitor.  Další informace najdete v tématu [vytvoření, zobrazení a Správa výstrah](../../monitoring-and-diagnostics/alert-metric.md).      

Abyste mohli začít, zde jsou doporučené nastavení pro upozornění:

* Cíl: Vyberte prostředek Log Analytics
* Kritéria: 
   * Název signálu: vlastní prohledávání protokolů
   * Vyhledávací dotaz: operace | má-li podrobnosti "nadměrnou.
   * Na základě: počet výsledků
   * Podmínka: Větší než
   * Prahová hodnota: 0
   * Období: 5 (minuty)
   * Frekvence: 5 (minuty)
* Název pravidla upozornění: dosáhlo se denního datového limitu
* Závažnost: Upozornění (záv. 1)

Po upozornění je definován a dosáhnete limitu, výstraha se aktivuje a provádí definované ve skupině Akce odpovědi. Dokáže upozorňovat tým prostřednictvím e-mailu a textovými zprávami nebo Automatizujte akce pomocí webhooků, runbooků služby Automation nebo [integraci s externím řešení ITSM](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Změnit dobu uchování dat 
Následující kroky popisují, jak nakonfigurovat jak dlouho protokol dat se uchovává ve vašem pracovním prostoru.
 
1. V levém podokně vašeho pracovního prostoru vyberte **Využití a odhadované náklady**.
2. V horní části stránky **Využití a odhadované náklady** klikněte na **Správa objemu dat**.
5. V podokně s pomocí posuvníku zvyšte nebo snižte počet dní a potom klikněte na tlačítko **OK**.  Pokud používáte *bezplatné* úroveň, nebudete moci upravit dobu uchovávání dat a budete muset upgradovat na placenou úroveň cílem kontrolovat, toto nastavení.<br><br> ![Změna nastavení uchovávání dat pracovního prostoru](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Řešení potíží
**Dotaz**: jak řešit Pokud Log Analytics je už shromažďování dat? 
**Odpověď**: Pokud se na bezplatné cenové úrovně a odeslali více než 500 MB dat za den, shromažďování dat zastaví zbytek dne. Dosažení denního limitu je běžným důvodem Log Analytics se zastaví shromažďování dat, nebo se zdá být chybějící data.  
Log Analytics, vytváří událost typu operace při shromažďování dat spustí a zastaví.  
Spuštěním následujícího dotazu ve službě search zkontrolujte, jestli jsou dosažení denního limitu a chybějící data: operace | kde OperationCategory == Stav shromažďování dat   
Když se zastaví shromažďování dat, je upozornění stav OperationStatus. Při shromažďování dat začne, stav OperationStatus je proběhlo úspěšně.  
Následující tabulka popisuje důvody, které zastaví shromažďování dat a navrhovanou akci pokračování shromažďování dat:  

|Zastaví shromažďování důvod| Řešení| 
|-----------------------|---------|
|Dosažení denního limitu bezplatná data<sup>1</sup>|Počkejte, až další den pro kolekci k automatickému restartu, nebo změňte na placenou cenovou úroveň.|
|Dosáhlo se denního limitu, které jste definovali v Správa objemu dat|Počkejte, až další den pro kolekci k automatickému restartu nebo zvýšit denní limit objemu dat je popsáno v [spravovat maximální denní objem dat](#manage-the-maximum-daily-volume)|
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Bezplatná zkušební verze skončila<br> Vypršení platnosti Azure passu<br> Měsíční limit útraty dosaženo (například na předplatné MSDN nebo Visual Studio)|Převést na placené předplatné<br> Odeberte limit, nebo počkejte, dokud limit se resetuje|

<sup>1</sup> Pokud váš pracovní prostor v bezplatné cenové úrovně, jste omezeni na 500 MB dat denně odesílá do služby. Pokud denní limit překročíte, shromažďování dat zastaví až další den. Data odeslaná při zastavení shromažďování dat se indexují a není k dispozici pro hledání. Po obnovení se shromažďování dat, zpracování probíhá pouze pro nové odeslaná data. 

Log Analytics používá čas UTC. Doba obnovení se liší mezi pracovními prostory, aby se zabránilo limitní pracovní prostory začínají ingestovat data ve stejnou dobu. Pokud pracovní prostor dosáhne denní limit, zpracování bude pokračovat po resetování času definované v **denní limit se nastaví na**.<br><br> ![Log Analytics omezit časové pásmo UTC](media/manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Dotaz**: jak si nastavím upozorňování při zastavení shromažďování dat? 
**Odpověď**: použijte postup popsaný v *vytvořit denním datovém limitu* upozornění, která vás upozorní, když se zastaví shromažďování dat a postupujte podle kroků, pomocí kroků popsaných v přidání akcí k pravidlům upozornění konfigurace e-mailu, webhooku nebo runbooku Akce pro pravidlo upozornění. 

## <a name="next-steps"></a>Další postup  

Pokud chcete zjistit, kolik dat se shromažďují, jaké zdroje jsou odesílání a různé typy dat odeslaných ke správě využití a náklady, najdete v článku [analýza využití dat v Log Analytics](../../azure-monitor/platform/data-usage.md).
