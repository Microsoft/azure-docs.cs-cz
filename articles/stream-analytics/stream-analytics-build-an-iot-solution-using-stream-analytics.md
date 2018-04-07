---
title: Vytvoření řešení IoT pomocí služby Azure Stream Analytics
description: Úvodní kurz pro řešení Stream Analytics IoT mýtná celnice scénáře
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Vytvoření řešení IoT pomocí služby Stream Analytics

## <a name="introduction"></a>Úvod
V tomto řešení a zjistěte, jak používat Azure Stream Analytics z dat získat přehledy v reálném čase. Datové proudy dat, například klikněte na tlačítko-datové proudy, protokoly a události generované zařízení mohou vývojáři kombinovat snadno s staré záznamy nebo referenční data odvození informací o podniku. Jako výpočetní služba plně spravovaná, v reálném čase datový proud, který je hostován v Microsoft Azure Azure Stream Analytics poskytuje integrované odolnost proti chybám, s nízkou latencí a škálovatelnost, které jste si a spustit v minutách.

Po dokončení tohoto řešení, budete moci:

* Seznamte se s portálem Azure Stream Analytics.
* Nakonfigurujte a nasaďte úlohu streamování.
* Vyjádřete reálného problémy a řešení je pomocí dotazovacího jazyka pro Stream Analytics.
* Vývoj streamování řešení pro vaše zákazníky pomocí služby Stream Analytics s jistotou.
* Sledování a protokolování prostředí používejte k řešení potíží.

## <a name="prerequisites"></a>Požadavky
Je třeba požadovaný k dokončení tohoto řešení:
* [Předplatného Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scénář Úvod: "Hello, Projedou!"
Stanice projedou je běžné jevu. Narazíte je na mnoha rychlostních, mosty a tunely po celém světě. Každé stanici projedou má více kabin projedou. Na ruční kabin zastavíte platit projedou k průvodcem. Senzor nad každý stánek na automatizované kabin kontroluje RFID kartu, která je opatřit čelního skla z vaší vehicle, jak předat stánek projedou. Je snadné k vizualizaci průchod vozidel přes tyto stanice projedou jako datového proudu událostí za které zajímavé operace lze provádět.

![Obrázek automobilů na projedou kabin](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Příchozí data
Toto řešení funguje s dvěma datových proudů. Nainstalovaných ve vstupu a ukončení stanice projedou vytvořit první datového proudu. Druhý datový proud je statický vyhledávání datovou sadu, která má vehicle registrační data.

### <a name="entry-data-stream"></a>Vstupní datový proud
Vstupní datový proud obsahuje informace o aut při vstupu projedou stanice. Výstupní data události jsou nasazeny datového proudu do frontu centra událostí z webové aplikace součástí ukázkovou aplikaci.

| TollID | EntryTime | LicensePlate | Stav | Make | Model | VehicleType | VehicleWeight | Projedou | Značka |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NI |Toyota |4x4 |1 |0 |6 |321987654 |

Zde je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| TollID |ID stánek projedou, která jednoznačně identifikuje stánek projedou |
| EntryTime |Datum a čas položky vehicle k stánek projedou v UTC |
| LicensePlate |Počet registrační nástroj |
| Stav |Stav v USA |
| Make |Výrobce daného automobilu |
| Model |Číslo modelu daného automobilu |
| VehicleType |Buď 1 pro osobní vozidel či 2 pro komerční vozidel |
| WeightType |Vehicle váhy v tunách; 0 pro osobní vozidel |
| Projedou |Hodnota projedou v USD |
| Značka |E-Tag na automobilu, který zautomatizuje platebních; prázdné, kde je platba bylo provedeno ručně |

### <a name="exit-data-stream"></a>Výstupní datový proud
Výstupní datový proud obsahuje informace o aut ponechat projedou stanice. Výstupní data události jsou nasazeny datového proudu do frontu centra událostí z webové aplikace součástí ukázkovou aplikaci.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Zde je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| TollID |ID stánek projedou, která jednoznačně identifikuje stánek projedou |
| ExitTime |Datum a čas ukončení nástroj z projedou stánek v UTC |
| LicensePlate |Počet registrační nástroj |

### <a name="commercial-vehicle-registration-data"></a>Vehicle komerční registrační data
Toto řešení využívá statické snímek databáze komerční vehicle registrace. Tato data se ukládají jako soubor JSON do úložiště objektů blob v Azure, zahrnuté v ukázce.

| LicensePlate | RegistrationId | Vypršela platnost |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Zde je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| LicensePlate |Počet registrační nástroj |
| RegistrationId |ID registrace vehicle |
| Vypršela platnost |Stav registrace nástroj: 0, pokud je aktivní, vehicle registrace 1, pokud platnost registrace |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Nastavení prostředí pro Azure Stream Analytics
K dokončení tohoto řešení, potřebujete předplatné Microsoft Azure. Pokud účet Azure nemáte, můžete [požadavku bezplatná zkušební verze](http://azure.microsoft.com/pricing/free-trial/).

Ujistěte se, postupujte podle kroků v části "Vyčištění účtu Azure" na konci tohoto článku, tak, aby bylo možné provést doporučené použití Azure kreditu.

## <a name="deploy-the-sample"></a>Ukázka nasazení 
Existuje několik prostředků, které lze snadno nasadit ve skupině prostředků, spolu s několika kliknutí. Definice řešení je hostován v úložišti github v [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Nasazení šablony TollApp na portálu Azure
1. TollApp prostředí nasadit do Azure, použijte tento odkaz na [nasazení šablony Azure TollApp](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Pokud budete vyzváni, přihlaste se k portálu Azure.

3. Zvolte předplatné, ve kterém se účtují různým prostředkům.

4. Zadejte novou skupinu prostředků s jedinečným názvem, například `MyTollBooth`. 

5. Vyberte umístění Azure.

6. Zadejte **Interval** jako počet sekund. Tato hodnota se používá v ukázkovou webovou aplikaci, jak často se bude odesílat data do centra událostí. 

7. **Zkontrolujte** potvrdit souhlas s podmínkami a ujednáními.

8. Vyberte **připnout na řídicí panel** , aby mohl snadno najít prostředky později.

9. Vyberte **nákupu** k nasazení šablony ukázka.

10. Po chvíli se k potvrzení, zobrazí se upozornění **nasazení bylo úspěšné**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Zkontrolujte prostředky Azure Stream Analytics TollApp
1. Přihlášení k portálu Azure Portal

2. Najděte skupinu prostředků, která s názvem v předchozí části.

3. Ověřte, že jsou ve skupině prostředků uvedené v následujících zdrojích informací:
   - Jeden Cosmos DB účtu
   - Jeden úlohy Azure Stream Analytics
   - Jeden účet úložiště Azure
   - One Azure Event Hub
   - Two Web Apps

## <a name="examine-the-sample-tollapp-job"></a>Zkontrolujte úlohu TollApp vzorku 
1. Spouštění ze skupiny prostředků v předchozí části, vyberte úlohu služby Stream Analytics streamování spouští s názvem **tollapp** (název obsahuje náhodných znaků jedinečnosti).

2. Na **přehled** stránku úlohy, upozornění **dotazu** políčka zobrazit syntaxi dotazu.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafrázovat záměr dotazu, Řekněme, že je potřeba počítat počet vozidel, které projedou stánek zadejte. Protože highway projedou stánek má může nepřetržitý proud vozidel zadávání, ty jsou události vstupu se podobá datový proud, který nikdy zastaví. Chcete-li vyčíslení datového proudu, budete muset definovat "v časovém intervalu" k měření přes. Umožňuje upřesněte dotaz navíc na "kolik vozidel zadejte projedou stánek každé tři minuty?" To se obvykle označuje jako počet přeskakující.

   Jak vidíte, Azure Stream Analytics používá dotazovací jazyk, který je například SQL a přidává několik rozšíření k určení souvisejících s časem aspekty dotazu.  Další podrobnosti najdete v tématu o [Správa času](https://msdn.microsoft.com/library/azure/mt582045.aspx) a [Oddílová](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukce použitý v dotazu.

3. Zkontrolujte vstupní hodnoty TollApp ukázkové úlohy. Pouze vstupní EntryStream se používá v aktuálním dotazu.
   - **EntryStream** vstup je připojení k centra událostí, která data představující pokaždé, když automobilu zadá mýtná celnice na komunikacích fronty. Webovou aplikaci, která je součástí ukázce je vytvoření události a data zařazených do fronty v toto Centrum událostí. Všimněte si, že tento vstup je dotazován v klauzuli FROM streamování dotazu.
   - **ExitStream** vstup je připojení k centra událostí, která data představující pokaždé, když automobilu ukončí mýtná celnice na komunikacích fronty. Tento vstup streamování se používá v novější variace syntaxe dotazu.
   - **Registrace** vstup je připojení k Azure Blob storage, odkazující na soubor statické registration.json používá k vyhledávání podle potřeby. Tato referenčního datového vstupu se používá v novější variace syntaxe dotazu.

4. Zkontrolujte výstupy TollApp ukázkové úlohy.
   - **Cosmos DB** výstup je kolekce Cosmos databáze, která přijímá události výstupní jímku. Všimněte si, že tento výstup se používá v do klauzuli streamování dotazu.

## <a name="start-the-tollapp-streaming-job"></a>Spustit úlohu streamování TollApp
Postupujte podle těchto kroků spusťte úlohu streamování:

1. Na **přehled** stránku úlohy, vyberte **spustit**.

2. Na **spuštění úlohy** podokně, vyberte **nyní**.

3. Po chvíli, když úloha běží na **přehled** stránku streamování úlohy, zobrazení **monitorování** grafu. Graf by měl zobrazit několika tisíc vstupních událostech a desítkami výstupních událostech.

## <a name="review-the-cosmosdb-output-data"></a>Zkontrolujte CosmosDB výstupní data
1. Najděte skupinu prostředků, která obsahuje TollApp prostředky.

2. Vyberte účet Azure Cosmos databáze se stejným názvem vzor **tollapp<random>-cosmos**.

3. Vyberte **Průzkumníku dat** záhlaví chcete otevřít stránku Průzkumníku dat.

4. Rozbalte **tollAppDatabase** > **tollAppCollection** > **dokumenty**.

5. V seznamu identifikátorů několik dokumentace se zobrazí po výstup je k dispozici.

6. Vyberte každý id Chcete-li zkontrolovat dokumentu JSON. Všimněte si každý tollid windowend čas a počet aut z tohoto okna.

7. Po další tři minuty, další sada čtyři dokumenty je k dispozici, jeden dokument za tollid. 


## <a name="report-total-time-for-each-car"></a>Celkový čas sestavy pro každý Auto
Průměrná doba, které je nutné pro automobilu předávat projedou pomáhá vyhodnocení efektivitu proces a zkušeností zákazníků.

Celkový čas najdete připojte datový proud EntryTime s ExitTime datového proudu. Připojení dvě vstupní datové proudy pro stejné odpovídající TollId a LicencePlate sloupce. **Připojení** operátor vyžaduje, abyste zadejte dočasné volnost, který popisuje přijatelný časový rozdíl mezi připojené k události. Použití **DATEDIFF** funkce k určení, že události musí být delší než 15 minut od sebe navzájem. Platí také **DATEDIFF** funkce ukončíte a časy položku k výpočtu skutečnou času stráví automobilu projedou stanice. Všimněte si rozdílu použití **DATEDIFF** při použití v **vyberte** příkaz ne **připojení** podmínku.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aktualizace TollApp streamování syntaxe dotazu úlohy:

1. Na **přehled** stránku úlohy, vyberte **Zastavit**.

2. Chvíli počkejte pro oznámení, že úloha byla zastavena.

3. V části úlohy TOPOLOGIE záhlaví, vyberte **< > dotazu**

4. Vložte upravenou streamování dotaz SQL.

5. Vyberte **Uložit** Uložte dotaz. Potvrďte **Ano** a uložte změny.

6. Na **přehled** stránku úlohy, vyberte **spustit**.

7. Na **spuštění úlohy** podokně, vyberte **nyní**.

### <a name="review-the-total-time-in-the-output"></a>Zkontrolujte celkový čas ve výstupu
Opakujte kroky v předchozí části, chcete-li zkontrolovat CosmosDB výstupní data z datových proudů úlohy. Přečtěte si nejnovější dokumentů JSON. 

Například tento dokument uvádí car příklad s určité registrační, čas entrytime a ukončete a pole DATEDIFF počítané doba trvání v minutách, zobrazuje dobu trvání projedou stánek jako dvě minuty: 
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

## <a name="report-vehicles-with-expired-registration"></a>Sestava vozidel s vypršenou platností registrace
Azure Stream Analytics můžete použít statické snímky referenčních dat spojení s dočasné datové proudy. K předvedení tato možnost, použijte následující ukázkový dotaz. Vstup registrace je soubor json statické objekt blob se seznamem vypršení platnosti licence značky. Díky připojení ke službě na štítku licence, referenční data se porovná s každou vehicle prošla projedou obou. 

Pokud komerční vehicle je registrován ve společnosti projedou, můžete předat prostřednictvím stánek projedou bez zastavení pro kontroly. Tabulku vyhledávání registrace použijte k určení všech vozidel komerční, jejichž platnost vypršela registrace.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Opakujte kroky v předchozí části aktualizovat TollApp streamování syntaxe dotazu úlohy.

2. Opakujte kroky v předchozí části, chcete-li zkontrolovat CosmosDB výstupní data z datových proudů úlohy. 

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
Azure Stream Analytics je určena pro pružně škálování tak, aby ho může zpracovávat velké objemy dat. Můžete použít Azure Stream Analytics dotaz **PARTITION BY** klauzule systému říct, že horizontálně navýší kapacitu tento krok. **PartitionId** je speciální sloupec, který přidá systému tak, aby odpovídaly ID oddílu vstupu (Centrum událostí).

Chcete-li škálovat dotaz do oddílů, upravte syntaxe dotazu na následující kód:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Škálování na víc jednotek streamování streamování úlohy:

1. **Zastavit** aktuální úlohu. 

2. Aktualizovat syntaxe dotazu **< > dotazu** stránky a uložte změny.

3. V části konfigurace na úlohu streamování, vyberte **škálování**.
   
4. Posuňte **jednotek Streaming** jezdce od 1 do 6. Jednotky streamování definovat množství výpočetního výkonu, který může přijímat úlohy. Vyberte **Uložit**.

5. **Spustit** úlohu streamování k předvedení další škálování. Azure Stream Analytics rozdělí práci víc zdrojích výpočtů a dosáhnout lepší propustnosti, vytváření oddílů práci mezi prostředky pomocí sloupci uvedených v klauzuli PARTITION BY. 

## <a name="monitor-the-job"></a>Monitorování úlohy
**Monitorování** oblast obsahuje statistiku spuštěná úloha. První konfigurace je potřeba k použití účet úložiště ve stejné oblasti (název poplatků jako zbytek tohoto dokumentu).   

![Snímek obrazovky monitorování](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Dostanete **protokoly aktivity** z řídicího panelu úloha **nastavení** také oblasti.

## <a name="clean-up-the-tollapp-resources"></a>Vyčištění prostředků TollApp
1. Zastavte úlohu služby Stream Analytics na portálu Azure.

2. Najděte skupinu prostředků, která obsahuje osm materiály související s TollApp šablony.

3. Vyberte **Odstranit skupinu prostředků**. Zadejte název skupiny prostředků potvrďte odstranění.

## <a name="conclusion"></a>Závěr
Toto řešení je zavedená do služby Azure Stream Analytics. Je prokázat, jak nakonfigurovat vstupy a výstupy úlohy Stream Analytics. Pomocí dat Projedou scénář, popsaných řešení nejběžnějších problémů, které jsou vyvolány v prostoru dat v provozu a jak lze vyřešit pomocí jednoduchého dotazy podobné jazyku SQL v Azure Stream Analytics. Řešení popsaná konstrukce rozšíření SQL pro práci s dočasná data. Vám ukázal, jak propojit datové proudy, jak zlepšit komunikaci oddělení datový proud s statická referenční data a postup škálování dotazů k dosažení vyšší propustnost.

I když toto řešení poskytuje úvod funkční, není kompletní, a to jakýmkoli způsobem. Můžete najít další typy dotazů pomocí jazyka SAQL v [dotaz příkladů běžných vzorů využití Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
