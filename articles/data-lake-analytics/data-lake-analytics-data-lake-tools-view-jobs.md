---
title: Použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics
description: Tento článek popisuje použití prohlížeče úloh a zobrazení úloh pro úlohy Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonwhowell
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 905100f8a1444f6f6ee18d3bf9e9eab2ede8c805
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815196"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Použití prohlížeče úloh a zobrazení úloh pro Azure Data Lake Analytics
Služba Azure Data Lake Analytics archivuje odeslané úlohy v úložišti dotazů. V tomto článku se dozvíte, jak použití prohlížeče úloh a zobrazení úloh v Azure Data Lake Tools pro Visual Studio k nalezení informací o historie úlohy. 

Ve výchozím nastavení služby Data Lake Analytics archivuje úlohy po dobu 30 dnů. Dobu vypršení platnosti je možné nakonfigurovat z portálu Azure tím, že nakonfigurujete zásady přizpůsobené vypršení platnosti. Nebudete mít přístup k informacím úlohy po vypršení platnosti. 

## <a name="prerequisites"></a>Požadavky
Zobrazit [nástrojů Data Lake pro Visual Studio požadavky](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otevřít prohlížeč úloh
Přístup k prohlížeč úloh prostřednictvím **Průzkumník serveru > Azure > Data Lake Analytics > úlohy** v sadě Visual Studio.  Použití prohlížeče úloh, můžete přístup k úložišti dotazů účtu Data Lake Analytics. Prohlížeč úloh zobrazí Query Store na levé straně, informace o základních úlohách a zobrazení úloh na pravé zobrazení podrobné informace o úloze.

## <a name="job-view"></a>Zobrazení úlohy
Zobrazení úlohy zobrazuje podrobné informace o úlohu. Spustit úlohu, můžete dvakrát klikněte na úlohu v prohlížeči projektu, nebo otevřete z nabídky Data Lake kliknutím zobrazení úloh. Zobrazí se dialogové okno vyplní adresa URL úlohy.

![Nástroje data Lake Visual Studio prohlížeč úloh](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Obsahuje zobrazení úlohy:

* Souhrn úlohy
  
    Aktualizujte zobrazení úlohy zobrazíte nejnovější informace o spouštění úloh.
  
  * Stav úlohy (graf):
    
      Stav úlohy popisuje fáze úlohy:
    
      ![Fáze stav úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Příprava: Nahrajte skript do cloudu, kompilaci a optimalizaci skriptu s použitím služby kompilace.
    * Ve frontě: Úlohy se zařadí do fronty při čekání dostatek prostředků, nebo úlohy, které překračují maximální počet souběžných úloh na účet omezení. Nastavení priority určuje pořadí ve frontě úloh – Čím nižší je číslo, tím vyšší je priorita.
    * Spuštěno: Úloha je ve skutečnosti spuštěná ve svém účtu Data Lake Analytics.
    * Dokončování: Úloha se dokončuje (například dokončení souboru).
      
      Úloha může selhat v každé fázi. Například chyby kompilace ve fázi Příprava, vypršení časového limitu ve fázi zařazeno do fronty a provádění chyby ve fázi spuštění atd.
  * Základní informace
    
      Informace o základních úlohách zobrazuje v dolní části panely Souhrn úlohy.
    
      ![Fáze stav úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Výsledek úlohy: Úspěšné nebo neúspěšné. V každé fázi, může úloha selhat.
    * Celková doba trvání: Skutečný čas (doba trvání) mezi odesláním čas a koncový čas.
    * Celková doba výpočtu: Součet všech doba spuštění vrcholu, můžete ho jako čas, který úlohu spouští v pouze jeden vrchol. Odkazovat na celkový počet vrcholů najdete další informace o vrcholu.
    * Odeslat/počáteční/koncový čas: Čas, kdy služby Data Lake Analytics přijímá úlohy odeslání/spustí úlohu spustit úlohu/skončí, úspěšně nebo ne.
    * Kompilace/ve frontě/spuštění: Skutečný čas strávený během fáze příprava nebo ve frontě/spuštěný.
    * Účet: Účet Data Lake Analytics používá pro spuštění úlohy.
    * Autor: Uživatel, který odešle úlohu, může to být účet skutečná osoba nebo systémový účet.
    * Priorita: Priorita úlohy. Čím nižší je číslo, tím vyšší je priorita. Ovlivňuje pouze pořadí úloh ve frontě. Nastavení s vyšší prioritou neomezoval na probíhající úlohy.
    * Paralelismus: Požadovaná maximální počet souběžných Azure Data Lake Analytics jednotek (ADLAUs), neboli vrcholy. V současné době jednoho vrcholu se rovná jednomu virtuálnímu počítači s dvě virtuální jádra a 6 GB paměti RAM, i když může být upgradována v budoucnu Data Lake Analytics aktualizuje.
    * Zbývající bajty: Počet bajtů, které potřebují ke zpracování, dokud se úloha nedokončí.
    * Načtených/zapsaných bajtů: Počet bajtů, které byly načtených/zapsaných od spuštění úlohy.
    * Vrcholy celkem: Úloha je rozdělený do částí mnoho práce, každá část práce se nazývá vrcholu. Tuto hodnotu popisuje počet jednotek zpracování úlohy se skládá z. Jako jednotku základního procesu, označuje se také jako Azure Data Lake Analytics jednotek (ADLAU), můžete zvážit vrcholu a vrcholy můžete spustit v paralelismu. 
    * Dokončení/spuštění nebo se nezdařilo: Počet vrcholů dokončení/spuštění nebo se nezdařilo. Vrcholy může selhat z důvodu chyby kódu a systému i uživatele, ale systém opakované pokusy byly neúspěšné vrcholy automaticky několikrát. Pokud po opakovaném pokusu stále selhal vrchol, celá úloha skončí chybou.
* Graf úlohy
  
    Skript U-SQL představuje logiku transformuje vstupní data a výstupní data. Skript je zkompilován a optimalizovaná tak, aby na Příprava fáze spuštění fyzického plánu. Graf úlohy je zobrazíte spuštění fyzického plánu.  Následující obrázek znázorňuje proces:
  
    ![Fáze stav úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Úloha je rozdělený do částí mnoho práce. Každá část práce se nazývá vrcholu. Vrcholy jsou seskupeny jako Super vrcholu (neboli fáze) a vizualizovat jako graf úlohy. Malá zelená fáze v grafu úlohy zobrazit v jednotlivých fázích.
  
    Všech vrcholů ve fázi provádí stejný druh práce s různý stejná data. Například pokud máte soubor s jeden TB dat a existují stovky vrcholy čtení z něj, každý z nich čte blok. Tyto vrcholy jsou seskupené ve stejné fázi a tím stejné pracovat na různé softwarové součásti ze stejného vstupního souboru.
  
  * <a name="state-information"></a>Informace o fázi
    
      V určité fázi jsou uvedeny některé čísla v štítek.
    
      ![Fáze grafu úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * Extrakce SV1: Název fáze, čísla a operace metodu s názvem.
    * 84 vrcholy: Celkový počet vrcholů v této fázi. Na obrázku označuje, kolik jednotek zpracování je rozdělen na tuto fázi.
    * 12.90 s/vrchol: Průměrná doba spuštění vrcholu pro tuto fázi. Na tomto obrázku je vypočítána SUM (každý doba spuštění vrcholu) / (celkový počet vrcholů). To znamená, že pokud můžete přiřadit všechny vrcholy, které jsou provedeny v paralelismu, celý fáze se dokončí v 12.90 s. To také znamená, že pokud veškerou práci v této fázi se provádí sériově, náklady by byly #vertices * Průměrný čas.
    * zapsané 850,895 řádky: Celkový počet řádků v této fázi.
    * R/W: Objem dat pro čtení/Written v této fázi v bajtech.
    * Barvy: Barvy se používají ve fázi označuje stav různých vrcholu.
      
      * Zelená značí, že je úspěšně vrchol.
      * Oranžová označuje, že proběhne vrchol. Opakované vrcholu došlo k chybě, ale systém proběhne automaticky a úspěšně a celkové fáze byla úspěšně dokončena. Pokud vrchol opakovat, ale stále se nezdařilo, barva se změní na červené a celé úlohy se nezdařilo.
      * Červená značí selhání, to znamená, že některé vrcholu měl byla několikrát opakována v systému, ale stále se nezdařilo. Tento scénář způsobí, že celá úloha nezdaří.
      * Modré znamená, že některé vrchol běží.
      * Prázdné označuje vrchol čeká. Vrchol může čekat k naplánování, jakmile bude k dispozici ADLAU nebo může být čekání na vstup od jeho vstupních dat pravděpodobně není připravena.
      
      Další podrobnosti můžete najít pro fázi podržením ukazatele myši podle státu:
      
      ![Podrobnosti o grafu fáze úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vrcholy: Popisuje podrobnosti vrcholy, například kolik vrcholy celkem, kolik vrcholy nejsou dokončeny, jsou se nezdařilo nebo je stále spuštěna/čeká atd.
  * Data přečtená mezi/uvnitř podů: Soubory a data se ukládají v několik podů se v systému souborů DFS. Tuto hodnotu popisuje, jak velký objem dat byl přečten pod stejné nebo různé pod.
  * Celková doba výpočtu: Součet dob spuštění všech vrcholů ve fázi, můžete ho jako čas, kdy by to obnášelo případě všechny práci ve fázi se provádí pouze jeden vrchol.
  * Data a řádky zapisují/čtení: Označuje, kolik mít byla načtených/zapsaných dat nebo řádky, nebo je potřeba načíst.
  * Nepovedená čtení vrcholů: Popisuje počet vrcholů se chyba při čtení dat.
  * Duplicitní zrušení vrcholů: Pokud vrchol běží příliš pomalu, systém může naplánovat několik vrcholů spustit stejnou část práce. Přijdete o reductant vrcholy po jedné vrcholů úspěšně dokončit. Duplicitní zrušení vrcholů zaznamenává počet vrcholů, které jsou zahozeny jako duplicit ve fázi.
  * Vrácení vrcholů: Vrchol byl úspěšně, ale získat později znovu z nějakého důvodu. Například pokud podřízený vrcholu ztratí zprostředkující vstupních dat, budete dotázáni, nadřazený vrcholu spustit znovu.
  * Spuštění vrcholů podle plánu: Celková doba naplánovali vrcholy.
  * Čtení dat vrcholu průměr/min/Max: Minimální/průměrný/maximální počet každý vrchol číst data.
  * Doba trvání: Skutečný čas fázi přijímá, je potřeba načíst profil chcete zobrazit tuto hodnotu.
  * Přehrávání úloh
    
      Data Lake Analytics, spouští úlohy a archivy vrcholy s informace o úlohy, například při vrcholy spuštění, zastavení, se nezdařilo a jak se zopakují, atd. Všechny informace, je automaticky zaznamenána v úložišti dotazů a uložených v jeho profil úlohy. Můžete si stáhnout profil úlohy prostřednictvím "Profil zatížení" v zobrazení úloh a přehrávání úlohy můžete zobrazit po stažení profilu úlohy.
    
      Přehrání úlohy je vizualizaci epitome co se stalo v clusteru. Pomáhá sledovat průběh provádění úlohy a vizuálně detekovat kritická místa ve velmi krátkém čase (méně než obvykle 30 s) a anomálie výkonu.
  * Zobrazení úlohy Heat mapy 
    
      Heat mapa úlohy se dají vybrat pomocí rozevíracího seznamu zobrazení v grafu úlohy. 
    
      ![Azure Data Lake Analytics úlohy grafu haldy zobrazení mapy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Zobrazuje heat mapa vstupně-výstupních operací, čas a propustnost úlohy, pomocí kterého můžete najít kde úloha tráví většinu času, nebo zda úlohy úlohu hranice vstupně-výstupní operace a tak dále.
    
      ![Příklad haldy mapy úlohy grafu Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Průběh: Provádění úlohy průběh, naleznete informace v informace o fázi.
    * Načtených/zapsaných dat: Heat mapa celkové množství dat načtených/zapsaných v jednotlivých fázích.
    * Výpočetní čas: Heat mapa Sum (každý doba spuštění vrcholu), můžete zvážit to, jak dlouho by trvalo, pokud veškerou práci ve fázi provádí s pouze 1 vrcholu.
    * Průměrná doba provádění na jeden uzel: Heat mapa Sum (každý doba spuštění vrcholu) / (vrchol číslo). Což znamená, že pokud můžete přiřadit všechny vrcholy, které jsou provedeny v paralelismu, celý fáze bude provedeno v tohoto časového rámce.
    * Propustnost vstupu/výstupu: Heat mapa vstupně výstupní propustnost každou z fází, můžete potvrdit, pokud vaše úloha je že vázán vstup nebo výstup úlohy v něm.
* Operace s metadaty
  
    Můžete provádět některé operace s metadaty ve skriptu U-SQL, jako je vytvoření databáze, vyřaďte tabulku atd. Tyto operace jsou uvedeny v operace s metadaty po kompilaci. Může najít kontrolní výrazy, vytvářet entity, Sem přetáhněte entity.
  
    ![Azure Data Lake Analytics úlohy zobrazení operace s metadaty](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historie stavů
  
    Historie stavu je také vizualizovat v souhrnu úlohy, ale můžete získat další podrobnosti tady. Podrobné informace najdete, jako je při přípravě úlohy ve frontě, Začínáme spuštění skončila. Taky můžete najít kolikrát byl zkompilován úlohy (CcsAttempts: 1), když je úloha odeslaných do clusteru ve skutečnosti (podrobnosti: Odesílání úloh do clusteru), atd.
  
    ![Historie stavu Azure Data Lake Analytics úlohy zobrazení](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostika
  
    Nástroj automaticky diagnostikuje provádění úlohy. Pokud nějaké chyby nebo problémy s výkonem ve svých úlohách, dostanete upozornění. Všimněte si, že budete muset stáhnout profil Chcete-li získat úplné informace zde. 
  
    ![Diagnostika Azure Data Lake Analytics úlohy zobrazení](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Upozornění: Upozornění se zobrazí tady s upozorněním kompilátoru. Můžete kliknout na odkaz "x problémů" obsahují více podrobností, jakmile se zobrazí upozornění.
  * Vrchol spuštění příliš dlouhá: Pokud libovolný vrchol běží mimo dobu (třeba 5 hodin), problémů dispozici zde.
  * Využití prostředků: Pokud jste přidělili další nebo není dostatek paralelismu, než potřebujete, problémů dispozici zde. Také můžete kliknout na využití prostředků a zobrazte další podrobnosti a provádět scénáře co kdyby k vyhledání lepší přidělování prostředků (Další podrobnosti najdete v této příručce).
  * Kontrola paměti: Pokud žádné vrcholu používá více než 5 GB paměti, problémů dispozici zde. Provádění úlohy může získat ukončen systému, pokud je větší spotřebu paměti než omezení systému.

## <a name="job-detail"></a>Podrobnosti úlohy
Podrobnosti úlohy zobrazuje podrobné informace o úloze včetně skriptu, prostředků a zobrazení provádění vrcholu.

![Podrobnosti úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    Tento skript U-SQL úlohy je uložený v úložišti dotazů. Můžete zobrazit původní skript U-SQL a znovu odeslat v případě potřeby.
* Zdroje a prostředky
  
    Můžete najít výstupy úlohy kompilace, uložená v úložišti dotazů prostřednictvím prostředků. Například můžete najít "algebra.xml", který slouží k zobrazení grafu úlohy sestavení, které jste zaregistrovali, zde atd.
* Zobrazení provádění vrcholů
  
    Vrcholy zobrazuje podrobnosti o spuštění. Profil úlohy archivuje každý protokolu spuštění vrcholu, jako je například celkové množství dat načtených/zapsaných, modul runtime, stavu, atd. Prostřednictvím tohoto zobrazení můžete získat další informace o tom, jak úloha běžela. Další informace najdete v tématu [použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Použití zobrazení provádění vrcholů, najdete v článku [použití zobrazení provádění vrcholů v Data Lake Tools pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

