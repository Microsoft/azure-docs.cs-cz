---
title: Podrobné informace o tom, jak předvídat stavu vozidel a jízdních návycích – Azure | Dokumentace Microsoftu
description: Pomocí možností Cortana Intelligence získat v reálném čase a prediktivní přehledy o stavu vozidel a jízdních návycích.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: deguhath
ms.openlocfilehash: 8c4946ebef8d17d2016d482010768207d5e859ff
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300944"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Řešení analýzy Telemetrie vozidla: Podívejte se na řešení

Tento článek při procházení k podrobnostem do každé fáze použité v řešení. Pokyny a odkazy pro úpravy jsou zahrnuty. 

Souhrnný popis tohoto řešení najdete v tématu [řešení analýzy Telemetrie vozidla playbook](cortana-analytics-playbook-vehicle-telemetry.md).


## <a name="data-sources"></a>Zdroje dat
Toto řešení využívá dvou různých datových zdrojů:

* Signály simulované vozidel a diagnostických datové sady
* Katalog vozidel

Simulátor telematiky vozidel je součástí tohoto řešení, jak je znázorněno na následujícím snímku obrazovky. Vysílá diagnostické informace a signály, které odpovídají státu vozidla a řízení vzor k danému bodu v čase.  Katalog vozidel obsahuje referenční sady dat, která se mapuje na modely vozidla identifikační čísla (VINs). Poznámka: Datové sady telematika vozidel simulátor Visual Studio řešení již není k dispozici. 

![Simulátor telematiky vozidel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Tato datová sada ve formátu JSON obsahuje následující schéma.

| Sloupec | Popis | Hodnoty |
| --- | --- | --- |
| VIN |Náhodně generované VIN |Získané z hlavní seznam 10 000 náhodně generované VINs |
| Vnější teploty |Mimo teploty, kde se zvyšování vozidla |Náhodně generované číslo od 0 do 100 |
| Modul teploty |Modul teploty vozidla |Náhodně generované číslo od 0 do 500 |
| Rychlost |Modul rychlost, jakou má zásadní vliv na vozidla |Náhodně generované číslo od 0 do 100 |
| Palivo |Úroveň posílit vozidla |Náhodně generované číslo od 0 do 100 (určuje procento úrovně posílit) |
| EngineOil |Úroveň ropy stroje vozidla |Náhodně generované číslo od 0 do 100 (určuje procento úrovně ropy modulu) |
| Můžete zadat tlaku |Můžete zadat tlak vozidla |Náhodně generované číslo od 0 do 50 (určuje procento úrovně můžete zadat přetížení) |
| Počítadlo kilometrů |Čtení počítadlo kilometrů vozidla |Náhodně generované číslo od 0 do 200 000 |
| Accelerator_pedal_position |Pozice Pedálové akcelerátoru vozidla |Náhodně generované číslo od 0 do 100 (určuje procento úrovně akcelerátoru) |
| Parking_brake_status |Určuje, zda je nebo není zaparkované vozidla |True nebo False |
| Headlamp_status |Určuje, zda světlometu je na, nebo ne |True nebo False |
| Brake_pedal_status |Určuje, zda pedálu brzdou stisknutí nebo ne |True nebo False |
| Transmission_gear_position |Pozice ozubeného kola přenosu vozidla |Stavy: první, druhý, třetí, čtvrtý, páté, šestého, sedmým, osmého |
| Ignition_status |Určuje, zda je spuštěná nebo zastavená vozidla |True nebo False |
| Windshield_wiper_status |Určuje, zda stírání čelního skla je zapnutá nebo vypnutá |True nebo False |
| ABS |Určuje, zda je nebo není zapojení ABS |True nebo False |
| Časové razítko |Časové razítko, když se vytvoří datový bod |Datum |
| Město |Umístění vozidla |Čtyři měst v tomto řešení: Bellevue, Redmond, Sammamish, Seattle |

Model vozidel referenční sady dat mapuje VINs modely. 

| VIN | Model |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Limuzíny |
| 8J0U8XCPRGW4Z3NQE |Hybridní |
| WORG68Z2PLTNZDBI7 |Řady Sedan |
| JTHMYHQTEPP4WBMRN |Limuzíny |
| W9FTHG27LZN1YWO0Y |Hybridní |
| MHTP9N792PHK08WJM |Řady Sedan |
| EI4QXI2AXVQQING4I |Limuzíny |
| 5KKR2VB4WHQH97PF8 |Hybridní |
| W9NSZ423XZHAONYXB |Řady Sedan |
| 26WJSGHX4MA5ROHNL |Převoditelné |
| GHLUB6ONKMOSI7E77 |Kombi |
| 9C2RHVRVLMEJDBXLP |Kompaktní Auto |
| BRNHVMZOUJ6EOCP32 |Malé SUV |
| VCYVW0WUZNBTM594J |Sportovní auta |
| HNVCE6YFZSA5M82NY |Střední SUV |
| 4R30FOR7NUOBL05GJ |Kombi |
| WYNIIY42VKV6OQS1J |Velké SUV |
| 8Y5QKG27QET1RBK7I |Velké SUV |
| DF6OX2WSRA6511BVG |Kupé |
| Z2EOZWZBXAEW3E60T |Limuzíny |
| M4TV6IEALD5QDS3IR |Hybridní |
| VHRA1Y2TGTA84F00H |Řady Sedan |
| R0JAUHT1L1R3BIKI0 |Limuzíny |
| 9230C202Z60XX84AU |Hybridní |
| T8DNDN5UDCWL7M72H |Řady Sedan |
| 4WPYRUZII5YV7YA42 |Limuzíny |
| D1ZVY26UV2BFGHZNO |Hybridní |
| XUF99EW9OIQOMV7Q7 |Řady Sedan |
| 8OMCL3LGI7XNCC21U |Převoditelné |
| ……. | |

## <a name="ingestion"></a>Příjem
Kombinace Azure Event Hubs, Azure Stream Analytics a Azure Data Factory slouží k ingestování signály vozidel, diagnostických událostí v reálném čase a dávkové analýzy. Všechny tyto součásti jsou vytvořena a nakonfigurována jako součást nasazení řešení. 

### <a name="real-time-analysis"></a>Analýza v reálném čase
Události generované modulem simulátor telematiky vozidel se publikují do centra událostí pomocí sady SDK centra událostí.  

![Řídicí panel centra událostí](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Úlohy Stream Analytics tyto události z centra událostí ingestuje a zpracovává data v reálném čase analyzovat stavu vozidel.

![Stream Analytics úlohy zpracování dat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Úlohy Stream Analytics:

* Ingestuje data z centra událostí.
* Provede připojení k s referenčními daty mapování vozidel VIN odpovídající vzoru. 
* Opakuje je do úložiště objektů Blob v Azure pro bohaté dávkové analýzy. 

Následující dotazu Stream Analytics se používá k uchování dat do úložiště objektů Blob: 

![Dotaz na úlohu Stream Analytics pro příjem dat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Dávková analýza
Další svazek simulované vozidla signály a diagnostických datové sady je také generovány pro bohatší dávkové analýzy. Tento další svazek je nutný k zajištění funkční reprezentativní datový svazek pro dávkové zpracování. K tomuto účelu slouží PrepareSampleDataPipeline v pracovním postupu služby Data Factory ke generování za jeden rok simulované vozidla signály a diagnostických datové sady. Chcete-li stáhnout služby Data Factory vlastní aktivitu .NET řešení sady Visual Studio na základě vašich požadavků na přizpůsobení, přejděte na [služby Data Factory vlastní aktivity](https://go.microsoft.com/fwlink/?LinkId=717077) webové stránky. 

Tento pracovní postup nastiňuje ukázkových dat připravené pro dávkové zpracování.

![Ukázková data připravena pro zpracování pracovní postup služby batch](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Kanál se skládá z vlastní aktivitu Data Factory .NET.

![Aktivita PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Po úspěšně provádí kanálu a RawCarEventsTable datové sady je označený "Připraveno", jsou vytvářeny za jeden rok simulované vozidla signály a diagnostická data. Zobrazí se následující složku a soubor vytvořený ve vašem účtu úložiště v rámci kontejneru connectedcar:

![PrepareSampleDataPipeline výstupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Oddíl datové sady
V kroku přípravy dat nezpracovaná semistrukturovaná vozidla signály a diagnostických datové sady jsou rozdělené do formátu rok/měsíc. Toto rozdělení podporuje více efektivní dotazování a škálovatelné dlouhodobé úložiště tím, že převzetím služeb při selhání. Například jako první účtu služby blob zaplní, ho chyb za další účet. 

>[!NOTE] 
>Tento krok v rámci řešení platí pouze pro dávkové zpracování.

Vstupní a výstupní data správy:

* **Výstupní data** (označené PartitionedCarEventsTable), zůstane po dlouhou dobu jako základní / "rawest" formulář dat ve službě data lake zákazníka. 
* **Vstupní data** do tohoto kanálu se zruší, obvykle vzhledem k tomu, že výstupní data má plnou věrností na vstup. Uloží se (dělené) lépe pro pozdější použití.

Oddíl car události pracovního postupu.

![Oddíl car události pracovního postupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Nezpracovaná data jsou rozdělená pomocí Azure HDInsight Hive aktivita v PartitionCarEventsPipeline, jak je znázorněno na následujícím snímku obrazovky. Ukázková data generovaná pro rok v krok přípravy dat. je rozdělený podle roku a měsíce. Oddíly slouží ke generování vozidla signály a diagnostických dat pro každý měsíc (celkem 12 oddílů) v roce. 

![Aktivita PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Skript PartitionConnectedCarEvents Hive**

Partitioncarevents.hql skript Hive se používá k rozdělení do oddílů. Je umístěn ve složce \demo\src\connectedcar\scripts stažený soubor zip. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
    ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

    INSERT OVERWRITE TABLE Stage_RawCarEvents
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        Year(gendate),
        Month(gendate)

    FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

    INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
    SELECT
        vin,            
        model,
        timestamp,
        outsidetemperature,
        enginetemperature,
        speed,
        fuel,
        engineoil,
        tirepressure,
        odometer,
        city,
        accelerator_pedal_position,
        parking_brake_status,
        headlamp_status,
        brake_pedal_status,
        transmission_gear_position,
        ignition_status,
        windshield_wiper_status,
        abs,
        gendate,
        YearNo,
        MonthNo
    FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

Po úspěšném spuštění kanálu se zobrazí následující oddíly generované ve vašem účtu úložiště v rámci kontejneru connectedcar:

![Dělené výstupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Data je teď optimalizovaná, snáze spravovatelné a je připravený k dalšímu zpracování a získávat poznatky bohaté služby batch. 

## <a name="data-analysis"></a>Analýza dat
V této části naleznete v tématu jak kombinovat bohaté možnosti pokročilé analýzy velkých stavu vozidel a jízdních návycích Stream Analytics, Azure Machine Learning, Data Factory a HDInsight.

### <a name="machine-learning"></a>Strojové učení
Cílem je předpovědět vozidel, které vyžadují údržby nebo odvolání na základě určitých stavu statistik podle následující předpoklady:

* Pokud je splněna jedna z těchto tří podmínek, vozidel vyžaduje servis údržby:
  
  * Můžete zadat přetížení je nízký.
  * Úroveň ropy modul je nízká.
  * Teplota modul je vysoká.

* Pokud platí jedna z následujících podmínek, vozidel může mít bezpečnostní problém a vyžadují odvolání:
  
  * Teplota modul je vysoká, ale dochází mimo teploty.
  * Teploty modulu je nízká, ale mimo teplota je vysoká.

Na základě předchozích požadavků, dvou samostatných modelů detekci anomálií. Jeden model je pro zjišťování údržby vozidla a jeden model je detekce spojené s vracením vozidel. V obou modelech algoritmu integrované hlavních komponentách analýzy (DPS) slouží k detekci anomálií. 

#### <a name="maintenance-detection-model"></a>**Údržba zjišťování modelu**

Pokud tří ukazatelů – můžete zadat tlak, modul olej nebo modul teploty – splňuje stavu odpovídajících, sestavy modelu údržby detekce anomálií. V důsledku toho pouze těchto tří proměnných, třeba vzít v úvahu při vytváření modelu. V experiment v machine learning **výběr sloupců v datové sadě** modul se používá k extrakci těchto tří proměnných. V dalším kroku modulu detekce anomálií založený na PCA slouží k sestavení modelu detekce anomálií. 

DPS je zavedený postup ve službě machine learning, který lze použít pro výběr součástí, klasifikace a detekci anomálií. DPS převede sadu případů, které obsahují potenciálně korelační proměnné do sady hodnot nazývané jako hlavní komponenty. Založený na PCA modelování spočívá klíče na projektová data na nižší rozměrného prostoru pro snazší identifikaci funkce a anomálie.

Pro každý nový vstup do modelu detekce detekce anomálií nejprve vypočítá jeho projekce na eigenvectors. Pak vypočítá normalizované obnovu chyby. Tato chyba normalizované je hodnocení anomálie: Čím vyšší chybě, více neobvyklé instance. 

Zjišťování potíží údržby, každý záznam se považuje za bod v trojrozměrném prostoru podle zatížení můžete zadat, modul ropy a teploty modulu definice souřadnice. Pokud chcete zaznamenat tyto anomálie, DPS slouží k původní data v trojrozměrném prostoru projektu do dvourozměrné prostoru. Díky tomu se číslo parametru komponenty pro použití v DPS nastavena na dva. Tento parametr hraje důležitou roli při používání detekce anomálií založená na PCA. Po použití DPS na data projektu, jsou tyto anomálie více snadno identifikovat.

#### <a name="recall-anomaly-detection-model"></a>**Odvolat modelu detekce anomálií**

V modelu spojené s vracením detekce anomálií **výběr sloupců v datové sadě** a používají se moduly detekce anomálií založený na PCA podobným způsobem. Konkrétně tří proměnných – modul teploty, mimo teploty a rychlost – se extrahují pomocí volby **výběr sloupců v datové sadě** modulu. Rychlost také je proměnná zahrnuté, protože teploty modulu obvykle souvisí s rychlostí. V dalším kroku modulu detekce anomálií založený na PCA slouží k dat z trojrozměrném prostoru projektu do dvourozměrné prostoru. Jsou splněna kritéria odvolání. Při vysoce negativně korelační modul teploty a mimo teploty, vyžaduje vozidla odvolání. Po provedení DPS algoritmus detekce anomálií založený na PCA slouží k zaznamenání anomálie. 

Při tréninku buď modelu, normální data se používají jako vstupní data pro trénování modelu detekce anomálií založený na PCA. (Normální dat nevyžaduje, údržby nebo odvolání.) V hodnoticí experimentu modelu detekce natrénované anomálií slouží ke zjištění, zda nástroj vyžaduje údržby nebo odvolání. 

### <a name="real-time-analysis"></a>Analýza v reálném čase
Následující dotaz Stream Analytics SQL slouží k získání průměr všech parametrů důležité vozidlo. Tyto parametry zahrnují rychlosti, posílit úrovni, teploty modulu, počítadlo kilometrů čtení, můžete zadat přetížení, modul ropy i ostatní. Průměry se používají ke zjištění anomálií, vydávání výstrah a určení podmínek celkového stavu vozidel provozované v určité oblasti. Průměry se pak korelují na demografických údajů. 

![Dotazu Stream Analytics pro zpracování v reálném čase](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Všechny průměry se počítá za tři sekundy aktivační událost pro přeskakující okno. Aktivační událost pro přeskakující okno se používá, protože nepřekrývajících se souvislých časových intervalů. 

Další informace o možnostech oddílová ve službě Stream Analytics najdete v tématu [časová okna (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**Predikce v reálném čase**

Aplikace je součástí řešení pro zprovoznění modelu machine learning v reálném čase. Aplikace RealTimeDashboardApp je vytvořen a nakonfigurován jako součást nasazení řešení. Aplikace:

* Naslouchá instancí centra událostí, kde Stream Analytics publikuje události ve vzorku průběžně.

    ![Dotazu Stream Analytics pro publikování dat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Přijímá události. Pro každou událost, která přijímá této aplikace: 
   
   * Zpracování dat pomocí služby machine learning typu žádost odpověď bodovací koncový bod (RRS). Koncový bod RRS je automaticky publikován jako součást svého nasazení.
   * Výstup RRS se publikuje do datové sady Power BI pomocí nabízené instalace rozhraní API.

Tento model se také vztahuje na scénáře, ve které chcete integrovat – obchodní aplikace s analýzy v reálném čase. Mezi tyto scénáře patří výstrahy, oznámení a zasílání zpráv.

Poznámka: aby data pro řešení sady RealtimeDashboardApp Visual Studio již není k dispozici.

#### <a name="execute-the-real-time-dashboard-application"></a>**Spuštění aplikace v reálném čase řídicí panel**
1. Extrahovat RealtimeDashboardApp a uloží do místního prostředí.

    ![RealTimeDashboardApp složky](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Spuštění aplikace RealtimeDashboardApp.exe.

3. Zadejte platné přihlašovací údaje Power BI a vyberte **přihlášení**.  

    ![Okna přihlášení aplikace pro řídicí panel v reálném čase](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Vyberte **Přijmout**.

    ![Řídicí panel v reálném čase okna aplikace na poslední přihlášení](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Pokud chcete vyprázdnit datové sady Power BI, spusťte RealtimeDashboardApp s parametrem "flushdata". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Dávková analýza
Cílem je zobrazit, jak motory Contoso využívá možnosti Azure compute a využití velkých objemů dat. Tato data zobrazí podrobné informace o řízení vzory využití chování a stavu vozidel. Tyto informace díky tomu je možné:

* Vylepšení prostředí pro zákazníky a nastavte ji levnější tím, že poskytuje přehledy o jízdních návycích a zvýšení řízení chování.
* Seznamte se aktivně zákazníků a jejich řízení vzory řídí rozhodnutí o vašem podniku a poskytují ve své třídě nejlepší produkty a služby.

V tomto řešení jsou cílené následující metriky:

* **Agresivní řízení chování**: identifikuje trend modely, umístění, řízení podmínky a období roku získat přehledy o agresivní dodávala vzory. Contoso motory insights můžete použít tyto pro marketingové kampaně zavést nové individuální funkce a založená na využití pojištění.
* **Zvýšení řízení chování**: identifikuje trend modely, umístění, řízení podmínky a období roku k získání přehledu o zvýšení dodávala vzory. Motory contoso můžete použít tyto přehledy pro marketingové kampaně zavádět nové funkce a proaktivní odesílajících sestavy do ovladače pro nákladově efektivní a podporou prostředí dodávala návycích.
* **Odvolat modely**: identifikuje modely, které vyžadují navrácení ve zprovozňování anomálií detekce experimentu strojového učení.

Pojďme se podívat na podrobnosti o každé z těchto měřítek.

#### <a name="aggressive-driving-behavior-patterns"></a>**Agresivní dodávala vzorce chování**

Dělené vozidla signály a diagnostická data se zpracovávají v AggresiveDrivingPatternPipeline, jak je znázorněno v následujícím pracovním postupu. Hive se používá k určení modely, umístění, vozidlo, podmínky za jízdy a další parametry, které vykazují agresivní dodávala vzory.

![Agresivní řízení pracovního postupu vzor](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Agresivní dodávala vzor dotazu Hive***

Aggresivedriving.hql skript Hive slouží k analýze agresivní dodávala vzory podmínku. Je umístěn ve složce \demo\src\connectedcar\scripts stažený soubor zip. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



    INSERT OVERWRITE TABLE CarEventsAggresive
    select
    vin,
    model,
    timestamp,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'


Tento skript využívá kombinaci přenosu ozubeného kola pozice vozidla, brzdou Pedálové stav a rychlost ke zjištění reckless/agresivní řízení chování v závislosti na brzdění vzory vysokou rychlostí. 

Po úspěšném spuštění kanálu se zobrazí následující oddíly generované ve vašem účtu úložiště v rámci kontejneru connectedcar:

![AggressiveDrivingPatternPipeline výstupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Zvýšení dodávala vzorce chování**

Dělené vozidla signály a diagnostická data se zpracovávají v FuelEfficientDrivingPatternPipeline, jak je znázorněno v následujícím pracovním postupu. Hive se používá k určení modely, umístění, vozidlo, podmínky za jízdy a dalších vlastností, které vykazují zvýšení dodávala vzory.

![Zvýšení dodávala vzory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Zvýšení dodávala vzor dotazu Hive***

Fuelefficientdriving.hql skript Hive slouží k analýze zvýšení dodávala vzory podmínku. Je umístěn ve složce \demo\src\connectedcar\scripts stažený soubor zip. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



    INSERT OVERWRITE TABLE FuelEfficientDriving
    select
    vin,
    model,
    city,
    speed,
    transmission_gear_position,
    brake_pedal_status,
    accelerator_pedal_position,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from PartitionedCarEvents
    where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


Skript používá kombinaci vozidla přenosu ozubeného kola pozici, brzdou Pedálové stav, rychlost a akcelerátor Pedálové pozici k detekci zvýšení řízení chování v závislosti na akceleraci brzdění a urychlit vzory. 

Po úspěšném spuštění kanálu se zobrazí následující oddíly generované ve vašem účtu úložiště v rámci kontejneru connectedcar:

![FuelEfficientDrivingPatternPipeline výstupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Odvolat předpovědí modelu**

Machine learning experimentu je zřízený a publikovat jako webovou službu jako součást nasazení řešení. Dávkové bodování koncový bod se používá v tomto pracovním postupu. Má registrované jako datové továrny, propojené služby a zprovoznili jej pomocí data factory dávkového vyhodnocování aktivity.

![Koncový bod Machine learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Registrovanou propojenou službu se používá v DetectAnomalyPipeline pro vyhodnocení dat pomocí modelu detekce anomálií. 

![Machine learning aktivita dávkového bodování ve službě data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Pár kroků jsou prováděny v tomto kanálu pro přípravu dat tak, aby ho se mají zprovoznit s dávkového vyhodnocování webové služby. 

![DetectAnomalyPipeline pro odvolání predikcí](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Dotaz Hive detekce anomálií***

Po dokončení hodnocení, aktivita HDInsight procesy a agreguje data, která modelu zařazený do kategorie jako anomálie. Model využívá skóre pravděpodobnosti 0.60 nebo vyšší.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

    INSERT OVERWRITE TABLE RecallModel
    select
    vin,
    model,
    city,
    outsidetemperature,
    enginetemperature,
    speed,
    "${hiveconf:Year}" as Year,
    "${hiveconf:Month}" as Month
    from CarEventsAnomaly
    where RecallLabel = '1' AND RecallProbability >= '0.60'


Po úspěšném spuštění kanálu se zobrazí následující oddíly generované ve vašem účtu úložiště v rámci kontejneru connectedcar:

![DetectAnomalyPipeline výstupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publikování

### <a name="real-time-analysis"></a>Analýza v reálném čase
Některý z dotazů v úloze Stream Analytics publikuje do instance výstupní události centra událostí. 

![Publikování instancí centra událostí výstupu úlohy Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Následující dotaz Stream Analytics se používá k publikování do instancí centra událostí výstupu:

![Dotazu Stream Analytics k publikování do instancí centra událostí výstupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Tento datový proud událostí je využívána RealTimeDashboardApp, který je součástí řešení. Tato aplikace používá odpověď na požadavek webovou službu machine learning pro vyhodnocování v reálném čase. Publikuje Výsledná data do datové sady Power BI k použití. 

### <a name="batch-analysis"></a>Dávková analýza
Výsledky dávkových skriptů a zpracování v reálném čase se publikují do tabulky Azure SQL Database za využití. SQL server, databáze a tabulky se vytvoří automaticky jako součást instalačního skriptu. 

Výsledky zpracování služby batch se zkopírují do pracovního postupu datového tržiště.

![Zpracování výsledků zkopírují do datového tržiště pracovní postup služby batch](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Úlohy Stream Analytics se publikuje do datového tržiště.

![Úlohy Stream Analytics se publikují do datového tržiště](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Nastavení Tržiště dat je v úloze Stream Analytics.

![Datové Tržiště nastavení v úloze Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Konzumace
Power BI poskytuje tomuto řešení panel s bohatými funkcemi pro vizualizace prediktivních analýz a dat v reálném čase. 

Poslední řídicí panel bude vypadat jako v tomto příkladu:

![Řídicí panel Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Souhrn
Tento dokument obsahuje podrobné procházení podrobností řešení analýzy Telemetrie vozidla. Vzor architektury lambda se používá pro v reálném čase a dávkových analýz s využitím předpovědi a akce. Tento model se vztahuje na širokou škálu případů použití, které vyžadují kritická cesta (v reálném čase) a analýzy studené cesty (batch). 

### <a name="references"></a>Odkazy

* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Azure Event Hubs SDK účelem ingestování datových proudů](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Možnosti přesunu dat služby Azure Data Factory](../../data-factory/copy-activity-overview.md)
* [Aktivita služby Azure Data Factory .NET](../../data-factory/transform-data-using-dotnet-custom-activity.md)
* [Azure Data Factory .NET aktivity řešení sady Visual Studio používá k přípravě ukázková data](https://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="next-steps"></a>Další kroky

Další informace o konfiguraci sestavy Power BI a řídicí panely pro toto řešení, najdete v článku [pokyny k instalaci vozidla telemetrická data Analytics řešení šablony Power BI řídicí panel](cortana-analytics-playbook-vehicle-telemetry-powerbi.md).
