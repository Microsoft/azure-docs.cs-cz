---
title: Použití & zobrazení úloh v prohlížeči úloh – Azure Data Lake Analytics
description: Tento článek popisuje, jak použít prohlížeč úloh a zobrazení úloh pro Azure Data Lake Analytics úlohy.
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.topic: conceptual
ms.date: 08/02/2017
ms.openlocfilehash: 2d33a6ec5ff6b687913914e9433b85765aaa7aec
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309938"
---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics"></a>Použití prohlížeče úloh a zobrazení úloh pro Azure Data Lake Analytics
Služba Azure Data Lake Analytics archivuje odeslané úlohy do úložiště dotazů. V tomto článku se naučíte, jak pomocí prohlížeče úloh a zobrazení úloh v Nástroje Azure Data Lake pro Visual Studio najít historické informace o úlohách. 

Ve výchozím nastavení služba Data Lake Analytics archivuje úlohy po dobu 30 dnů. Dobu platnosti můžete nakonfigurovat z Azure Portal konfigurací přizpůsobených zásad vypršení platnosti. Po vypršení platnosti nebude možné získat přístup k informacím o úloze. 

## <a name="prerequisites"></a>Požadavky
[Požadavky sady Visual Studio najdete v tématu Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md#prerequisites).

## <a name="open-the-job-browser"></a>Otevřete prohlížeč úloh.
Přístup k prohlížeči úloh prostřednictvím **Průzkumník serveru > Azure > Data Lake Analytics > úloh** v aplikaci Visual Studio.  Pomocí prohlížeče úloh můžete získat přístup k úložišti dotazů účtu Data Lake Analytics. Prohlížeč úloh zobrazuje na levé straně úložiště dotazů, zobrazuje základní informace o úlohách a zobrazení úloh na pravé straně znázorňující podrobné informace o úlohách.

## <a name="job-view"></a>Zobrazení úlohy
Zobrazení úlohy zobrazuje podrobné informace o úloze. K otevření úlohy můžete dvakrát kliknout na úlohu v prohlížeči úloh nebo otevřít z nabídky Data Lake kliknutím na zobrazení úlohy. Měl by se zobrazit dialogové okno naplněné adresou URL úlohy.

![Prohlížeč úloh v nástroji Visual Studio pro Data Lake](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

Zobrazení úlohy obsahuje:

* Souhrn úlohy
  
    Aktualizujte zobrazení úlohy a podívejte se na nejnovější informace o spuštěných úlohách.
  
  * Stav úlohy (graf):
    
      Stav úlohy popisuje fáze úlohy:
    
      ![Stav fází Azure Data Lake Analytics úloh](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * Připraven Nahrání skriptu do cloudu, kompilování a optimalizace skriptu pomocí služby kompilace.
    * Ve frontě Úlohy jsou zařazené do fronty, když čekají na dostatek prostředků, nebo úlohy překračují maximální počet souběžných úloh na účet. Nastavení priority určuje pořadí úloh ve frontě – čím nižší je číslo, tím vyšší je priorita.
    * Instalovanou Úloha je ve skutečnosti spuštěna v účtu Data Lake Analytics.
    * Dokončuje Úloha se dokončuje (například finalizace souboru).
      
      Úloha může selhat v každé fázi. Například chyby kompilace ve fázi přípravy, chyby časového limitu ve frontě a chyby spuštění ve fázi běhu atd.
  * Základní informace
    
      Základní informace o úloze se zobrazí v dolní části panelu souhrnu úlohy.
    
      ![Stav fází Azure Data Lake Analytics úloh](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * Výsledek úlohy: Bylo úspěšné nebo neúspěšné. Úloha se nemusí podařit v každé fázi.
    * Celková doba trvání: Doba na zdi (doba trvání) mezi odesláním času a času ukončení.
    * Celková doba výpočtu: Součet každé doby provádění vrcholu, můžete ji zvážit jako čas, kdy se úloha spustí pouze v jednom vrcholu. Další informace o vrcholu najdete v části celkový počet vrcholů.
    * Čas odeslání/zahájení/ukončení: Čas, kdy služba Data Lake Analytics obdrží odeslání úlohy/spustí úlohu/ukončí úlohu nebo skončí úspěšně.
    * Kompilace/fronta/spuštění: Čas hodin strávený při přípravě/spuštění ve fázi
    * Zohledňují Účet Data Lake Analytics použitý ke spuštění úlohy.
    * Autorizova Uživatel, který úlohu odeslal, může být účtem reálné osoby nebo účtem systému.
    * Priorita: Priorita úlohy. Čím nižší číslo, tím vyšší Priorita. Ovlivňuje jenom pořadí úloh ve frontě. Nastavením vyšší priority nedojde k přerušení spuštěných úloh.
    * Paralelismu Požadovaný maximální počet souběžných Azure Data Lake Analyticsch jednotek (ADLAUs), neboli vrcholů. V současné době je jeden vrchol stejný jako jeden virtuální počítač se dvěma virtuálními jádry a šesti GB paměti RAM, ale můžete ho upgradovat v budoucích Data Lake Analytics aktualizacích.
    * Zbývající bajty: Bajty, které je třeba zpracovat až do dokončení úlohy.
    * Přečtené a zapsané bajty: Počet bajtů, které byly přečteny nebo zapsány od spuštění úlohy.
    * Celkový počet vrcholů: Úloha je rozdělená do mnoha pracovních částí, každá práce se nazývá vrchol. Tato hodnota popisuje, kolik částí práce se úloha skládá z. Můžete uvažovat o vrcholu jako základní jednotku procesu, neboli Azure Data Lake Analytics jednotky (ADLAU) a vrcholy lze spustit paralelně. 
    * Dokončeno/spuštěno/selhalo: Počet dokončených a spuštěných/neúspěšných vrcholů. Vrcholy mohou selhat z důvodu chyby v uživatelském kódu a systému, ale systém opakuje automaticky selhání vrcholů. Pokud se vrchol stále nezdařil po opakování, celá úloha selže.
* Graf úlohy
  
    Skript U-SQL představuje logiku transformace vstupních dat do výstupních dat. Skript je zkompilován a optimalizován pro fyzický plán spuštění ve fázi přípravy. Graf úlohy je zobrazit plán fyzického spuštění.  Následující obrázek znázorňuje proces:
  
    ![Stav fází Azure Data Lake Analytics úloh](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    Úloha je rozdělená do mnoha částí práce. Každý díl práce se nazývá vrchol. Vrcholy jsou seskupené jako super vrcholy (neboli fáze) a vizuálů jako graf úlohy. Fáze zelené fáze placards v grafu úlohy zobrazují fáze.
  
    Každý vrchol ve fázi má stejný druh práce s různými částmi stejných dat. Například pokud máte soubor s jedním z TB dat a z něj jsou čteny stovky vrcholů, každý z nich čte blok. Tyto vrcholy se seskupují ve stejné fázi a provádějí stejnou práci na různých kusech stejného vstupního souboru.
  
  * <a name="state-information"></a>Informace o fázi
    
      V určité fázi se na štítku zobrazí některá čísla.
    
      ![Azure Data Lake Analytics fáze grafu úlohy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 extrahování: Název fáze pojmenovaný číslem a metodou operace.
    * 84 vrcholů: Celkový počet vrcholů v této fázi. Obrázek označuje, kolik pracovních částí je v této fázi rozděleno.
    * 12,90 s/vrchol: Průměrná doba provádění vrcholu pro tuto fázi. Tento obrázek se vypočítá pomocí SUM (každý čas spuštění vrcholu)/(celkový počet vrcholů). To znamená, že pokud jste mohli přiřadit všechny vrcholy provedené paralelně, je celá fáze dokončena v 12,90 s. Také to znamená, že veškerá práce v této fázi se provádí po sériovém stavu, náklady budou #vertices × Průměrná doba.
    * 850 895 zapsaných řádků: Celkový počet řádků napsaný v této fázi
    * R/W: Objem dat načtených/zapsaných v této fázi v bajtech.
    * Barvy Barvy se ve fázi používají k označení jiného stavu vrcholu.
      
      * Zelená značí, že vrchol byl úspěšný.
      * Oranžová označuje opakování vrcholu. Vrchol opakovaného pokusu se nezdařil, ale systém byl automaticky opakován a úspěšně proveden a celková fáze byla úspěšně dokončena. Pokud se znovu pokusíte vrcholu, ale pořád se nezdařil, barva se změní na červenou a celá úloha se nezdařila.
      * Červená signalizuje neúspěch, což znamená, že určitý vrchol byl systémem opakován několikrát, ale stále se nezdařil. Tento scénář způsobí selhání celé úlohy.
      * Blue znamená, že je spuštěný určitý vrchol.
      * Bílá indikuje, že vrchol čeká. Vrchol může čekat na naplánování, jakmile bude ADLAU k dispozici, nebo může čekat na vstup, protože vstupní data nemusí být připravena.
      
      Další podrobnosti o této fázi najdete po přesunutí ukazatele myši na jeden stav:
      
      ![Podrobnosti fáze grafu Azure Data Lake Analytics úlohy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * Vrcholy Popisuje podrobné informace o vrcholech, například o počtu dokončených vrcholů, o tom, kolik vrcholů bylo dokončeno, že se nezdařily nebo stále běží/čekají atd.
  * Data přečtená mezi/v pod: Soubory a data jsou v systému souborů DFS uloženy v několika luskech. Hodnota zde popisuje, kolik dat bylo přečteno ve stejném nebo více pod.
  * Celková doba výpočtu: Součet všech časů provádění vrcholu ve fázi můžete zvážit jako čas, který by měl trvat, pokud se veškerá práce ve fázi spustí jenom v jednom vrcholu.
  * Zapsané/přečtené data a řádky: Určuje, kolik dat nebo řádků bylo přečteno/zapsáno nebo je nutné je přečíst.
  * Selhání čtení vrcholu: Popisuje, kolik vrcholů se při čtení dat nezdařilo.
  * Duplicitní zahození vrcholu: Pokud je vrchol spuštěný příliš pomalu, může systém naplánovat spuštění stejné práce pomocí více vrcholů. Po úspěšném dokončení jednoho z vrcholů se odeberou vrcholy. Při duplicitním zahození vrcholu se zaznamenává počet vrcholů, které se ve fázi zahodí jako duplicity.
  * Odvolání vrcholů: Vrchol byl úspěšný, ale z nějakého důvodu ho znovu spusťte později. Pokud například u podřízeného vrcholu dojde ke ztrátě zprostředkujících vstupních dat, požádá o opětovné spuštění počátečního vrcholu.
  * Spuštění plánu vrcholu: Celková doba, po kterou byly vrcholy naplánovány.
  * Minimum/průměr/maximum přečtených dat vrcholů: Minimum/průměr/maximum každého data pro čtení vrcholu.
  * Úkolu Doba, po kterou fáze trvá, je nutné načíst profil, aby se tato hodnota zobrazila.
  * Přehrání úlohy
    
      Data Lake Analytics spouští úlohy a archivuje vrcholy, ve kterých se spouštějí informace o úlohách, například když jsou vrcholy spouštěny, zastaveny, selhání a způsob opakování, atd. Všechny informace se automaticky přihlásí do úložiště dotazů a uloží se do svého profilu úlohy. Profil úlohy si můžete stáhnout přes profil zatížení v zobrazení úloh a po stažení profilu úlohy si můžete zobrazit přehrávání úlohy.
    
      Přehrávání úloh je epitomeá vizualizace toho, co se stalo v clusteru. Pomáhá sledovat průběh provádění úlohy a vizuálně zjistit anomálie výkonu a kritická místa v velmi krátké době (méně než 30 s obvykle).
  * Zobrazení Heat mapy úlohy 
    
      Heat mapa úlohy se dá vybrat prostřednictvím rozevíracího seznamu zobrazení v grafu úloh. 
    
      ![Azure Data Lake Analytics zobrazení mapy haldy grafu úlohy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      Zobrazuje vstupně-výstupní, časovou a propustnostní mapu úlohy, pomocí kterých můžete najít, kde se úloha ve většině času tráví, nebo jestli je úloha hranice vstupně-výstupních operací, a tak dále.
    
      ![Příklad mapy haldy grafu úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * Přejde Průběh provádění úlohy najdete v informacích v části informace o fázi.
    * Přečtená/zapsaná data: Heat mapa celkových dat načtených/zapsaných v každé fázi.
    * Výpočetní čas: Heat mapa SOUČTu (každý čas spuštění vrcholu), můžete to vzít v úvahu, jak dlouho bude trvat, pokud bude provedena veškerá práce ve fázi s pouze 1 vrcholem.
    * Průměrná doba provádění na uzel: Heat mapa SOUČTu (každý čas spuštění vrcholu)/(číslo vrcholu). To znamená, že pokud byste mohli přiřadit všechny vrcholy provedené paralelně, bude v tomto časovém rámci provedena celá fáze.
    * Vstupní/výstupní propustnost: Heat mapa propustnosti vstupu a výstupu v jednotlivých fázích, můžete potvrdit, zda se jedná o úlohu s vstupně-výstupní vazbou.
* Operace s metadaty
  
    V rámci skriptu U-SQL můžete provádět některé operace s metadaty, jako je například vytvoření databáze, vyřazení tabulky atd. Tyto operace jsou po kompilaci zobrazeny v operaci s metadaty. Můžete najít kontrolní výrazy, vytvořit entity, sem přetáhněte entity.
  
    ![Operace s metadaty zobrazení Azure Data Lake Analytics úlohy](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* Historie stavů
  
    Historie stavu je také vizuálně shrnutá v souhrnu úlohy, ale tady můžete získat další podrobnosti. Můžete najít podrobné informace, jako je například při přípravě úlohy, ve frontě spuštěno, ukončeno. Můžete také zjistit, kolikrát byla úloha zkompilována (CcsAttempts: 1), kdy je úloha odesílána ve skutečnosti do clusteru (podrobnosti: Probíhá odesílání úlohy do clusteru) atd.
  
    ![Historie stavu zobrazení úlohy Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* Diagnostika
  
    Nástroj diagnostikuje spouštění úlohy automaticky. Pokud dojde k nějakým chybám nebo problémům s výkonem v úlohách, zobrazí se upozornění. Nezapomeňte prosím, že pokud chcete získat úplné informace, musíte si stáhnout profil. 
  
    ![Diagnostika zobrazení úloh Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * Varování Tady se zobrazí výstraha s upozorněním kompilátoru. Kliknutím na odkaz počet problémů x můžete zobrazit další podrobnosti, jakmile se zobrazí výstraha.
  * Spuštění vrcholu je příliš dlouhé: Pokud dojde k nějakému vrcholu mimo čas (například 5 hodin), najdete tady problémy.
  * Využití prostředků: Pokud jste přidělili více nebo nedostatečné Paralelismuy, než je potřeba, budou zde problémy nalezeny. Také můžete kliknout na využití prostředků a zobrazit další podrobnosti a scénáře, co když chcete najít lepší přidělení prostředků (Další informace najdete v této příručce).
  * Kontrolu paměti: Pokud libovolný vrchol používá více než 5 GB paměti, budou zde problémy nalezeny. Spuštění úlohy může být ukončeno systémem, pokud využívá více paměti, než je omezení systému.

## <a name="job-detail"></a>Podrobnosti úlohy
Podrobnosti úlohy zobrazují podrobné informace o úloze, včetně skriptu, prostředků a zobrazení spouštění vrcholů.

![Podrobnosti o úloze Azure Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* Skript
  
    Skript U-SQL úlohy je uložený v úložišti dotazů. V případě potřeby můžete zobrazit původní skript U-SQL a v případě potřeby ho znovu odeslat.
* Zdroje a prostředky
  
    Výstupy kompilace úlohy uložené v úložišti dotazů můžete najít prostřednictvím prostředků. Například můžete najít "algebraický. XML", který se používá k zobrazení grafu úlohy, vámi registrovaných sestavení atd.
* Zobrazení spuštění vrcholu
  
    Zobrazuje podrobnosti o spuštění vrcholů. Profil úlohy archivuje každý protokol spouštění vrcholů, jako je například celkový počet čtení/zápisu, doba běhu, stav atd. V tomto zobrazení můžete získat další podrobnosti o tom, jak byla úloha spuštěna. Další informace naleznete v tématu [použití zobrazení spuštění vrcholu v nástroji Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).

## <a name="next-steps"></a>Další kroky
* Pokud chcete protokolovat diagnostické informace, přečtěte si téma [Zobrazení protokolů diagnostiky pro Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Pokud chcete zobrazit komplexnější dotaz, přejděte k tématu [Analýza webových protokolů pomocí Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Chcete-li použít zobrazení spuštění vrcholu, přečtěte si téma [použití zobrazení spuštění vrcholu v nástroji Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md) .

