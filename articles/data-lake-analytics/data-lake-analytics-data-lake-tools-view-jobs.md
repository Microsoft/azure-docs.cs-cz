---
title: Pomocí prohlížeče úlohy a zobrazení úloh pro úlohy Azure Data Lake Analytics | Microsoft Docs
description: 'Naučte se používat prohlížeč úlohy a zobrazení úloh pro úlohy Azure Data Lake Analytics. '
services: data-lake-analytics
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2017
ms.author: jgao
ms.openlocfilehash: cc9048566dd58733cd86aac0f9836763fa095843
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics-jobs"></a>Pomocí prohlížeče úlohy a zobrazení úloh pro úlohy Azure Data lake Analytics
Služba Azure Data Lake Analytics archivy odeslaná úloh v [úložiště dotazů](#query-store). V tomto článku zjistěte, jak používat prohlížeč úlohy a zobrazení úloh v nástrojů Azure Data Lake pro Visual Studio k nalezení informací o historie úlohy. 

Ve výchozím nastavení služba Data Lake Analytics archivy úlohy po dobu 30 dnů. Je doba vypršení platnosti lze nakonfigurovat z portálu Azure tak, že nakonfigurujete zásady přizpůsobené vypršení platnosti. Nebudete mít přístup k informacím úlohy po vypršení platnosti. 

## <a name="prerequisites"></a>Požadavky
V tématu [nástroje Data Lake pro Visual Studio požadavky](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otevřete prohlížeč úlohy
Přístup k úloze prohlížeče prostřednictvím **Průzkumníka serveru > Azure > Data Lake Analytics > úlohy** v sadě Visual Studio.  Pomocí prohlížeče úlohy, získat přístup k úložišti dotazů účtu Data Lake Analytics. Úloha prohlížeče zobrazí úložiště dotazů na levé straně, informací o základní úloh a zobrazení úloh na pravé zobrazující podrobné informace o úlohách.

## <a name="job-view"></a>Zobrazení úlohy
Zobrazení úloh jsou uvedené podrobné informace o úlohy. Chcete-li spustit úlohu, můžete dvakrát klikněte na úlohu v prohlížeči úlohy nebo otevřít z nabídky Data Lake klepnutím zobrazení úloh. Měli byste vidět, zobrazí se dialogové okno naplněný adresu URL úlohy.

![Nástroje data Lake Visual Studio úlohy prohlížeče](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Obsahuje zobrazení úloh:

* Souhrn úlohy
  
    Aktualizujte zobrazení úlohy zobrazíte nejnovější informace o spuštěných úloh.
  
  * Stav úlohy (grafu):
    
      Stav úlohy popisuje fáze úlohy:
    
      ![Fáze stav úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Příprava: Odešlete skript do cloudu, kompilace a optimalizace pomocí služby kompilace skriptu.
    * Zařazených do fronty: Úlohy se zařadí do fronty při čekání na dostatek prostředků nebo úlohy překročí maximální počet souběžných úloh na omezení účtu. Nastavení priority určuje pořadí úloh zařazených do fronty - Čím nižší je číslo, tím vyšší je priorita.
    * Spuštění: Úloha je ve skutečnosti spuštěna v účtu Data Lake Analytics.
    * Dokončení: Úloha je dokončení (například dokončení souboru).
      
      Úloha může selhat v každé fázi. Například chyby při kompilaci Příprava fáze, vypršení časového limitu ve fázi zařazeno do fronty a provádění chyby ve fázi spuštěná atd.
  * Základní informace
    
      Základní úlohy se zobrazuje v dolní části panelu Souhrn úlohy.
    
      ![Fáze stav úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Výsledek úlohy: Byla úspěšná nebo neúspěšná. Úloha může selhat v každé fázi.
    * Celková doba trvání: Horní času hodin (doba trvání) mezi odesláním čas a koncový čas.
    * Celková doba výpočetní: Součet při každém spuštění vrchol, můžete zvážit ho jako čas, úloha se spustí v pouze jednoho vrcholu. Celkový počet vrcholy najdete další informace o vrchol odkazovat.
    * Odeslat/počáteční/koncový čas: Čas, kdy služba Data Lake Analytics přijme úlohy odeslání nebo začne spustit úlohu nebo ukončí úlohu úspěšně nebo ne.
    * Kompilace nebo zařazeno do fronty nebo spuštění: Skutečný čas strávený během fáze příprava nebo zařazeno do fronty nebo spuštěna.
    * Účet: Účet Data Lake Analytics používaný ke spuštění úlohy.
    * Autor: Uživatel, který odeslal úlohu, může být skutečný člověk účet nebo účet system.
    * Priorita: Prioritu úlohy. Čím nižší je číslo, tím vyšší je priorita. Ovlivňuje pouze pořadí úloh ve frontě. Nastavení s vyšší prioritou není mají prioritu před spuštěné úlohy.
    * Paralelismus: Požadovaný maximální počet souběžných Azure Data Lake Analytics jednotky (ADLAUs), neboli vrcholy. V současné době jednoho vrcholu rovná jeden virtuální počítač s dva virtuální základní a 6 GB paměti RAM, i když může být upgradována v budoucnosti Data Lake Analytics aktualizací.
    * Zbývající bajty: Bajtů, které je třeba zpracovat až do dokončení úlohy.
    * Číst/zapsaných bajtů: bajtů, které byly číst nebo zapisovat od spuštění úlohy.
    * Celkový počet vrcholy: Úloha je rozdělena na mnoha pracovními, každou část práce se nazývá vrchol. Tato hodnota popisuje kolik jednotek zpracování úlohy se skládá z. Můžete zvážit vrchol jako základní proces jednotku, neboli Azure Data Lake Analytics jednotky (ADLAU), a vrcholy se může spouštět ve stupně paralelního zpracování. 
    * Byla dokončena nebo spuštěná nebo se nezdařilo: Počet vrcholy dokončit nebo spuštěná nebo se nezdařilo. Vrcholy může selhat z důvodu selhání kódu a systému i uživatele, ale systému opakované pokusy byly neúspěšné vrcholy automaticky několikrát. Pokud po opakovaném pokusu se stále nezdařilo vrchol, celé úlohy se nezdaří.
* Graf úlohy
  
    Skript U-SQL představuje logiku transformace vstupní data na výstupní data. Skript je zkompilovat a optimalizovaná tak, aby na fázi Příprava fyzických spuštění plánu. Graf úlohy je zobrazit plán fyzické spuštění.  Následující diagram znázorňuje proces:
  
    ![Fáze stav úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Úloha je rozdělena na mnoha pracovními. Každý část práce se nazývá vrchol. Vrcholy jsou seskupeny jako Super vrchol (neboli stage) a vizualizována jako graf úlohy. Malá zelená fázi v grafu úlohy zobrazit fáze.
  
    Všechny vrcholy ve fázi provádí stejný druh pracují s různá stejné data. Například pokud máte soubor s jedním datovým TB a používají stovky vrcholy čtení z něj, každý z nich je čtení bloku. Tyto vrcholy jsou seskupeny ve stejné fáze a to stejné fungovat na různých částí stejné vstupní soubor.
  
  * <a name="state-information"></a>Fáze informace
    
      V konkrétní fázi jsou uvedeny některé čísla v štítek.
    
      ![Fáze graf úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 Extrahování: název fáze, s názvem číslo a metodu operaci.
    * 84 vrcholy: Celkový počet vrcholy v této fázi. Na obrázku Určuje, kolik pracovními je rozdělený v této fázi.
    * 12.90 s/vrchol: vrchol průměrný čas provádění pro tuto fázi. Tato hodnota je vypočítána pomocí SUM (každých čas provádění vrchol) / (celková vrchol počet). To znamená, pokud může přiřadit všechny vrcholy spustit v paralelismus, celou fáze je dokončit 12.90 s. Také znamená, pokud všechny v této fázi práci sériově, náklady na by #vertices * Průměrný čas.
    * 850,895 řádků zapsaných: Celkový počet řádků, které jsou napsané v této fázi.
    * R/w: množství dat pro čtení nebo Written v této fázi v bajtech.
    * Barvy: Barvy jsou ve fázi slouží k určení stavu různých vrchol.
      
      * Zelená značí, že je úspěšně vrchol.
      * Oranžová označuje, že se pokus o vrchol. Opakován vrchol byl selhal, ale je opakovat automaticky a úspěšně systému, a celkový fáze byla úspěšně dokončena. Pokud vrchol opakovat, ale stále se nezdařilo, změní barvu red a celý úlohy se nezdařilo.
      * Červená značí selhání, což znamená, že určité vrchol obsahovala byla několikrát opakována v systému, ale stále se nezdařilo. Tento scénář způsobí selhání celé úlohy.
      * Modré znamená určité vrchol je spuštěna.
      * Prázdné označuje vrchol čeká. Vrchol může čekat třeba naplánovat až ADLAU bude k dispozici, nebo ho může být čekání na vstup od jeho vstupních dat nemusí být připravené.
      
      Můžete najít další podrobnosti pro fázi podržením ukazatele myši podle jednoho stavu:
      
      ![Graf fáze podrobnosti úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vrcholy: Popisuje podrobnosti vrcholy, například kolik vrcholy celkem, kolik vrcholy byly dokončeny, jsou jejich se nezdařilo nebo je stále spuštěná nebo čekání atd.
  * Číst data mezi nebo uvnitř pod: soubory a data jsou uloženy v několika pracovními stanicemi soustředěnými kolem v systému souborů DFS. Hodnota popisuje, jaké množství dat byl načten ve stejné pod nebo křížové pod.
  * Celkový čas výpočetní: sum pokaždé, když provádění vrchol ve fázi, můžete zvážit ho jako čas, bude potřeba, pokud všechny fungovat ve fázi se spouštějí v pouze jednoho vrcholu.
  * Data a řádky zapsána/čtení: Určuje, kolik byly číst nebo zapisovat data nebo řádky, nebo musí přečíst.
  * Vrchol číst selhání: Popisuje, kolik vrcholy došlo k selhání při čtení dat.
  * Vrchol duplicitní zahodí: Pokud vrchol běží příliš pomalu, systém může naplánovat více vrcholy ke spuštění stejnou část práce. Reductant vrcholy se zahodí, po jednu vrcholů úspěšně dokončit. Duplicitní zahození vrchol zaznamenává počet vrcholy, které jsou zahozeny jako duplicity vztahující se k ve fázi.
  * Vrchol odvolání: vrchol bylo úspěšné, ale získat z nějakého důvodu později znovu. Například pokud podřízené vrchol ztratí zprostředkující vstupních dat, bude dotaz nadřazeného vrchol spusťte znovu.
  * Vrchol plán spuštěních: celkový čas, který bylo naplánováno vrcholy.
  * Průměr/minimální/maximální vrchol data načtená: minimální/průměrný nebo maximální počet všechny vrcholy číst data.
  * Doba trvání: Skutečný čas úsek trvá, je třeba načíst profil zobrazíte tuto hodnotu.
  * Přehrávání úloh
    
      Spustí úlohy data Lake Analytics a archivy vrcholy systémem informace úloh, jako například při spuštění vrcholy zastavená, se nezdařilo a jak jsou opakovat, atd. Všechny informace automaticky je zaznamenána v úložišti dotazů a uložen v jeho profil úlohy. Si můžete stáhnout profil úlohy prostřednictvím "Profil zatížení" v zobrazení úloh a přehrávání úloh lze zobrazit po stažení profilu úlohy.
    
      Přehrávání úloh je vizualizaci epitome co se stalo v clusteru. Pomáhá sledovat průběh provádění úlohy a vizuálně detekovat kritická místa v velmi krátké době (méně než 30s obvykle) a anomálie výkonu.
  * Úloha Heat mapa zobrazení 
    
      Heat mapa úlohy lze vybrat pomocí rozevíracího seznamu zobrazení v grafu úlohy. 
    
      ![Azure Data Lake Analytics úlohy graf haldy zobrazení mapy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Zobrazuje heat mapa vstupně-výstupních operací, čas a propustnost úlohy, pomocí kterého můžete najít kde úloha stráví ve většině případů, nebo zda úlohu úlohu hranic vstupně-výstupních operací a tak dále.
    
      ![Příklad haldy mapy úlohy graf Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Postup: Provedení úloh průběhu, přečtěte si informace v [Příprava informace](#stage-information).
    * Číst nebo zapisovat data: heat mapa celkový dat číst nebo zapisovat v jednotlivých fázích.
    * Výpočetní čas: heat mapa Sum (každých čas provádění vrchol), můžete zvážit to jak dlouho bude trvat, pokud všechnu práci ve fázi proveden s pouze 1 vrchol.
    * Průměrná doba provádění na jeden uzel: heat mapa Sum (každých čas provádění vrchol) / (vrchol číslo). Což znamená, že pokud může přiřadit všechny vrcholy spustit v paralelismus, fázi celou bude provedeno v této časového rámce.
    * Propustnost vstupu a výstupu: heat mapa propustnosti vstupu a výstupu u každé fáze, můžete potvrdit, pokud vaše úlohy vázané úlohu vstupně-výstupní operace přes tento.
* Operace s metadaty
  
    Můžete provádět některé operace metadata ve vašem skriptu U-SQL, jako například vytvoření databáze, drop table atd. Tyto operace jsou uvedené v operace metadat po kompilace. Může najít kontrolní výrazy, vytvořte entity, místo pro entity.
  
    ![Operace v Azure Data Lake Analytics úlohy zobrazení metadat](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historie stavů
  
    Historie stavů jsou také vizualizována ve Souhrn úlohy, ale můžete získat další informace v tomto poli. Podrobné informace můžete najít tak, jak připravit úlohu ve frontě, byl ukončen Začínáme spuštěná. Taky můžete najít kolikrát byl zkompilován úlohy (CcsAttempts: 1), když je úloha odeslaných do clusteru ve skutečnosti (podrobností: odesílání úloh do clusteru) atd.
  
    ![Azure Data Lake Analytics úlohy zobrazení historie stavů](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostika
  
    Nástroj automaticky diagnostikuje provádění úlohy. Pokud jsou některé chyby nebo problémy s výkonem v úlohách, budou dostávat upozornění. Upozorňujeme, že budete muset stáhnout profil získat úplné informace v tomto poli. 
  
    ![Azure Data Lake Analytics úlohy zobrazení diagnostiky](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Upozornění: Výstraha se zde zobrazuje s upozornění kompilátoru. Můžete kliknout na odkaz "x problém(y)" tak, aby měl další podrobnosti, jakmile se zobrazí upozornění.
  * Vrchol spustit příliš dlouhá: Pokud libovolný bod uchycení používá mimo dobu (například 5 hodin), problémy se nachází tady.
  * Využití prostředků: Pokud jste přidělili další nebo není dostatek paralelismus, než je nutné, problémy se nachází tady. Také můžete kliknout na využití prostředků najdete v části Další podrobnosti a provádět citlivostních scénářů najít lepší přidělení prostředků (Další podrobnosti najdete v této příručce).
  * Zkontrolujte paměť: Pokud libovolný bod uchycení používá více než 5 GB paměti, problémy se nachází tady. Provádění úlohy může získat ukončená systému, pokud používá víc paměti než omezení systému.

## <a name="job-detail"></a>Podrobnosti úlohy
Podrobnosti úlohy jsou uvedené podrobné informace o úlohy, včetně skriptu, prostředky a zobrazení provádění vrcholů.

![Podrobnosti úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    Tento skript U-SQL úlohy je uložený v úložišti dotazů. Můžete zobrazit původní skript U-SQL a odešlete ji znovu, v případě potřeby.
* Zdroje a prostředky
  
    Můžete najít výstupy úlohy kompilace uložené v úložišti dotazů prostřednictvím prostředků. Například můžete najít "algebra.xml", který se používá k zobrazení grafu úlohy, sestavení, které jste zaregistrovali, zde atd.
* Zobrazení provádění vrcholů
  
    Podrobnosti o provádění zobrazuje vrcholy. Profil úlohy archivy každých protokolu spuštění vrchol, jako je například celková data číst nebo zapisovat, modul runtime, stav, atd. Prostřednictvím tohoto zobrazení můžete získat další informace o tom, jak úloha běžela. Další informace najdete v tématu [použít zobrazení provádění vrcholů v nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pomocí zobrazení provádění vrcholů naleznete v části [použít zobrazení provádění vrcholů v nástrojů Data Lake pro Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)

