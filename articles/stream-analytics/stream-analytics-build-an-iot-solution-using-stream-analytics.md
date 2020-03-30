---
title: Vytvoření řešení IoT pomocí Azure Stream Analytics
description: Začínáme pro řešení Stream Analytics IoT scénáře mýtného
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426446"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Vytvoření řešení IoT pomocí Stream Analytics

## <a name="introduction"></a>Úvod
V tomto řešení se dozvíte, jak pomocí Azure Stream Analytics získat přehledy z vašich dat v reálném čase. Vývojáři mohou snadno kombinovat datové proudy dat, jako jsou toky kliknutí, protokoly a události generované zařízením, s historickými záznamy nebo referenčními daty pro odvození obchodních přehledů. Azure Stream Analytics jako plně spravovaná výpočetní služba datových proudů v reálném čase, která je hostovaná v Microsoft Azure, poskytuje integrovanou odolnost proti chybám, nízkou latenci a škálovatelnost, abyste se během několika minut zprovoznili.

Po dokončení tohoto řešení, jste schopni:

* Seznamte se s portálem Azure Stream Analytics.
* Konfigurace a nasazení úlohy streamování.
* Artikulujte problémy z reálného světa a vyřešte je pomocí dotazovacího jazyka Stream Analytics.
* Vyvíjejte streamovací řešení pro své zákazníky pomocí Stream Analytics s jistotou.
* Pomocí monitorování a protokolování zkušenosti k řešení problémů.

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto řešení potřebujete následující předpoklady:
* [Předplatné Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scénář úvod: "Dobrý den, Mýtné!"
Mýtná stanice je běžný jev. Setkáte se s nimi na mnoha rychlostních cestách, mostech a tunelech po celém světě. Každá mýtná stanice má několik mýtných stánků. U manuálních kabin se zastavíte, abyste zaplatili mýtné obsluhě. Na automatických stáncích senzor na vrcholu každé kabiny skenuje RFID kartu, která je připevněna k čelnímu sklu vašeho vozidla, když procházíte mýtnou budkou. Je snadné vizualizovat průjezd vozidel přes tyto mýtné stanice jako událost proud, přes který lze provádět zajímavé operace.

![Obrázek aut na mýtných stáncích](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Příchozí data
Toto řešení pracuje se dvěma datovými proudy dat. Senzory instalované ve vchodu a výstupu z mýtných stanic produkují první proud. Druhý datový proud je datová sada statického vyhledávání, která má údaje o registraci vozidla.

### <a name="entry-data-stream"></a>Vstupní datový proud
Vstupní datový proud obsahuje informace o automobilech při vstupu do mýtných stanic. Události výstupních dat se živě streamují do fronty Centra událostí z webové aplikace zahrnuté v ukázkové aplikaci.

| TollID | EntryTime | Registrační značka | Stav | Značka | Model | Typ vozidla | Hmotnost vozidla | Mýtné | Značka |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |Crv |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |Crv |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Zde je stručný popis sloupců:

| Sloupec | Popis |
| --- | --- |
| TollID |ID mýtného stánku, které jednoznačně identifikuje mýtnou budku |
| EntryTime |Datum a čas vjezdu vozidla do mýtného stánku v UTC |
| Registrační značka |Poznávací značka vozidla |
| Stav |Stát v Usa |
| Značka |Výrobce automobilového |
| Model |Číslo modelu automobilu |
| Typ vozidla |Buď 1 pro osobní vozidla, nebo 2 pro užitková vozidla |
| WeightType |Hmotnost vozidla v tunách; 0 pro osobní vozidla |
| Mýtné |Hodnota mýtného v USD |
| Značka |E-Tag na automobilu, který automatizuje platby; prázdné místo, kde byla platba provedena ručně |

### <a name="exit-data-stream"></a>Ukončení datového proudu
Výstupní datový tok obsahuje informace o automobilech opouštějících mýtnou stanici. Události výstupních dat se živě streamují do fronty Centra událostí z webové aplikace zahrnuté v ukázkové aplikaci.

| **Id mýtného** | **Doba ukončení** | **Registrační značka** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000z |CDE 1007 |

Zde je stručný popis sloupců:

| Sloupec | Popis |
| --- | --- |
| TollID |ID mýtného stánku, které jednoznačně identifikuje mýtnou budku |
| Doba ukončení |Datum a čas výstupu vozidla z mýtného boxu v UTC |
| Registrační značka |Poznávací značka vozidla |

### <a name="commercial-vehicle-registration-data"></a>Údaje o registraci užitkových vozidel
Řešení využívá statický snímek databáze registrace užitkových vozidel. Tato data se uloží jako soubor JSON do úložiště objektů blob Azure, které je součástí ukázky.

| Registrační značka | Id registrace | Platnost vypršela |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Zde je stručný popis sloupců:

| Sloupec | Popis |
| --- | --- |
| Registrační značka |Poznávací značka vozidla |
| Id registrace |ID registrace vozidla |
| Platnost vypršela |Stav registrace vozidla: 0, pokud je registrace vozidla aktivní, 1, pokud vypršela platnost registrace |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Nastavení prostředí pro Azure Stream Analytics
K dokončení tohoto řešení potřebujete předplatné Microsoft Azure. Pokud nemáte účet Azure, můžete [požádat o bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

Nezapomeňte postupovat podle kroků v části "Vyčistit účet Azure" na konci tohoto článku, abyste mohli co nejlépe využít svůj kredit Azure.

## <a name="deploy-the-sample"></a>Nasazení ukázky
Existuje několik prostředků, které lze snadno nasadit ve skupině prostředků spolu s několika kliknutími. Definice řešení je hostována v [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp)úložišti GitHub na adrese .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Nasazení šablony TollApp na webu Azure Portal
1. Chcete-li nasadit prostředí TollApp do Azure, použijte tento odkaz k [nasazení šablony TollApp Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Pokud se zobrazí výzva, přihlaste se na portál Azure.

3. Zvolte předplatné, ve kterém se účtují různé prostředky.

4. Zadejte novou skupinu prostředků s jedinečným názvem, například `MyTollBooth`.

5. Vyberte umístění Azure.

6. Zadejte **interval** jako počet sekund. Tato hodnota se používá v ukázkové webové aplikaci, jak často odesílat data do Centra událostí.

7. **Zkontrolujte, zda** souhlasíte s podmínkami.

8. Vyberte **Připnout na řídicí panel,** abyste později mohli prostředky snadno najít.

9. Vyberte **Koupit,** chcete-li nasadit ukázkovou šablonu.

10. Po několika okamžicích se zobrazí oznámení potvrzující, že **nasazení proběhlo úspěšně**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Kontrola prostředků Azure Stream Analytics TollApp

1. Přihlášení k webu Azure Portal

2. Vyhledejte skupinu prostředků, kterou jste pojmenovali v předchozí části.

3. Ověřte, zda jsou ve skupině prostředků uvedeny následující prostředky:
   - Jeden účet Cosmos DB
   - Jedna úloha Azure Stream Analytics
   - Jeden účet úložiště Azure
   - Jedno Centrum událostí Azure
   - Dvě webové aplikace

## <a name="examine-the-sample-tollapp-job"></a>Prohlédněte si ukázkovou úlohu TollApp
1. Počínaje skupinou prostředků v předchozí části vyberte úlohu streamování Stream Analytics začínající názvem **tollapp** (název obsahuje náhodné znaky pro jedinečnost).

2. Na stránce **Přehled** úlohy si všimněte pole **Dotaz,** kde se zobrazí syntaxe dotazu.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Chcete-li parafrázovat záměr dotazu, řekněme, že je třeba počítat počet vozidel, která vstupují do mýtného stánku. Vzhledem k tomu, dálnice mýtného stánku má nepřetržitý proud vozidel vstupu, to jsou vstupní události jsou obdobou proudu, který nikdy nezastaví. Chcete-li kvantifikovat datový proud, musíte definovat "časové období" pro měření. Pojďme upřesnit otázku dále, na "Kolik vozidel vjezd mýtného stánku každé tři minuty?" To se běžně označuje jako počet omílání.

   Jak můžete vidět, Azure Stream Analytics používá dotazovací jazyk, který se podobá SQL a přidá několik rozšíření k určení časově související aspekty dotazu.  Další podrobnosti načtete o [konstrukcích Time Management](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) a [Windowing](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) použitých v dotazu.

3. Prozkoumejte vstupy ukázkové úlohy TollApp. V aktuálním dotazu se používá pouze vstup EntryStream.
   - **EntryStream** vstup je připojení Centra událostí, které fronty data představující pokaždé, když auto vstoupí do mýtného budky na dálnici. Webová aplikace, která je součástí ukázky, vytváří události a tato data jsou zařazena do fronty v tomto centru událostí. Všimněte si, že tento vstup je dotazován v FROM klauzule streamování dotazu.
   - **ExitStream** vstup je připojení Centra událostí, které fronty data představující pokaždé, když auto opustí mýtnou budku na dálnici. Tento vstup streamování se používá v pozdějších variantách syntaxe dotazu.
   - **Vstup registrace** je připojení úložiště objektů Blob Azure, odkazující na statický soubor registration.json, který se používá pro vyhledávání podle potřeby. Tento vstup referenčních dat se používá v pozdějších variantách syntaxe dotazu.

4. Prozkoumejte výstupy ukázkové úlohy TollApp.
   - **Výstup Cosmos DB** je kontejner databáze Cosmos, který přijímá události výstupního jímky. Všimněte si, že tento výstup se používá v INTO klauzule streamování dotazu.

## <a name="start-the-tollapp-streaming-job"></a>Spuštění úlohy streamování Aplikace TollApp
Chcete-li spustit úlohu streamování, postupujte takto:

1. Na stránce **Přehled** úlohy vyberte **Start**.

2. V podokně **úloh Start** vyberte **Nyní**.

3. Po několika okamžicích, jakmile je úloha spuštěna, na stránce **Přehled** úlohy streamování, zobrazte graf **monitorování.** Graf by měl zobrazit několik tisíc vstupních událostí a desítky výstupních událostí.

## <a name="review-the-cosmosdb-output-data"></a>Kontrola výstupních dat CosmosDB
1. Vyhledejte skupinu prostředků, která obsahuje prostředky TollApp.

2. Vyberte účet Azure Cosmos DB s názvem vzor **tollapp\<random\>-cosmos**.

3. Výběrem záhlaví **Průzkumníka dat** otevřete stránku Průzkumník dat.

4. Rozbalte **tollAppDatabase** > **tollAppCollection** > **Documents**.

5. V seznamu IDs, několik dokumentů jsou zobrazeny, jakmile je k dispozici výstup.

6. Vyberte každé id, abyste zkontrolovali dokument JSON. Všimněte si každého tollid, windowend čas a počet vozů z tohoto okna.

7. Po dalších třech minutách je k dispozici další sada čtyř dokumentů, jeden dokument na tollid.


## <a name="report-total-time-for-each-car"></a>Nahlásit celkový čas pro každé auto
Průměrná doba potřebná k tomu, aby auto prošlo mýtným, pomáhá posoudit efektivitu procesu a zákaznickou zkušenost.

Chcete-li najít celkový čas, připojte entrytime datový proud s ExitTime datového proudu. Připojte dva vstupní datové proudy na stejné odpovídající TollId a LicensePlate sloupce. Operátor **JOIN** vyžaduje zadání časové prosazení, které popisuje přijatelný časový rozdíl mezi spojenými událostmi. Pomocí funkce **DATEDIFF** určete, že události by neměly být od sebe delší než 15 minut. Také použít **funkci DATEDIFF** pro ukončení a vstup časy vypočítat skutečný čas, který auto stráví v mýtné stanici. Všimněte si rozdílu použití **DATEDIFF** při použití v **příkazu SELECT** spíše než **podmínka JOIN.**

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aktualizace syntaxe dotazu na úlohu streamování aplikace TollApp:

1. Na stránce **Přehled** úlohy vyberte **Zastavit**.

2. Počkejte několik okamžiků na oznámení, že úloha byla zastavena.

3. Pod nadpisem TOPOLOGIE ÚLOHY vyberte **< > dotaz.**

4. Vložte upravený dotaz SQL streamování.

5. Chcete-li dotaz uložit, vyberte **uložit.** Potvrďte **ano,** chcete-li změny uložit.

6. Na stránce **Přehled** úlohy vyberte **Start**.

7. V podokně **úloh Start** vyberte **Nyní**.

### <a name="review-the-total-time-in-the-output"></a>Kontrola celkového času ve výstupu
Opakujte kroky v předchozí části a zkontrolujte výstupní data CosmosDB z úlohy streamování. Prohlédněte si nejnovější dokumenty JSON.

Tento dokument například zobrazuje ukázkové auto s určitou registrační značkou, čas emitování a čas ukončení a pole DATEDIFF vypočítané doby trvání v minutách zobrazující dobu trvání mýtného budky jako dvě minuty:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Nahlásit vozidla s prošlou registrací
Azure Stream Analytics můžete použít statické snímky referenčních dat pro připojení s časové datové proudy. Chcete-li tuto schopnost prokázat, použijte následující ukázkovou otázku. Vstup registrace je statický soubor json objektu blob, který obsahuje seznam vypršení platnosti licenčních značek. Spojením na poznávací značku se referenční údaje porovnávají s každým vozidlem, které projíždí mýtným.

Je-li užitkové vozidlo registrováno u mýtné společnosti, může projít mýtnou kabinou, aniž by bylo zastaveno pro kontrolu. Pomocí vyhledávací tabulky registrace můžete identifikovat všechna užitková vozidla, jejichž platnost vypršela.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Opakováním kroků v předchozí části aktualizujte syntaxi pracovního dotazu streamování Aplikace TollApp.

2. Opakujte kroky v předchozí části a zkontrolujte výstupní data CosmosDB z úlohy streamování.

Příklad výstupu:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Horizontální navýšení kapacity
Azure Stream Analytics je navržen tak, aby elasticky škálovat tak, aby mohl zpracovávat velké objemy dat. Dotaz Azure Stream Analytics můžete použít **klauzuli PARTITION BY** sdělit systému, že tento krok se škáluje. **PartitionId** je speciální sloupec, který systém přidá tak, aby odpovídaly ID oddílu vstupu (centrum událostí).

Chcete-li škálovat dotaz na oddíly, upravte syntaxi dotazu na následující kód:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Chcete-li vertikálně rozšířit úlohu streamování na více jednotek streamování:

1. **Zastavte** aktuální úlohu.

2. Aktualizujte syntaxi dotazu na stránce **< > dotaz** a uložte změny.

3. V části Konfigurace v rámci úlohy streamování vyberte **měřítko**.

4. Posuňte posuvník **Jednotek streamování** z 1 na 6. Jednotky streamování definují množství výpočetního výkonu, které může úloha přijímat. Vyberte **Uložit**.

5. **Spusťte** úlohu streamování k předvedení dalšího škálování. Azure Stream Analytics distribuuje práci napříč více výpočetními prostředky a dosáhnout lepší propustnost, rozdělení práce mezi prostředky pomocí sloupce určeného v klauzuli PARTITION BY.

## <a name="monitor-the-job"></a>Monitorování úlohy
Oblast **MONITOR** obsahuje statistické údaje o spuštěné úloze. První konfigurace je potřeba použít účet úložiště ve stejné oblasti (název mýtného jako zbytek tohoto dokumentu).

![Monitorování úloh Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

K **protokolům aktivit** můžete přistupovat také z oblasti **Nastavení** řídicího panelu úlohy.

## <a name="clean-up-the-tollapp-resources"></a>Vyčištění prostředků Společnosti TollApp
1. Zastavte úlohu Stream Analytics na webu Azure Portal.

2. Vyhledejte skupinu prostředků, která obsahuje osm prostředků souvisejících se šablonou TollApp.

3. Vyberte **Odstranit skupinu prostředků**. Zadejte název skupiny prostředků pro potvrzení odstranění.

## <a name="conclusion"></a>Závěr
Toto řešení vás seznámilo se službou Azure Stream Analytics. To ukazuje, jak nakonfigurovat vstupy a výstupy pro úlohu Stream Analytics. Pomocí scénáře Mýtná data řešení vysvětluje běžné typy problémů, které vznikají v prostoru dat v pohybu a jak je lze vyřešit pomocí jednoduchých dotazů podobných SQL v Azure Stream Analytics. Řešení popsalo konstrukce rozšíření SQL pro práci s temporálními daty. Ukázalo se, jak připojit datové proudy, jak obohatit datový proud se statickými referenčními daty a jak horizontální navýšení kapacity dotazu k dosažení vyšší propustnosti.

Ačkoli toto řešení poskytuje dobrý úvod, není v žádném případě úplné. Další vzory dotazů najdete pomocí jazyka SAQL v [příkladech dotazu pro běžné vzorce využití Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
