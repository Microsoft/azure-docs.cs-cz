---
title: Správa nákladů dat v Azure Log Analytics | Microsoft Docs
description: Zjistěte, jak změnit cenový plán a spravovat zásady svazku a uchovávání dat pro pracovní prostor analýzy protokolů na portálu Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: magoedte
ms.openlocfilehash: 629031310eba21bc3308754706bd7ba227009ac6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="manage-cost-by-controlling-data-volume-and-retention-with-your-log-analytics-workspace"></a>Správa nákladů kontrolou datový svazek a uchovávání se pracovní prostor analýzy protokolů
Analýzy protokolů je navržený tak, aby škálování a podpora shromažďování, indexování a ukládání masivní objemy dat za den z jakéhokoli zdroje ve vašem podniku nebo nasazené v Azure.  To může být primární ovladač pro vaši organizaci, je základní ovladač nakonec efektivitu nákladů. Za tímto účelem je jeho důležité si uvědomit, že náklady na pracovním prostoru Analytisc protokolu není právě na základě objemu dat shromažďovaných, je také závislý na plán vybraný a jak dlouho jste se rozhodli uložit data generovaná z připojených zdrojů.  

V tomto článku jsme zkontrolujte, jak můžete proaktivně sledovat nárůst svazek a úložiště dat a definování omezení pro řízení těchto souvisejících nákladů. 

Náklady na dat může být výrazně v závislosti na následujících faktorech: 

- Počet systémů, komponent infrastruktury, cloudové prostředky, atd., které shromažďujete od 
- Typ dat vytvořené zdroje, například fronty zpráv, protokoly, události, související se zabezpečením dat nebo metrik výkonu. 
- Objem dat generované tyto zdroje a požity do pracovního prostoru 
- Období data se uchovávají v pracovním prostoru  
- Počet řešení pro správu povolena, zdroj dat a četnosti kolekce 

> [!NOTE]
> Jako poskytuje odhad množství dat, které shromáždí naleznete v dokumentaci pro každé řešení.   

Pokud jste na *volné* plán, je omezený na 7 dní uchovávání dat. Pro *samostatné* nebo *zaplacenou* vrstvy, shromažďovaných dat je k dispozici za posledních 31 dní. *Volné* plán má denní limit přijímání 500 MB, a pokud najít konzistentně překročit objemy povolené svazku, můžete změnit pracovního prostoru na placený plán ke shromažďování dat nad rámec tohoto limitu. 

> [!NOTE]
> Pokud zvolíte možnost vybrat delší doby uchování pro úroveň placené účtovány poplatky. Můžete změnit typ vašeho plánu kdykoli a další informace o cenách najdete v tématu [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/log-analytics/). 

Jsou dva způsoby, ve kterých můžete objem dat být omezené a lépe řídit vaše náklady, jedná se o denní uchování zakončení a data.  

## <a name="review-estimated-cost"></a>Zkontrolujte odhadované náklady
Díky protokolu analýzy ho snadno zjistit, co jsou náklady na pravděpodobně být založené na poslední vzorce používání.  Chcete-li to provést, proveďte následující kroky.  

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com). 
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
3. V podokně analýzy protokolů předplatných, vyberte pracovní prostor a pak klikněte na tlačítko **využití a odhadované náklady** v levém podokně.<br><br> ![Stránka odhadované náklady a využití](media/log-analytics-manage-cost-storage/usage-estimated-cost-dashboard-01.png)<br>

Odsud můžete zkontrolovat datový svazek pro daný měsíc. To zahrnuje všechna data přijme a uchovávají v pracovní prostor analýzy protokolů.  Klikněte na tlačítko **podrobnosti o použití** z horní části stránky zobrazení řídicího panelu využití s informacemi o vývoji svazku datového zdroje, počítače a nabídky. K zobrazení a nastavit denní zakončení nebo dobu uchování upravit, klikněte na tlačítko **Správa svazku dat**.
 
Log Analytics poplatky budou přidány do faktury Azure. Zobrazí podrobnosti o vašem Azure účtovat pošle části fakturace na portálu Azure nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Denní zakončení
Při vytváření pracovního prostoru analýzy protokolů z portálu Azure a vybrat *volné* plán, je nastavený na 500 MB za den limit. Neexistuje žádné omezení pro jiných cenových plánů. Můžete nakonfigurovat denní zakončení a omezit denní přijímání vašeho pracovního prostoru, ale použijte pozor, jak je vaším cílem by neměl být narazí denního limitu.  V opačném případě v tomto okamžiku dojde ke ztrátě dat pro zbytek dne a ovlivní schopnost sledovat podmínky stavu prostředků podpůrné služby IT.  Denní zakončení je určena pro použití jako způsob, jak zůstat v rámci svého limitu, nebo když chcete omezit jednoduše neplánované poplatky za pracovního prostoru a spravovat neočekávané zvýšení objemu dat ze spravované prostředky.  

Po dosažení denního limitu pro zbytek den zastaví kolekci fakturovatelný datových typů.  Nápis informující o upozornění se zobrazí v horní části stránky pro vybraný pracovní prostor analýzy protokolů a operace událost je odeslána do *operace* tabulky v části **LogManagement** kategorie. Shromažďování dat obnoví po době obnovení definované v části *denního limitu bude nastavena na*. Doporučujeme, abyste definování pravidlo výstrahy založené na události tuto operaci, nakonfigurován tak, aby informovala, že bylo dosaženo denního limitu data. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identifikovat jaké denní limit dat k definování 
Zkontrolujte [protokolu analýzy využití a odhadované náklady](log-analytics-usage.md) pochopit trend přijímání dat a co je denní cap svazku definovat. Dát pozor, je nutné zvážit, protože nebude možné po dosažení limitu bude monitorování vašich prostředků. 

### <a name="manage-the-maximum-daily-data-volume"></a>Spravovat maximální denní datový svazek 
Následující kroky popisují, jak nakonfigurovat omezení ke správě objem dat, který bude analýzy protokolů ingestování za den.  

1. Z pracovního prostoru vyberte **využití a odhadované náklady** v levém podokně.
2. Na **využití a odhadované náklady** stránky pro vybraný pracovní prostor, klikněte na tlačítko **Správa svazku dat** z horní části stránky. 
5. Denní krytka **OFF** ve výchozím nastavení – klikněte na tlačítko **ON** ji povolit, a pak nastavte limit dat svazek v GB a den.<br><br> ![Omezení dat konfigurace analýzy protokolů](media/log-analytics-manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-limit-reached"></a>Upozornit, když je dosaženo limitu
Když jsme k dispozici vizuální upozornění na portálu Azure při splnění prahové hodnoty pro limit vaše data, toto chování není nutně zarovnat na tom, jak spravovat provozních problémů, které vyžadují okamžitou pozornost.  Pokud chcete dostávat oznámení výstrahy, můžete vytvořit nové pravidlo výstrahy v nástroji Sledování Azure.  Další informace najdete v tématu [jak vytvářet, zobrazovat a spravovat výstrahy](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).      

Abyste mohli začít, zde jsou doporučená nastavení pro výstrahy:

* Cíl: Vyberte prostředek analýzy protokolů
* Kritéria: 
   * Název signálu: vlastní protokol hledání
   * Vyhledávací dotaz: operace | má-li podrobností 'OverQuota.
   * Na základě: počet výsledků
   * Podmínka: Větší než
   * Prahová hodnota: 0
   * Období: 5 (minuty)
   * Frekvence: 5 (minuty)
* Název pravidla výstrahy: denní dosažen limit dat
* Závažnost: Upozornění (závažnost 1)

Po výstraha je definovaný a dosažení limitu, výstrahu se aktivuje a provádí definovaná ve skupině Akce odpovědi. Dokáže oznámit váš tým přes e-mail a textové zprávy nebo automatizovat akce s použitím webhooky, runbooků Automation nebo [integraci s řešením pro externí ITSM](log-analytics-itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Změnit období uchovávání dat 
Následující kroky popisují postup konfigurace jak dlouho protokolu dat, který si ponechá v pracovním prostoru.
 
1. Z pracovního prostoru vyberte **využití a odhadované náklady** v levém podokně.
2. Na **využití a odhadované náklady** klikněte na tlačítko **Správa svazku dat** z horní části stránky.
5. V podokně, přesuňte jezdec zvýšit nebo snížit počet dní, a potom klikněte na **OK**.  Pokud jste na *volné* vrstvy, nebudete moct změnit období uchovávání dat a je třeba upgradovat k vrstvě placené cílem kontrolovat, toto nastavení.<br><br> ![Změna nastavení uchovávání dat pracovního prostoru](media/log-analytics-manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="troubleshooting"></a>Řešení potíží
**Otázka**: jak odstranit Pokud analýzy protokolů je už shromažďování dat? 
**Odpověď**: Pokud jsou na bezplatnou cenová úroveň a odeslali více než 500 MB dat za den, shromažďování dat zastaví pro zbytek dne. Dosažení denního limitu je běžným důvodem, který analýzy protokolů zastaví shromažďování dat, nebo se zdá být chybí data.  
Analýzy protokolů vytvoří událost typu operace, při shromažďování dat spuštění a zastavení.  
Spuštěním následujícího dotazu v hledání, zkontrolujte, zda jsou dosažení denního limitu a chybějící data: operace | kde OperationCategory == Stav shromažďování dat   
Když se shromažďování dat zastaví, je upozornění OperationStatus. Při spuštění shromažďování dat, je OperationStatus bylo úspěšně dokončeno.  
Následující tabulka popisuje důvody, které shromažďování dat zastaví a navrhovanou akci obnovíte shromažďování dat:  

|Zastaví důvod kolekce| Řešení| 
|-----------------------|---------|
|Dosažení denního limitu volné dat<sup>1</sup>|Počkejte, až další den pro kolekci k automatickému restartu, nebo změňte placené cenovou úroveň.|
|Dosažení denního limitu, které jste definovali ve svazku Správa dat|Počkejte, až další den pro kolekci k automatickému restartu, případně zvyšte denní limit dat svazek popsané v [spravovat maximální denní datový svazek](#manage-the-maximum-daily-volume)|
|Předplatné Azure je v pozastaveném stavu z důvodu:<br> Bezplatná zkušební verze skončila<br> Platnost Azure průchodu<br> Měsíčně útrat dosažen (například na předplatné MSDN nebo v sadě Visual Studio)|Převést na placené předplatné<br> Odeberte limit nebo počkejte, až se obnoví limit|

<sup>1</sup> Pokud pracovního prostoru na cenová úroveň free, jste omezené s odesíláním 500 MB dat za den ke službě. Po dosažení denního limitu shromažďování dat zastaví až další den. Data odeslaná při ukončení shromažďování dat je není indexované a není k dispozici pro vyhledávání. Když se obnoví shromažďování dat, dojde k zpracování pouze pro nové odeslaná data. 

Analýzy protokolů používá čas UTC. Doba obnovení se liší mezi pracovních prostorů můžete zabránit všechny počáteční vyloučením nejvyššího a nejnižšího pracovních prostorů příjem dat ve stejnou dobu. Pokud v pracovním prostoru dosáhne denního limitu, obnoví zpracování po době obnovení definované v **denního limitu bude nastavena na**.<br><br> ![Časové pásmo UTC omezit analýzy protokolů](media/log-analytics-manage-cost-storage/data-volume-mgmt-limit-utc.png)

**Otázka**: jak můžete se upozornění, když se shromažďování dat zastaví? 
**Odpověď**: pomocí kroků popsaných v *vytvořit denní data cap* výstrahu, kterou chcete být upozorněni, když se shromažďování dat zastaví a postupujte podle kroků pomocí kroků popsaných v přidání akce pravidla výstrah nakonfigurovat e-mailu, webhooku nebo sady runbook Akce pro pravidlo výstrahy. 

## <a name="next-steps"></a>Další postup  

Pokud chcete zjistit, kolik se data shromažďují, jaké zdroje jsou odesílání a různé typy dat odesílaných ke správě využívání a náklady, najdete v části [analýza využití dat v analýzy protokolů](log-analytics-usage.md).