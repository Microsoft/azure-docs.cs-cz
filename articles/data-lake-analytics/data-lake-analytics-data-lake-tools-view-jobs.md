---
title: Použití prohlížeče úloh & zobrazení úloh – Azure Data Lake Analytics
description: Tento článek popisuje, jak používat job browser a job view pro úlohy Azure Data Lake Analytics.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309938"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Použití prohlížeče úloh a zobrazení úloh pro Azure Data Lake Analytics
Archivy služby Azure Data Lake Analytics odeslané úlohy v úložišti dotazů. V tomto článku se dozvíte, jak pomocí prohlížeče úloh a zobrazení úloh v Nástrojích datového jezera Azure pro Visual Studio najít informace o historické úloze. 

Ve výchozím nastavení služba Data Lake Analytics archivuje úlohy po dobu 30 dnů. Dobu vypršení platnosti lze nakonfigurovat z portálu Azure konfigurací přizpůsobených zásad vypršení platnosti. Po vypršení platnosti nebudete mít přístup k informacím o úloze. 

## <a name="prerequisites"></a>Požadavky
Viz [Nástroje datového jezera pro požadavky sady Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otevření job browseru
Přístup k job browser přes **Průzkumník a server>Azure>Data Lake Analytics>úlohy** v sadě Visual Studio.  Pomocí job browseru můžete přistupovat k úložišti dotazů účtu Data Lake Analytics. V prohlížeči úloh se vlevo zobrazí úložiště dotazů se základními informacemi o úloze a vpravo se zobrazením úloh y podrobné informace o úloze.

## <a name="job-view"></a>Zobrazení úlohy
Zobrazení úlohy zobrazuje podrobné informace o projektu. Chcete-li otevřít úlohu, můžete poklepat na úlohu v prohlížeči úloh nebo ji otevřít v nabídce Datové jezero kliknutím na zobrazení úlohy. Měl by se zobrazit dialog naplněný adresou URL úlohy.

![Prohlížeč úloh Aplikace Visual Studio Tools pro datové jezero](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Zobrazení úlohobsahuje:

* Souhrn úlohy
  
    Aktualizujte zobrazení úloh, abyste viděli novější informace o spuštěných úlohách.
  
  * Stav úlohy (graf):
    
      Stav úlohy popisuje fáze úlohy:
    
      ![Stav fází úlohAzure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Příprava: Nahrajte skript do cloudu, kompilaci a optimalizaci skriptu pomocí služby kompilace.
    * Zařazeno do fronty: Úlohy jsou zařazeny do fronty, když čekají na dostatek prostředků, nebo úlohy překročí maximální množství souběžných úloh na omezení účtu. Nastavení priority určuje pořadí úloh ve frontě - čím nižší číslo, tím vyšší je priorita.
    * Běh: Úloha ve skutečnosti běží ve vašem účtu Data Lake Analytics.
    * Dokončení: Úloha se dokončuje (například dokončení souboru).
      
      Úloha může selhat v každé fázi. Například chyby kompilace ve fázi příprava, chyby časového období ve fázi zařazené do fronty a chyby spuštění ve fázi Spuštění atd.
  * Základní informace
    
      Základní informace o úloze se zobrazují v dolní části panelu Souhrn úloh.
    
      ![Stav fází úlohAzure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Výsledek úlohy: Úspěšné nebo neúspěšné. Úloha může selhat v každé fázi.
    * Celková doba trvání: Čas hodin ve zdi (doba trvání) mezi časem odeslání a časem ukončení.
    * Celkový výpočetní čas: Součet každého času spuštění vrcholu, můžete jej považovat za čas, kdy je úloha provedena pouze v jednom vrcholu. Další informace o vrcholu naleznete v svatých vrcholech.
    * Odeslat/Odeslat/Čas ukončení: Čas, kdy služba Data Lake Analytics obdrží odeslání/spuštění úlohy/úspěšné spuštění úlohy/ukončení úlohy.
    * Kompilace/zařazeno do fronty/spuštění: Čas strávený hodinami ve zdi během fáze Příprava/Zařazení do fronty/Spuštění.
    * Účet: Účet Data Lake Analytics používaný pro spuštění úlohy.
    * Autor: Uživatel, který odeslal práci, může to být účet skutečné osoby nebo systémový účet.
    * Priorita: Priorita úlohy. Čím nižší číslo, tím vyšší priorita. Ovlivňuje pouze pořadí úloh ve frontě. Nastavení vyšší priority nebrání spuštění úloh.
    * Paralelismus: Požadovaný maximální počet souběžných jednotek Azure Data Lake Analytics (ADLAU), neboli vrcholů. V současné době jeden vrchol se rovná jednomu virtuálnímu počítači se dvěma virtuálními jádry a šesti GB paměti RAM, i když by to mohlo být upgradováno v budoucích aktualizacích Data Lake Analytics.
    * Počet bajtů vlevo: Bajtů, které je třeba zpracovat, dokud není úloha dokončena.
    * Bajty pro čtení a zápis: Bajty, které byly přečteny nebo zapsány od spuštění úlohy.
    * Celkový počet vrcholů: Úloha je rozdělena na mnoho děl, každá práce se nazývá vrchol. Tato hodnota popisuje, kolik kusů práce se skládá z. Vrchol můžete považovat za základní procesní jednotku, aka Azure Data Lake Analytics Unit (ADLAU) a vrcholy lze spouštět paralelismu. 
    * Dokončeno/Spuštěno/Nezdařilo se: Počet dokončených/spuštěných/neúspěšných vrcholů. Vrcholy mohou selhat z důvodu selhání uživatelského kódu i systému, ale systém se několikrát automaticky nezdaří. Pokud se vrchol po opakování stále nezdaří, celá úloha se nezdaří.
* Graf úlohy
  
    Skript U-SQL představuje logiku transformace vstupních dat do výstupních dat. Skript je zkompilován a optimalizován na plán fyzického spuštění ve fázi Příprava. Graf úlohy je zobrazit plán fyzického spuštění.  Následující diagram znázorňuje proces:
  
    ![Stav fází úlohAzure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Práce je rozdělena na mnoho děl. Každá práce se nazývá Vrchol. Vrcholy jsou seskupeny jako Super Vertex (aka stage) a vizualizovány jako Graf úloh. Zelené stage plakáty v grafu úlohy zobrazují fáze.
  
    Každý vrchol ve fázi provádí stejný druh práce s různými částmi stejných dat. Například pokud máte soubor s jedním tb dat a existují stovky vrcholů čtení z něj, každý z nich je čtení bloku. Tyto vrcholy jsou seskupeny ve stejné fázi a dělají stejnou práci na různých částech stejného vstupního souboru.
  
  * <a name="state-information"></a>Informace o etapě
    
      V určité fázi jsou některá čísla zobrazena na štítku.
    
      ![Fáze grafu úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extract: Název fáze, pojmenovaný číslem a metodou operace.
    * 84 vrcholů: Celkový počet vrcholů v této fázi. Obrázek ukazuje, kolik kusů práce je rozděleno v této fázi.
    * 12.90 s/vrchol: Průměrná doba spuštění vrcholu pro tuto fázi. Toto číslo se vypočítá pomocí sumy (každý čas spuštění vrcholu) / (celkový počet vrcholů). Což znamená, že pokud byste mohli přiřadit všechny vrcholy provedené v paralelismu, celá fáze je dokončena v 12.90 s. To také znamená, že pokud všechny práce v této fázi se provádí sériově, náklady by #vertices * AVG čas.
    * 850 895 řádků: Celkový počet řádků napsaný v této fázi.
    * R/W: Množství dat přečtených/zapsaných v této fázi v bajtech.
    * Barvy: Barvy se ve fázi používají k označení odlišného stavu vrcholu.
      
      * Zelená znamená, že vrchol byl úspěšný.
      * Oranžová označuje, že vrchol je opakován. Zopakovat vertex se nezdařilo, ale je opakován automaticky a úspěšně systémem a celková fáze je úspěšně dokončena. Pokud se vrchol zopakoval, ale přesto se nezdařil, barva se změní na červenou a celá úloha se nezdařila.
      * Červená barva označuje, že se nezdařila, což znamená, že určitý vrchol byl několikrát opakován systémem, ale přesto selhal. Tento scénář způsobí, že celá úloha nezdaří.
      * Modrá znamená, že určitý vrchol běží.
      * Bílá označuje, že vrchol čeká. Vrchol může čekat na naplánování, jakmile bude k dispozici ADLAU, nebo může čekat na vstup, protože jeho vstupní data nemusí být připravena.
      
      Další podrobnosti o vymezené ploše najdete tak, že kurzorem myši najedete podle jednoho stavu:
      
      ![Podrobnosti o fázi fáze grafu úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vrcholy: Popisuje podrobnosti vrcholů, například kolik vrcholů celkem, kolik vrcholů bylo dokončeno, jsou selhaly nebo stále běží / čekají, atd.
  * Data číst cross/intra pod: Soubory a data jsou uloženy ve více podů v distribuovaném souborovém systému. Hodnota zde popisuje, kolik dat bylo přečteno ve stejném podu nebo cross pod.
  * Celkový výpočetní čas: Součet každého času spuštění vrcholu ve fázi, můžete to považovat za čas, který by trvalo, kdyby byla veškerá práce ve fázi prováděna pouze v jednom vrcholu.
  * Data a řádky napsané/přečtené: Označuje, kolik dat nebo řádků bylo přečteno nebo zapsáno nebo je třeba je číst.
  * Selhání čtení vrcholů: Popisuje, kolik vrcholů se při čtení dat nezdařilo.
  * Duplikáty vrcholu: Pokud vrchol běží příliš pomalu, systém může naplánovat více vrcholů pro spuštění stejného kusu práce. Reduktované vrcholy budou zahozeny, jakmile se jeden z vrcholů úspěšně dokončí. Duplikáty vrcholu zahodí počet vrcholů, které jsou zahozeny jako duplikace ve fázi.
  * Odvolání vrcholů: Vrchol byl úspěšný, ale z některých důvodů se znovu spustí. Například pokud následný vrchol ztratí zprostředkující vstupní data, požádá protiproudý vrchol k opětovnému spuštění.
  * Spuštění plánu vrcholů: Celkový čas, po který byly naplánovány vrcholy.
  * Čtení dat min/průměr/max.
  * Doba trvání: Čas nástěnných hodin, který fáze trvá, musíte načíst profil, abyste viděli tuto hodnotu.
  * Přehrávání úloh
    
      Data Lake Analytics spouští úlohy a archivuje vrcholy, které spouštějí informace o úlohách, například při spuštění, zastavení, selhání a opakování atd. Všechny informace jsou automaticky zaznamenány v úložišti dotazů a uloženy v jeho profilu úlohy. Profil úlohy si můžete stáhnout prostřednictvím možnosti Načíst profil v zobrazení úlohy a po stažení profilu úlohy si můžete zobrazit přehrávání úloh.
    
      Přehrávání úlohje ztělesněním vizualizace toho, co se stalo v clusteru. Pomáhá sledovat průběh provádění úloh a vizuálně zjistit anomálie výkonu a kritické body ve velmi krátkém čase (obvykle méně než 30s).
  * Zobrazení mapy tepla práce 
    
      Job Heat Map lze vybrat prostřednictvím rozbalovací nabídky Zobrazení v grafu úloh. 
    
      ![Zobrazení mapy grafu úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Zobrazuje vstupně-to, čas a propustnost tepla mapy úlohy, jehož prostřednictvím můžete zjistit, kde úloha tráví většinu času, nebo zda vaše práce je vstupně-o hranice úlohy a tak dále.
    
      ![Příklad mapy grafu úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Průběh: Průběh provádění úlohy, viz Informace v informacích o fázi.
    * Čtená/zapsaná data: Tepelná mapa celkových dat přečtených/zapsaných v každé fázi.
    * Výpočetní čas: Tepelná mapa SUMA (každý čas spuštění vrcholu) to můžete považovat za to, jak dlouho by to trvalo, kdyby byla veškerá práce ve fázi provedena pouze s 1 vrcholem.
    * Průměrná doba provádění na uzel: Tepelná mapa SUMA (každý čas spuštění vrcholu) / (Číslo vrcholu). Což znamená, že pokud byste mohli přiřadit všechny vrcholy provedené paralelismem, celá fáze bude provedena v tomto časovém rámci.
    * Propustnost vstupu/výstupu: Tepelná mapa vstupní/výstupní propustnost každé fáze, můžete potvrdit, zda je vaše úloha vstupně-výstupní úlohou.
* Operace metadat
  
    Můžete provádět některé operace metadat ve skriptu U-SQL, jako je například vytvoření databáze, přetažení tabulky atd. Tyto operace jsou zobrazeny v operaci metadat po kompilaci. Můžete najít kontrolní výrazy, vytvořit entity, přetažení entity zde.
  
    ![Operace metadat azure data lake analytics zobrazení úloh](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historie státu
  
    Historie stavu je také vizualizována v souhrnu úloh, ale další podrobnosti můžete získat zde. Můžete najít podrobné informace, například když je úloha připravena, ve frontě, spuštěna, ukončena. Také můžete zjistit, kolikrát byla úloha zkompilována (CcsAttempts: 1), kdy je úloha skutečně odeslána do clusteru (detail: Odeslání úlohy do clusteru) atd.
  
    ![Historie stavu zobrazení úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostika
  
    Nástroj diagnostikuje provádění úloh automaticky. Budete dostávat upozornění, pokud jsou některé chyby nebo problémy s výkonem ve vašich úlohách. Vezměte prosím na vědomí, že musíte stáhnout profil získat úplné informace zde. 
  
    ![Diagnostika zobrazení úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Upozornění: Zobrazí se výstraha s upozorněním kompilátoru. Kliknutím na odkaz "x issue(s)" získáte další podrobnosti, jakmile se výstraha zobrazí.
  * Vrchol běží příliš dlouho: Pokud některý vrchol vyčerpá čas (řekněme 5 hodin), problémy najdete zde.
  * Využití prostředků: Pokud jste přidělili více nebo není dostatek paralelismu než potřeby, problémy najdete zde. Také můžete kliknout na využití prostředků zobrazíte další podrobnosti a provést scénáře co-if najít lepší přidělení prostředků (další podrobnosti naleznete v této příručce).
  * Kontrola paměti: Pokud některý vrchol využívá více než 5 GB paměti, problémy najdete zde. Spuštění úlohy může být usmrceno systémem, pokud používá více paměti než omezení systému.

## <a name="job-detail"></a>Detail úlohy
Podrobnosti úlohy zobrazuje podrobné informace o úloze, včetně skriptu, prostředků a zobrazení spuštění vrcholu.

![Detail úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    U-SQL skript úlohy je uložen v úložišti dotazů. Můžete zobrazit původní u-SQL skript a v případě potřeby jej znovu odeslat.
* Prostředky
  
    Výstupy kompilace úloh y uložené v úložišti dotazů můžete najít prostřednictvím prostředků. Například zde naleznete "algebra.xml", který se používá k zobrazení grafu úloh, sestavení, která jste zaregistrovali atd.
* Zobrazení spuštění vrcholu
  
    Zobrazuje podrobnosti o spuštění vrcholů. Profil úlohy archivuje každý protokol provádění vrcholu, jako je například celkový počet přečtených/zapsal, runtime, stav, atd. Prostřednictvím tohoto zobrazení můžete získat další podrobnosti o tom, jak byla úloha spuštěna. Další informace naleznete [v tématu Use the Vertex Execution View in Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pokud chcete použít zobrazení spuštění vrcholu, [přečtěte si informace o použití zobrazení spuštění vrcholu v nástrojích datového jezera pro Visual Studio.](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

