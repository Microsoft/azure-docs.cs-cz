---
title: Podrobné informace o tom, jak k předvídání vehicle stavu a řídí zvyklosti - Azure | Microsoft Docs
description: Pomocí možnosti Cortana Intelligence získat přehledy v reálném čase a prediktivní na vehicle stavu a řídí zvyklosti.
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
ms.openlocfilehash: a856c59191a1e35b78d1ab6c2c88ab5288ea1419
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836479"
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Vehicle řešení analýzy Telemetrie playbook: přímý ponořte do řešení
Této nabídky odkazy na části této playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Tento dokument podrobněji zanalyzuje data do každé z fází znázorněný v architektuře řešení. Pokyny a ukazatele pro přizpůsobení jsou zahrnuty. 

## <a name="data-sources"></a>Zdroje dat
Toto řešení využívá dva různé datové zdroje:

* Simulované vehicle signály a diagnostiky datové sady
* Vehicle katalogu

Vehicle telematika jsme je součástí tohoto řešení, jak je znázorněno na následujícím snímku obrazovky. Ho vysílá diagnostické informace a signály, které odpovídají stavu nástroj a podporovat jeho vzor k danému bodu v čase. Chcete-li stáhnout Vehicle telematika simulátoru Visual Studio řešení pro přizpůsobení podle požadavků, přejděte na [Vehicle telematika simulátoru](http://go.microsoft.com/fwlink/?LinkId=717075) webovou stránku. Katalogu vehicle obsahuje referenční datové sady, který se mapuje na modely vehicle identifikačními čísly (VINs).

![Vehicle telematika simulátoru](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Tato datová sada formátu JSON obsahuje následující schéma.

| Sloupec | Popis | Hodnoty |
| --- | --- | --- |
| VIN |Náhodně generované VIN |Získané z hlavní seznam 10 000 náhodně generované VINs |
| Mimo teploty |Mimo teploty, kde je nástroj Řízení |Náhodně generované číslo od 0 do 100 |
| Modul teploty |Modul teplota nástroj |Náhodně generované číslo od 0 do 500 |
| Rychlost |Modul rychlost, jakou se řídí nástroj |Náhodně generované číslo od 0 do 100 |
| Paliva |Úroveň paliva nástroj |Náhodně generované číslo od 0 do 100 (určuje procento úrovně paliva) |
| EngineOil |Úroveň modulu těžba ropy nástroj |Náhodně generované číslo od 0 do 100 (určuje procento úrovně těžba ropy modul) |
| Můžete zadat přetížení |Můžete zadat tlak nástroj |Náhodně generované číslo od 0 do 50 (určuje procento úrovně zatížení můžete zadat) |
| Počítadlo kilometrů |Čtení počítadlo kilometrů nástroj |Náhodně generované číslo od 0 do 200 000 |
| Accelerator_pedal_position |Nástroj Pedálové pozici akcelerátoru |Náhodně generované číslo od 0 do 100 (určuje procento úrovně akcelerátoru) |
| Parking_brake_status |Určuje, zda nástroj zaparkováno nebo ne |True nebo False |
| Headlamp_status |Určuje, zda je světlomet je na nebo ne |True nebo False |
| Brake_pedal_status |Určuje, zda je nebo není stisknuta brzdového pedálu |True nebo False |
| Transmission_gear_position |Pozice ozubené kolečko přenosu nástroj |Stavy: první, druhá, třetí a čtvrté, páté, šestého, sedmá, osmého |
| Ignition_status |Určuje, zda je spuštěná nebo zastavená nástroj |True nebo False |
| Windshield_wiper_status |Určuje, zda stírání čelního skla je zapnutá nebo vypnutá |True nebo False |
| ABS |Určuje, zda je nebo není zařazen ABS |True nebo False |
| Časové razítko |Časové razítko vytvoření datového bodu |Datum |
| Město |Umístění nástroj |Čtyři města v tomto řešení: Bellevue, Redmond, Sammamish, Praha |

Vehicle modelu odkaz na datovou sadu mapuje VINs modelů. 

| VIN | Model |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Limuzíny |
| 8J0U8XCPRGW4Z3NQE |Hybridní |
| WORG68Z2PLTNZDBI7 |Rodiny Sedan |
| JTHMYHQTEPP4WBMRN |Limuzíny |
| W9FTHG27LZN1YWO0Y |Hybridní |
| MHTP9N792PHK08WJM |Rodiny Sedan |
| EI4QXI2AXVQQING4I |Limuzíny |
| 5KKR2VB4WHQH97PF8 |Hybridní |
| W9NSZ423XZHAONYXB |Rodiny Sedan |
| 26WJSGHX4MA5ROHNL |Převoditelné |
| GHLUB6ONKMOSI7E77 |Kombi |
| 9C2RHVRVLMEJDBXLP |Compact car |
| BRNHVMZOUJ6EOCP32 |Malé SUV |
| VCYVW0WUZNBTM594J |Auto sportu |
| HNVCE6YFZSA5M82NY |Střední SUV |
| 4R30FOR7NUOBL05GJ |Kombi |
| WYNIIY42VKV6OQS1J |Velké SUV |
| 8Y5QKG27QET1RBK7I |Velké SUV |
| DF6OX2WSRA6511BVG |Kupé |
| Z2EOZWZBXAEW3E60T |Limuzíny |
| M4TV6IEALD5QDS3IR |Hybridní |
| VHRA1Y2TGTA84F00H |Rodiny Sedan |
| R0JAUHT1L1R3BIKI0 |Limuzíny |
| 9230C202Z60XX84AU |Hybridní |
| T8DNDN5UDCWL7M72H |Rodiny Sedan |
| 4WPYRUZII5YV7YA42 |Limuzíny |
| D1ZVY26UV2BFGHZNO |Hybridní |
| XUF99EW9OIQOMV7Q7 |Rodiny Sedan |
| 8OMCL3LGI7XNCC21U |Převoditelné |
| ……. | |

## <a name="ingestion"></a>Přijímání
Kombinace Azure Event Hubs, Azure Stream Analytics a Azure Data Factory slouží k ingestování signály vehicle, diagnostických událostí a v reálném čase a analýza služby batch. Všechny tyto součásti vytvoříte a nakonfigurujete jako součást nasazení řešení. 

### <a name="real-time-analysis"></a>Analýzy v reálném čase
Události vygenerované pomocí simulátoru telematika vehicle jsou publikovány do centra událostí pomocí centra událostí SDK.  

![Řídicí panel centra událostí](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Úloha Stream Analytics ingestuje tyto události z centra událostí a zpracovává data v reálném čase analyzovat vehicle stavu.

![Stream Analytics úlohy zpracování dat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Úloha Stream Analytics:

* Ingestuje dat z centra událostí.
* Spojí u referenčních dat pro mapování vehicle VIN odpovídající modelu. 
* Dál je do Azure Blob storage analytics bohaté batch. 

Následující dotaz služby Stream Analytics se používá k uchování dat do úložiště objektů Blob: 

![Dotaz úlohy Stream Analytics přijímání dat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Dávková analýza
Další svazek simulované vehicle signály a diagnostiky datové sady je vytvořena také pro širší batch analýzu. Tento další svazek je nutný k zajištění funkční reprezentativní datový svazek pro dávkové zpracování. K tomuto účelu slouží PrepareSampleDataPipeline v pracovním postupu Data Factory ke generování za jeden rok simulované vehicle signály a diagnostiky datové sady. Objekt pro vytváření dat vlastní .NET aktivita řešení sady Visual Studio pro přizpůsobení podle požadavků na stažení, přejděte na [vlastní aktivity služby Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) webovou stránku. 

Tento pracovní postup nastiňuje ukázkových dat připravené pro dávkové zpracování.

![Ukázková data připravené pro pracovní postup služby batch zpracování](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


Kanál se skládá z vlastních aktivit Data Factory .NET.

![PrepareSampleDataPipeline aktivity](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Po kanálu provede úspěšně a RawCarEventsTable datové sady je označen jako "Připravena", za jeden rok simulované vehicle signály a diagnostických dat vytváří. Zobrazí následující složku a soubor vytvořený v účtu úložiště v kontejneru connectedcar:

![Výstup PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Oddíl datové sady
V kroku přípravu dat nezpracovaná částečně strukturovaných vehicle signály a diagnostiky datové sady oddíly do formátu měsíc roku. Toto rozdělení do oddílů podporuje efektivnější dotazování a škálovatelné dlouhodobé úložiště povolíte odolnost převzetí. Například jako první účet blob zaplní, ho závady přes k další účtu. 

>[!NOTE] 
>Tento krok v řešení platí pouze pro dávkové zpracování.

Vstupní a výstupní data správy:

* **Výstupní data** (s popiskem PartitionedCarEventsTable) je udržováno na dlouhou dobu jako základní / "rawest" formu dat v data lake zákazníka. 
* **Vstupní data** pro tento kanál se zahodí, obvykle protože výstupních dat má úplné věrnosti na vstup. Je uložený (oddíly) lépe pro pozdější použití.

Oddíl car události pracovního postupu.

![Pracovní postup události car oddílu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


Nezpracovaná data se rozdělil pomocí aktivity Hive Azure HDInsight v PartitionCarEventsPipeline, jak je znázorněno na následujícím snímku obrazovky. Ukázková data vygenerované v roce v rámci přípravy dat je rozdělena na oddíly pomocí měsíc roku. Oddíly, které se používají ke generování vehicle signály a diagnostických dat pro každý měsíc (celkem 12 oddílů) v roce. 

![PartitionCarEventsPipeline aktivity](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Skript PartitionConnectedCarEvents Hive**

Partitioncarevents.hql skriptu Hive se používá pro vytváření oddílů. Je umístěn ve složce \demo\src\connectedcar\scripts souboru zip staženého. 
    
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

Po kanálu provede úspěšně, zobrazí se následující oddíly generované ve vašem účtu úložiště v kontejneru connectedcar:

![Oddílů výstup](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

Data je nyní optimalizovaná, lepší správu bitlockeru a připravený pro další zpracování a získáte přehled o bohaté batch. 

## <a name="data-analysis"></a>Analýza dat
V této části najdete postup kombinace Stream Analytics, Azure Machine Learning, Data Factory a HDInsight pro bohaté pokročilé analýzy na vehicle stavu a řídí zvyklosti.

### <a name="machine-learning"></a>Strojové učení
Cílem je k předvídání vozidel, které vyžadují údržby nebo odvolání na základě určité statistik stavu podle následující předpoklady:

* Pokud platí jedna z následujících tří podmínek, vozidel vyžadovat obsluhy údržby:
  
  * Můžete zadat naléhavost je nízký.
  * Úroveň modulu těžba ropy je nízký.
  * Modul teploty je vysoké.

* Pokud platí jedna z následujících podmínek, vozidel může mít problém zabezpečení a vyžadovat odvolání:
  
  * Modul teploty je vysoká, ale mimo teploty je nízký.
  * Modul teploty je nízký, ale mimo teploty je vysoká.

V závislosti na požadavcích předchozí dva samostatné modely zjišťovat anomálie. Jeden model je pro zjišťování vehicle údržby a jeden model je pro zjišťování pro vyvolání vehicle. V obou modelech se používá algoritmus analysis (PCA) předdefinované hlavní součásti pro zjišťování anomálií. 

#### <a name="maintenance-detection-model"></a>**Model pro odhalování údržby**

Pokud tři ukazatelů – můžete zadat naléhavost, těžba ropy modul nebo modul teploty – splňuje jeho odpovídající stav, model pro odhalování údržby sestavy anomálií. V důsledku toho pouze tyto tři proměnné muset být zvážit při vytváření modelu. V rámci experimentu v machine learning **výběr sloupců v datové sadě** modul se používá k extrakci těchto tří proměnných. V dalším kroku modulu detekce anomálií založený na PCA slouží k vytvoření modelu detekce anomálií. 

PCA je zavedených technika v machine learning, který lze použít k výběru funkcí, klasifikace a detekce anomálií. PCA převede sadu případů, které obsahují pravděpodobně korelační proměnné do sady hodnot nazývané jako hlavní komponenty. Klíče představu o na základě PCA modelování je projekt dat na nižší dimenzí místa pro snazší identifikaci funkce a anomálií.

Pro každý nový vstup model pro odhalování detekce anomálií nejprve vypočítá jeho projekce na eigenvectors. Pak vypočítá chyba normalizovaný obnovu. Tato chyba normalizovaný je skóre anomálií: Čím chyby, více neobvyklé instance. 

V údržby zjišťování problému je každý záznam považuje za bod v trojrozměrné prostoru definované zatížení můžete zadat, modul těžba ropy a teploty modul souřadnice. Když Pokud chcete zachytit tyto anomálií, PCA slouží do projektu původní data v trojrozměrné prostoru do dvourozměrná prostoru. Proto parametr počet komponenty pro použití v PCA nastavena na dva. Tento parametr hraje důležitou roli při uplatňování detekce anomálií založený na PCA. Po použití PCA k datům projektu, se označují snadněji tyto anomálií.

#### <a name="recall-anomaly-detection-model"></a>**Odvolat modelu detekce anomálií**

V modelu detekce anomálií odvolání **výběr sloupců v datové sadě** a moduly detekce anomálií založený na PCA používají podobným způsobem. Konkrétně tří proměnných – modul teploty, mimo teploty a rychlost – extrahují nejprve pomocí **výběr sloupců v datové sadě** modulu. Proměnnou rychlost je také součástí, protože modul teploty obvykle koreluje s rychlostí. Modul pro zjišťování anomálií založený na PCA se dále používá k projektu data z trojrozměrné prostoru do dvourozměrná prostoru. Jsou splněna kritéria pro vyvolání. Při vysoce negativně korelační modul teploty a mimo teploty, vyžaduje nástroj odvolání. Po provedení PCA algoritmus detekce anomálií založený na PCA se používá k zachycení anomálií. 

Při tréninku buď modelu, normální data se používají jako vstupní data pro trénování modelu detekce anomálií založený na PCA. (Normální datové nevyžaduje, údržby nebo odvolání.) V rámci vyhodnocování experimentu modelu detekce anomálií vyškolení slouží ke zjištění, zda nástroj vyžaduje údržby nebo odvolání. 

### <a name="real-time-analysis"></a>Analýzy v reálném čase
Následující dotaz Stream Analytics SQL se používá k získání průměr všech parametrů důležité vehicle. Tyto parametry zahrnují rychlosti, úroveň paliva, modul teploty, počítadlo kilometrů čtení, můžete zadat naléhavost, modul těžba ropy úroveň a dalších. Průměry se používají k zjišťovat anomálie, vydávání výstrah a určení celkového stavu podmínky vozidel provozovat v určité oblasti. Průměry jsou pak vztažen k demografické údaje. 

![Stream Analytics query pro zpracování v reálném čase](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Všechny průměry jsou vypočítávány přes tři sekundu přeskakující okno. Přeskakující okno je použít, protože jsou požadovány nepřekrývají a souvislý časové intervaly. 

Další informace o možnostech oddílová v Stream Analytics najdete v tématu [Oddílová (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

#### <a name="real-time-prediction"></a>**V reálném čase předpovědi**

Aplikace je součástí řešení, aby zprovoznit model machine learning v reálném čase. Aplikace RealTimeDashboardApp je vytvořen a nakonfigurován jako součást nasazení řešení. Aplikace:

* Naslouchá na instanci Centra událostí, kde Stream Analytics publikuje události ve tvaru nepřetržitě.

    ![Stream Analytics dotazu pro publikování dat](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Přijímá události. Pro každou událost, která obdrží tuto aplikaci: 
   
   * Zpracovává data pomocí strojového učení (záznamy RR) endpoint vyhodnocování požadavků a odpovědí. Záznamy o prostředku koncového bodu je automaticky publikován jako součást nasazení.
   * Výstup RRS je publikovaný na datové sady Power BI pomocí nabízené instalace rozhraní API.

Tento vzor platí i pro scénáře, ve kterých chcete integrovat-obchodní aplikace s tokem analýzu v reálném čase. Mezi tyto scénáře patří výstrahy, oznámení a zasílání zpráv.

Si můžete stáhnout řešení nástroje RealtimeDashboardApp Visual Studio přizpůsobení [RealtimeDashboardApp stažení](http://go.microsoft.com/fwlink/?LinkId=717078) webovou stránku. 

#### <a name="execute-the-real-time-dashboard-application"></a>**Spuštění aplikace v reálném čase řídicí panel**
1. Rozbalte RealtimeDashboardApp a uložit místně.

    ![RealTimeDashboardApp složky](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Spusťte aplikaci RealtimeDashboardApp.exe.

3. Zadejte platné přihlašovací údaje Power BI a vyberte **přihlášení**.  

    ![Okna přihlášení aplikace v reálném čase řídicí panel](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Vyberte **přijmout**.

    ![Řídicí panel v reálném čase okna aplikace na posledním přihlášení](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Pokud chcete vyprázdnit datové sady Power BI, spouštění RealtimeDashboardApp s parametrem "flushdata". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Dávková analýza
Cílem je zobrazit, jak motory Contoso využívá možnosti výpočtů Azure pro svůj velkých objemů dat. Tato data odhalí detailní přehled na provoz, využití chování a stavů vehicle. Tyto informace vám umožní:

* Zlepšovat prostředí zákazníků a nastavte jej levnější tím, že poskytuje přehled na řídí zvyklosti a zvýšení řízení chování.
* Seznamte se aktivně zákazníků a jejich řízení vzory a řídit obchodní rozhodnutí poskytující nejvhodnější třídy produktů a služeb.

V tomto řešení jsou cíleny následující metriky:

* **Agresivní řídí chování**: identifikuje trend modely, umístění, podporovat jeho podmínky a čas roku a získáte přehled o agresivní řízení vzory. Contoso motory pro můžete použít tyto přehledy marketingových kampaní zavádět nové funkce přizpůsobené a pojišťovnictví na základě využití.
* **Zvýšení řízení chování**: identifikuje trend modely, umístění, podporovat jeho podmínky a čas roku a získáte přehled o zvýšení řízení vzory. Contoso motory můžete použít tyto přehledy pro marketingové kampaně zavádět nové funkce a proaktivní vytváření sestav na ovladače pro finančně efektivní a podporou prostředí podporovat jeho návyky.
* **Odvolat modely**: identifikuje modely, které vyžadují navrácení s zprovozňování detekce anomálií strojového učení experimentu.

Podívejme se na podrobné informace o jednotlivých tyto metriky.

#### <a name="aggressive-driving-behavior-patterns"></a>**Vzory agresivní řízení chování**

Signály oddílů vehicle a diagnostických dat jsou zpracovány v AggresiveDrivingPatternPipeline, jak je znázorněno v následujícím pracovním postupu. Hive slouží k určení modely, umístění, vehicle, jízdních podmínek a další parametry, které vykazují agresivní podporovat jeho vzorce.

![Agresivní řízení pracovního postupu vzor](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Agresivní řízení dotaz Hive vzor***

Aggresivedriving.hql skriptu Hive se používá k analýze agresivní řízení vzory podmínku. Je umístěn ve složce \demo\src\connectedcar\scripts souboru zip staženého. 

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


Skript používá kombinaci přenosu ozubené kolečko pozice vehicle, brzdy Pedálové stavu a rychlost ke zjištění reckless/agresivní řízení chování v závislosti na brzdění vzory vysokou rychlostí. 

Po kanálu provede úspěšně, zobrazí se následující oddíly generované ve vašem účtu úložiště v kontejneru connectedcar:

![Výstup AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Zvýšení řízení chování vzory**

Signály oddílů vehicle a diagnostických dat jsou zpracovány v FuelEfficientDrivingPatternPipeline, jak je znázorněno v následujícím pracovním postupu. Hive slouží k určení modely, umístění, vehicle, jízdních podmínek a další vlastnosti, které vykazují zvýšení podporovat jeho vzorce.

![Zvýšení řízení vzory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Zvýšení řízení dotaz Hive vzor***

Fuelefficientdriving.hql skriptu Hive se používá k analýze zvýšení řízení vzory podmínku. Je umístěn ve složce \demo\src\connectedcar\scripts souboru zip staženého. 

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


Tento skript využívá kombinace vehicle přenosu ozubené kolečko pozice, brzdy Pedálové stav, rychlost a akcelerátoru Pedálové pozice k detekci zvýšení řízení chování v závislosti na akcelerace, brzdění a rychlost vzory. 

Po kanálu provede úspěšně, zobrazí se následující oddíly generované ve vašem účtu úložiště v kontejneru connectedcar:

![Výstup FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Odvolat předpovědi modelu**

Strojového učení experimentu je zřízený a publikovat jako webovou službu, jako součást nasazení řešení. Dávkového vyhodnocování koncový bod se používá v tomto pracovním postupu. Má zaregistrován jako služba data factory propojené a operationalized pomocí objektu pro vytváření dat dávkového vyhodnocování aktivity.

![Koncový bod Machine learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Registrovanou propojenou službu se používá v DetectAnomalyPipeline skóre data pomocí modelu detekce anomálií. 

![Machine learning aktivita dávkového vyhodnocování v datové továrně](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Několik kroky se provádějí v kanálu pro přípravu dat tak, aby může být operationalized s dávkového vyhodnocování webové služby. 

![DetectAnomalyPipeline pro předpověď odvolání](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Dotaz Hive detekce anomálií***

Po dokončení vyhodnocování aktivitu HDInsight procesy a agreguje data, která modelu zařazen do kategorie anomálií. Model používá pravděpodobnosti skóre 0.60 nebo vyšší.

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


Po kanálu provede úspěšně, zobrazí se následující oddíly generované ve vašem účtu úložiště v kontejneru connectedcar:

![Výstup DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Publikování

### <a name="real-time-analysis"></a>Analýzy v reálném čase
Některý z těchto dotazů v úloze Stream Analytics publikuje události na instanci výstupní událost rozbočovače. 

![Publikovat do instance centra událostí výstup úlohy Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

Následující dotaz služby Stream Analytics se používá k publikování do instance centra událostí výstup:

![Stream Analytics dotazu pro publikování do instance výstup události rozbočovače](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Tento datový proud událostí, které se spotřebovávají RealTimeDashboardApp, který je součástí řešení. Tato aplikace používá strojového učení požadavků a odpovědí webové služby pro vyhodnocování v reálném čase. Tato možnost publikuje Výsledná data do Power BI datové sady pro používání. 

### <a name="batch-analysis"></a>Dávková analýza
Výsledky batch a v reálném čase zpracování se publikují do tabulky Azure SQL Database pro používání. SQL server, databáze a tabulky se vytvoří automaticky v rámci instalační skript. 

Výsledky zpracování dávky se zkopírují do pracovního postupu datového tržiště.

![Dávkové zpracování výsledků zkopírován do datového tržiště pracovního postupu](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Úloha Stream Analytics je publikovat do datového tržiště.

![Publikovat do datového tržiště úlohy Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Nastavení Tržiště dat je v úloze Stream Analytics.

![Datové Tržiště nastavení v úloze Stream Analytics](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Konzumace
Power BI poskytuje toto řešení bohaté řídicí panel pro data v reálném čase a vizualizací prediktivní analýzy. 

Poslední řídicí panel vypadá v tomto příkladu:

![Řídicí panel Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Souhrn
Tento dokument obsahuje podrobné procházení řešení Analytics Vehicle Telemetrie. Vzor architektura lambda se používá pro v reálném čase a dávky analytics s předpovědi a akce. Tento vzor platí pro širokou škálu případy použití, které vyžadují aktivní cesta (v reálném čase) a analýzy neaktivní trase (batch). 

### <a name="references"></a>Odkazy

* [Řešení vehicle telematika simulátoru sady Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)
* [Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [Azure SDK centra událostí pro přijímání datového proudu](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Možnosti přesun dat Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Aktivita služby Azure Data Factory .NET](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Aktivita Azure Data Factory .NET řešení sady Visual Studio lze připravit ukázková data](http://go.microsoft.com/fwlink/?LinkId=717077) 
