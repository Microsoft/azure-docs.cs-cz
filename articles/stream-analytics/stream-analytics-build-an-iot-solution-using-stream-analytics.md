---
title: Sestavte řešení IoT pomocí Azure Stream Analytics
description: Úvodní kurz pro Stream Analytics řešení IoT ve scénáři Tollbooth
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ddec53b18cd6f374a5665298b43b46122bcfa143
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016146"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Sestavte řešení IoT pomocí Stream Analytics

## <a name="introduction"></a>Úvod
V tomto řešení se naučíte, jak pomocí Azure Stream Analytics získat informace z vašich dat v reálném čase. Vývojáři můžou snadno kombinovat datové proudy dat, jako jsou například datové proudy, protokoly a události generované zařízením, s historickými záznamy nebo referenční data pro odvození obchodních přehledů. Jako plně spravovaná služba výpočtu datových proudů v reálném čase, která je hostovaná v Microsoft Azure, Azure Stream Analytics nabízí integrovanou odolnost, nízkou latenci a škálovatelnost, díky které můžete v řádu minut pracovat.

Po dokončení tohoto řešení můžete provádět následující akce:

* Seznamte se s portálem Azure Stream Analytics.
* Nakonfigurujte a nasaďte úlohu streamování.
* Vyjadřují problémy reálného světa a vyřešte je pomocí dotazovacího jazyka Stream Analytics.
* K vývoji řešení streamování pro zákazníky můžete využít Stream Analytics s jistotou.
* K řešení problémů použijte prostředí pro monitorování a protokolování.

## <a name="prerequisites"></a>Předpoklady
K dokončení tohoto řešení potřebujete následující předpoklady:
* [Předplatné Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Úvod do scénáře: "Hello, placená!"
Stanice mýtné je běžným jev. Narazíte na řadu expressways, mostů a tunelů na celém světě. Každá placená stanice má několik zástánků za poplatek. V případě ručních zástupných stánků zabráníte platit placená telefonní platbu do průvodce. U automatizovaných kabin se snímač na každé kabině vyhledá na kartě RFID, která je připevněná k windshieldi vašeho vozidla, když předáte zástupné telefonní kabiny. Je snadné vizualizovat pasáž vozidel prostřednictvím těchto telefonních stanic jako datový proud událostí, na kterém je možné provádět zajímavé operace.

![Obrázek auta v zápočtu kabin](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Příchozí data
Toto řešení pracuje se dvěma datovými proudy. Senzory nainstalované ve vstupu a výstupu stanic pro telefonní linky vytváří první datový proud. Druhý datový proud je statická vyhledávací datová sada, která má registrační data vozidel.

### <a name="entry-data-stream"></a>Záznam datového streamu
Datový proud záznamu obsahuje informace o automobilůch při zadávání telefonních stanic. Události data ukončení jsou živé streamování do fronty centra událostí z webové aplikace obsažené v ukázkové aplikaci.

| TollID | EntryTime | LicensePlate | Stav | Značka | Modelování | VehicleType | VehicleWeight | Placená | Značka |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Tady je krátký popis sloupců:

| Sloupec | Popis |
| --- | --- |
| TollID |ID kabiny pro telefonní číslo, které jedinečně identifikuje telefonní kabinu |
| EntryTime |Datum a čas vstupu vozidla na telefonní kabinu v UTC |
| LicensePlate |Číslo licenční tabulky vozidla |
| Stav |Stav v USA |
| Značka |Výrobce automobilu |
| Modelování |Číslo modelu automobilu |
| VehicleType |1 pro osobní vozidla nebo 2 pro komerční vozidla |
| WeightType |Hmotnost vozidla v tunách; 0 pro osobní vozidla |
| Placená |Hodnota mýtné v USD |
| Značka |Elektronická značka na automobilu, který automatizuje platby; prázdné místo, kde byla platba provedena ručně |

### <a name="exit-data-stream"></a>Ukončit datový proud
Výstupní datový proud obsahuje informace o vozidlech opouštějících telefonní stanici. Události data ukončení jsou živé streamování do fronty centra událostí z webové aplikace obsažené v ukázkové aplikaci.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |CDE 1007 |

Tady je krátký popis sloupců:

| Sloupec | Popis |
| --- | --- |
| TollID |ID kabiny pro telefonní číslo, které jedinečně identifikuje telefonní kabinu |
| ExitTime |Datum a čas ukončení vozidla ze záplatku v UTC |
| LicensePlate |Číslo licenční tabulky vozidla |

### <a name="commercial-vehicle-registration-data"></a>Registrační data komerčních vozidel
Řešení používá statický snímek registrační databáze komerčních vozidel. Tato data se ukládají jako soubor JSON do úložiště objektů BLOB v Azure, které jsou součástí ukázky.

| LicensePlate | RegistrationId | Platnost vypršela |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Tady je krátký popis sloupců:

| Sloupec | Popis |
| --- | --- |
| LicensePlate |Číslo licenční tabulky vozidla |
| RegistrationId |Registrační ID vozidla |
| Platnost vypršela |Stav registrace vozidla: 0, pokud je aktivní registrace vozidla, 1, pokud vypršela platnost registrace |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Nastavení prostředí pro Azure Stream Analytics
K dokončení tohoto řešení potřebujete Microsoft Azure předplatné. Pokud nemáte účet Azure, můžete si [vyžádat bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

Nezapomeňte postupovat podle kroků uvedených v části Vyčištění účtu Azure na konci tohoto článku, abyste mohli co nejlépe využít kredit Azure.

## <a name="deploy-the-sample"></a>Nasazení ukázky
Existuje několik prostředků, které lze snadno nasadit ve skupině prostředků spolu s několika kliknutími. Definice řešení je hostována v úložišti GitHub na adrese [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Nasazení šablony TollApp do Azure Portal
1. K nasazení prostředí TollApp do Azure použijte tento odkaz k [nasazení šablony Azure TollApp](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Pokud se zobrazí výzva, přihlaste se k Azure Portal.

3. Vyberte předplatné, ve kterém se účtují různé prostředky.

4. Zadejte novou skupinu prostředků s jedinečným názvem, například `MyTollBooth` .

5. Vyberte umístění Azure.

6. Zadejte **interval** jako počet sekund. Tato hodnota se používá v ukázkové webové aplikaci, kdy se často odesílají data do centra událostí.

7. **Ověřte** , že souhlasíte s podmínkami a ujednáními.

8. Vyberte **Připnout na řídicí panel** , abyste později mohli snadno najít prostředky.

9. Vyberte **koupit** a nasaďte vzorovou šablonu.

10. Po chvíli se zobrazí oznámení o potvrzení **nasazení bylo úspěšné**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Kontrola prostředků Azure Stream Analytics TollApp

1. Přihlášení k webu Azure Portal

2. Vyhledejte skupinu prostředků, kterou jste pojmenovali v předchozí části.

3. Ověřte, že jsou ve skupině prostředků uvedené tyto prostředky:
   - Jeden Cosmos DB účet
   - Jedna Azure Stream Analytics úloha
   - Jeden Azure Storage účet
   - Jedno centrum událostí Azure
   - Dvě Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Projděte si ukázkovou úlohu TollApp
1. Počínaje ze skupiny prostředků v předchozí části vyberte úlohu streamování Stream Analytics, která začíná názvem **tollapp** (název obsahuje náhodné znaky pro jedinečnost).

2. Na stránce **Přehled** úlohy si všimněte pole **dotazu** , ve kterém se zobrazí syntaxe dotazu.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Řekněme, že k parafrázovali záměru dotazu, je třeba spočítat počet vozidel, která vstupují do kabiny. Vzhledem k tomu, že kabina silničního mýtné má souvislý proud vozidel vstupujících do provozu, jsou úvodní události analogické ke streamu, který se nikdy nezastaví. Chcete-li vyčíslit datový proud, je nutné definovat "časový interval" pro měření. Pojďme tuto otázku podrobněji zpřesnit, abyste mohli "kolik vozidel vstupuje v telefonním kabině každé tři minuty?" To se běžně označuje jako počet bubnů.

   Jak vidíte, Azure Stream Analytics používá dotazovací jazyk, který jako SQL, a přidává několik rozšíření pro určení aspektů dotazu souvisejících s časem.  Další podrobnosti najdete v článku o [správě času](/stream-analytics-query/time-management-azure-stream-analytics) a konstrukcích [okna](/stream-analytics-query/windowing-azure-stream-analytics) použitých v dotazu.

3. Prověřte vstupy vzorové úlohy TollApp. V aktuálním dotazu se používá pouze vstup EntryStream.
   - **EntryStream** input je připojení centra událostí, které při každém vstupu automobilu do Tollbooth na dálnici zařadí data do fronty. Webová aplikace, která je součástí ukázky, vytváří události a tato data jsou zařazená do fronty v tomto centru událostí. Všimněte si, že tento vstup je dotazován v klauzuli FROM dotazu streamování.
   - **ExitStream** input je připojení centra událostí, které zařadí data do fronty pokaždé, když automobil ukončí Tollbooth na dálnici. Tento vstup streamování se používá v pozdějších variacích syntaxe dotazu.
   - Vstup **registrace** je připojení úložiště objektů BLOB v Azure, které odkazuje na statický registration.jsv souboru, který se používá pro hledání podle potřeby. Tento vstup referenčních dat se používá v pozdějších variacích syntaxe dotazu.

4. Projděte si výstupy ukázkové úlohy TollApp.
   - **Cosmos DB** výstupem je kontejner databáze Cosmos, který přijímá výstupní události jímky. Všimněte si, že tento výstup se používá v klauzuli INTO dotazu streamování.

## <a name="start-the-tollapp-streaming-job"></a>Spuštění úlohy streamování TollApp
Pomocí těchto kroků spusťte úlohu streamování:

1. Na stránce **Přehled** úlohy vyberte **Spustit**.

2. V podokně **Spustit úlohu** vyberte **nyní**.

3. Po chvíli spuštění úlohy na stránce **Přehled** úlohy streamování Zobrazte graf **monitorování** . Graf by měl obsahovat několik tisíc vstupních událostí a desítky výstupních událostí.

## <a name="review-the-cosmosdb-output-data"></a>Kontrola výstupních dat CosmosDB
1. Vyhledejte skupinu prostředků, která obsahuje prostředky TollApp.

2. Vyberte účet Azure Cosmos DB se vzorem názvu **tollapp \<random\> -Cosmos**.

3. Vyberte záhlaví **Průzkumník dat** pro otevření stránky Průzkumník dat.

4. Rozbalte dokumenty **tollAppDatabase**  >  **tollAppCollection**  >  .

5. V seznamu ID se několik dokumentů zobrazuje až po zpřístupnění výstupu.

6. Pro kontrolu dokumentu JSON vyberte jednotlivá ID. Všimněte si jednotlivých tollid, windowend času a počtu automobilů z tohoto okna.

7. Po dalších třech minutách je k dispozici další sada čtyř dokumentů, jeden dokument na tollid.


## <a name="report-total-time-for-each-car"></a>Nahlásit celkovou dobu každého auta
Průměrná doba, kterou musí automobil předat telefonnímu rámci, pomáhá posoudit efektivitu procesu a prostředí pro zákazníky.

Pokud chcete zjistit celkový čas, připojte se ke streamu EntryTime ke streamu ExitTime. Připojte dva vstupní toky na sloupce EQUAL Matching TollId a LicencePlate. Operátor **Join** vyžaduje, abyste určili dočasné leewayy, které popisují přijatelný časový rozdíl mezi připojenými událostmi. Pomocí funkce **DateDiff** určíte, že události by neměly být delší než 15 minut. Pokud chcete vypočítat skutečný čas, kdy se automobil v telefonní stanici tráví, použijte také funkci **DateDiff** k ukončení a zadávání časů. Poznamenejte si rozdíl v použití funkce **DateDiff** při použití v příkazu **Select** , nikoli v rámci podmínky **spojení** .

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aktualizace syntaxe dotazu úlohy streamování TollApp:

1. Na stránce **Přehled** úlohy vyberte **zastavit**.

2. Chvíli počkejte a oznámení, že se úloha zastavila.

3. Pod hlavičkou topologie úlohy vyberte **< > dotaz** .

4. Vložte upravený dotaz SQL streamování.

5. Vyberte **Uložit** a dotaz uložte. Potvrďte změny uložením **Ano** .

6. Na stránce **Přehled** úlohy vyberte **Spustit**.

7. V podokně **Spustit úlohu** vyberte **nyní**.

### <a name="review-the-total-time-in-the-output"></a>Zkontrolujte celkový čas ve výstupu.
Zopakováním kroků v předchozí části zkontrolujete výstupní data CosmosDB z úlohy streamování. Projděte si nejnovější dokumenty JSON.

Tento dokument například ukazuje příklad auta s určitou licenční deskou, časem entryTime a ukončení a polem durationinminutes funkce DATEDIFF, které zobrazuje dobu trvání kabiny jako dvě minuty:
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

## <a name="report-vehicles-with-expired-registration"></a>Hlášení vozidel s registrací s vypršenou platností
Azure Stream Analytics může použít statické snímky referenčních dat pro připojení k dočasným datovým proudům. K tomu, abyste mohli tuto funkci předvést, použijte následující dotaz na ukázku. Vstup registrace je statický soubor JSON pro objekty blob, ve kterém jsou uvedené vypršení platnosti licenčních značek. Když se připojíte k licenční desce, referenční data se porovnávají se všemi vozidly, které procházejí telefonním spojením.

Pokud je komerční vozidlo registrované u placené telefonní společnosti, může projít telefonní kabinou bez zastavení pro kontrolu. K identifikaci všech komerčních vozidel, jejichž registrace vypršela, použijte tabulku pro vyhledávání registrací.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Zopakováním kroků v předchozí části aktualizujte syntaxi dotazu úlohy streamování TollApp.

2. Zopakováním kroků v předchozí části zkontrolujete výstupní data CosmosDB z úlohy streamování.

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

## <a name="scale-out-the-job"></a>Horizontální navýšení kapacity úlohy
Azure Stream Analytics je navržena pro elastické škálování tak, aby mohl zpracovávat velké objemy dat. Dotaz Azure Stream Analytics může použít klauzuli **partition by** k tomu, aby systém informoval, že tento krok se škáluje. **PartitionID** je speciální sloupec, který systém přidává, aby odpovídal ID oddílu vstupu (centra událostí).

Pro horizontální navýšení kapacity dotazu na oddíly upravte syntaxi dotazu na následující kód:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Postup při horizontálním navýšení kapacity úlohy streamování na více jednotek streamování:

1. **Zastavte** aktuální úlohu.

2. Aktualizujte syntaxi dotazu na stránce **< > dotaz** a uložte změny.

3. V části konfigurovat záhlaví v úloze streamování vyberte možnost **škálovat**.

4. Posuňte posuvník **jednotky streamování** od 1 do 6. Jednotky streamování definují množství výpočetní výkon, který může úloha přijmout. Vyberte **Uložit**.

5. **Spusťte** úlohu streamování, abyste ukázali dodatečné měřítko. Azure Stream Analytics distribuuje práci mezi více výpočetních prostředků a dosahuje lepší propustnosti, rozdělit práci mezi prostředky pomocí sloupce určeného v klauzuli PARTITION BY.

## <a name="monitor-the-job"></a>Monitorování úlohy
Oblast **monitorování** obsahuje statistiku o spuštěné úloze. První konfigurace je nutná k použití účtu úložiště ve stejné oblasti (název placená linka jako zbytek tohoto dokumentu).

![Monitorování úloh Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

K **protokolům aktivit** můžete přistupovat také z oblasti **Nastavení** řídicího panelu úloh.

## <a name="clean-up-the-tollapp-resources"></a>Vyčištění prostředků TollApp
1. Zastavte úlohu Stream Analytics v Azure Portal.

2. Vyhledejte skupinu prostředků, která obsahuje osm prostředků souvisejících se šablonou TollApp.

3. Vyberte **Odstranit skupinu prostředků**. Zadáním názvu skupiny prostředků potvrďte odstranění.

## <a name="conclusion"></a>Závěr
Toto řešení vás zavedlo ke službě Azure Stream Analytics. Ukázala, jak nakonfigurovat vstupy a výstupy pro úlohu Stream Analytics. Pomocí scénáře s daty o telefonních číslech řešení bylo vysvětleno běžné typy problémů, které vznikají v prostoru dat v pohybu, a jak je lze vyřešit pomocí jednoduchých dotazů typu SQL, které jsou v Azure Stream Analytics. Řešení popisuje konstrukce rozšíření SQL pro práci s dočasnými daty. Ukázal, jak spojit datové proudy, jak rozšířit datový proud pomocí statických referenčních dat a jak škálovat dotaz, abyste dosáhli vyšší propustnosti.

I když toto řešení přináší dobrý úvod, není to nic nekompletních. V [příkladech dotazů pro běžné Stream Analytics způsoby použití](stream-analytics-stream-analytics-query-patterns.md)můžete najít další vzory dotazů pomocí jazyka SAQL.