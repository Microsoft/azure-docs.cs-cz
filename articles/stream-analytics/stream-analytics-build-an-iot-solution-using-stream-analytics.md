---
title: Sestavení řešení IoT pomocí Azure Stream Analytics
description: Úvodní kurz pro řešení Stream Analytics IoT tollbooth scénáře
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 230318dc8e352a3adc970b13f20fa992954e3b15
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091090"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Sestavení řešení IoT s použitím Stream Analytics

## <a name="introduction"></a>Úvod
V tomto řešení se dozvíte, jak získat z vašich dat v reálném čase pomocí Azure Stream Analytics. Vývojářům můžete snadno kombinovat datové proudy dat, jako je například klikněte na tlačítko datové proudy, protokolů a událostí generovaných zařízením s historickými záznamy nebo referenčními daty. Chcete-li vytvořit firemní přehledy. Jako služba v reálném čase, plně spravovaný datový proud výpočet, který je hostován v Microsoft Azure Azure Stream Analytics poskytuje integrované odolnost, nízkou latenci a škálovatelnost, aby mohli vše okamžitě zprovoznit během pár minut poběží.

Po dokončení tohoto řešení, budete moct:

* Seznamte se s portálem Azure Stream Analytics.
* Konfigurace a nasazení úlohy streamování.
* Srozumitelně vysvětlit skutečné problémy a řešení s použitím dotazovací jazyk Stream Analytics.
* Vývoj řešení pro vaše zákazníky streamování s použitím Stream Analytics s jistotou.
* Řešení potíží pomocí sledování a protokolování prostředí.

## <a name="prerequisites"></a>Požadavky
Potřebujete následující požadované součásti pro dokončení tohoto řešení:
* [Předplatné Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Úvod scénář: "Hello, linka!"
Stanice linka je běžné jev. Kterými je mnoho rychlostních, přemostění a tunely po celém světě. Každá stanice linka má více kabin linka. Při ruční kabin zastavíte platit linka průvodcem. Na automatické kabin prohledává senzoru na každý z mýtných bran RFID karty, který je opatřit čelního skla vaše vozidla, jak předat z mýtných bran linka. Je snadno pochopitelný průchod vozidel přes tyto stanice linka jako datového proudu událostí nad tím, které zajímavé operace lze provádět.

! [Obrázek auta v linka kabin] (media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth JPG)

## <a name="incoming-data"></a>Příchozí data
Toto řešení funguje s dvěma datových proudů. Nainstalovaných ve vstupu a výstupu stanice linka vytvořit první datový proud. Druhý datový proud je datová sada statické vyhledávání, který má vozidla registrační data.

### <a name="entry-data-stream"></a>Vstupní datový proud
Vstupní datový proud obsahuje informace o automobilů při vstupu linka stanice. Data události ukončení jsou živé streamování do fronty centra událostí z webové aplikace součástí ukázkové aplikace.

| TollID | EntryTime | LicensePlate | Stav | Vytvoření | Model | VehicleType | VehicleWeight | Linka | Značka |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Koruny |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Tady je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| TollID |ID linka z mýtných bran, které jednoznačně identifikuje stánku linka |
| EntryTime |Datum a čas vstupu vozidla z mýtných bran linka ve standardu UTC |
| LicensePlate |Registrační číslo vozidla |
| Stav |Stav ve Spojených státech |
| Vytvoření |Výrobce daného automobilu |
| Model |Číslo modelu daného automobilu |
| VehicleType |1 pro osobní vozidel či 2 pro komerční vozidla |
| WeightType |Váha vozidla v tunách; 0 pro osobní vozidel |
| Linka |Hodnota linka v USD |
| Značka |E-Tag v automobilu, který automatizuje platby; prázdné, pokud způsob platby byl provést ručně |

### <a name="exit-data-stream"></a>Konec datového proudu
Konec datového proudu obsahuje informace o auta opuštění linka stanice. Data události ukončení jsou živé streamování do fronty centra událostí z webové aplikace součástí ukázkové aplikace.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Tady je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| TollID |ID linka z mýtných bran, které jednoznačně identifikuje stánku linka |
| ExitTime |Datum a čas ukončení vozidla z mýtných bran linka ve standardu UTC |
| LicensePlate |Registrační číslo vozidla |

### <a name="commercial-vehicle-registration-data"></a>Komerční vozidla registrační data
Toto řešení využívá statické snímek komerční vozidla registrační databázi. Tato data se uloží jako soubor JSON do úložiště objektů blob v Azure, obsažené v ukázce.

| LicensePlate | RegistrationId | Vypršela platnost |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Tady je stručný popis sloupce:

| Sloupec | Popis |
| --- | --- |
| LicensePlate |Registrační číslo vozidla |
| RegistrationId |ID registrace vozidla |
| Vypršela platnost |Stav registrace vozidla: 0, pokud vozidla registrace je aktivní, 1, pokud vypršela platnost registrace |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Nastavení prostředí pro Azure Stream Analytics
K dokončení tohoto řešení, budete potřebovat předplatné Microsoft Azure. Pokud nemáte účet Azure, můžete si [požádat o bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

Je potřeba, aby co nejlíp využít svůj kredit Azure lze provést, postupujte podle kroků v části "Vyčistit účtu Azure" na konci tohoto článku.

## <a name="deploy-the-sample"></a>Nasazení ukázky 
Existuje několik prostředků, které je možné snadno nasadit do skupiny prostředků spolu s několika kliknutími. Definice řešení je hostovaná v úložišti github na [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Nasazení šablony TollApp na webu Azure Portal
1. Nasazení TollApp prostředí do Azure, použijte tento odkaz [nasazení šablony Azure TollApp](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Pokud se zobrazí výzva, přihlaste se k webu Azure portal.

3. Vyberte předplatné, ve kterém se účtují různé prostředky.

4. Zadejte novou skupinu prostředků s jedinečným názvem, třeba `MyTollBooth`. 

5. Vyberte umístění Azure.

6. Zadejte **Interval** jako počet sekund. Tato hodnota se používá v ukázkové webové aplikaci, jak často chcete odesílat data do centra událostí. 

7. **Zkontrolujte** souhlas s podmínkami a ujednáními.

8. Vyberte **připnout na řídicí panel** tak, že máte jednoduchý přístup k prostředkům později.

9. Vyberte **nákupní** nasadit ukázkovou šablonu.

10. Po chvíli, zobrazí se upozornění, potvrďte, **nasazení bylo úspěšné**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Kontrola prostředků Azure Stream Analytics TollApp
1. Přihlášení k portálu Azure Portal

2. Vyhledejte skupinu prostředků, kterou jste pojmenovali v předchozí části.

3. Ověřte, že jsou ve skupině prostředků uvedené v následujících zdrojích:
   - Účet jeden Cosmos DB
   - Jednu úlohu Azure Stream Analytics
   - Jeden účet úložiště Azure
   - Jedno Centrum událostí Azure
   - Dvě webové aplikace

## <a name="examine-the-sample-tollapp-job"></a>Prozkoumat ukázkové úlohy TollApp 
1. Spouští se ze skupiny prostředků v předchozí části, vyberte streamování úlohy Stream Analytics začíná název **tollapp** (náhodných znaků jedinečný název obsahuje).

2. Na **přehled** stránka úlohy, Všimněte si, že **dotazu** políčko, chcete-li zobrazit syntaxi dotazu.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Parafrázovat záměru dotazu, Řekněme, že je potřeba vypočítat počet vozidel, které zadejte stánku linka. Protože stánku linka highway obsahuje nepřetržitý datový proud vozidel zadáte, jsou události vstupu jsou obdobou datový proud, který nikdy nezastaví. Kvantifikovat datového proudu, je nutné definovat "určitou dobu" k měření průběhu. Pojďme upřesňování, dále "kolik vozidel zadat linka stánku každé 3 minuty?" To se obvykle označuje jako aktivační událost pro přeskakující count.

   Jak je vidět, Azure Stream Analytics používá dotazovací jazyk, který je například SQL a přidává několik rozšíření k určení souvisejících s časem aspekty dotazu.  Další informace, přečtěte si informace o [Správa času](https://msdn.microsoft.com/library/azure/mt582045.aspx) a [Oddílová](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukce použitý v dotazu.

3. Zkontrolujte vstupy TollApp ukázkové úlohy. V aktuálním dotazu se používá pouze EntryStream vstup.
   - **EntryStream** připojení k Centru událostí, který zařadí do fronty dat, která představují pokaždé, když automobilu zadá tollbooth na komunikacích se vstup. Webové aplikace, která je součástí vzorku vytváří události a je zařazeno do fronty tato data do centra událostí. Všimněte si, že tento vstup je dotazován v klauzuli FROM streamování dotazu.
   - **ExitStream** připojení k Centru událostí, který zařadí do fronty dat, která představují pokaždé, když automobilu ukončí tollbooth na komunikacích se vstup. Tento vstup streamování se používá v pozdější variace syntaxi dotazu.
   - **Registrace** vstup je připojení k Azure Blob storage, odkazuje na soubor statické registration.json, používá pro vyhledávání podle potřeby. Tato referenčního datového vstupu se používá v pozdější variace syntaxi dotazu.

4. Zkontrolujte výstupy TollApp ukázkové úlohy.
   - **Cosmos DB** výstup je kolekce databáze Cosmos, která přijímá události výstupní jímky. Všimněte si, že tento výstup je používán v klauzuli streamování dotazu.

## <a name="start-the-tollapp-streaming-job"></a>Spuštění úlohy streamování TollApp
Postupujte podle těchto kroků ke spuštění úlohy streamování:

1. Na **přehled** stránka úlohy, vyberte **Start**.

2. Na **spuštění úlohy** vyberte **nyní**.

3. Po chvíli, když úloha běží na **přehled** stránky úlohu streamování, zobrazení **monitorování** grafu. Graf by se zobrazit několik tisíc vstupních událostí a desítky výstupních událostí.

## <a name="review-the-cosmosdb-output-data"></a>Zkontrolujte výstupní data služby cosmos DB
1. Vyhledejte skupinu prostředků obsahující prostředky TollApp.

2. Vyberte účet Azure Cosmos DB se vzorem názvu **tollapp<random>– cosmos**.

3. Vyberte **Průzkumník dat** záhlaví otevřete Průzkumníka dat stránky.

4. Rozbalte **tollAppDatabase** > **tollAppCollection** > **dokumenty**.

5. V seznamu identifikátorů se zobrazí několik dokumentace po výstup je k dispozici.

6. Vyberte každé id k revizi dokumentu JSON. Všimněte si, že každý tollid windowend čas a počet aut z tohoto okna.

7. Po další tři minuty, další sadu čtyři dokumenty je k dispozici, jeden dokument za tollid. 


## <a name="report-total-time-for-each-car"></a>Celkový čas sestavy pro každé auto
Průměrná doba potřebná pro automobilu předávání linka pomáhá vyhodnotit účinnost procesu a uživatelské prostředí.

Chcete-li zjistit celkový čas, připojte se k EntryTime datového proudu pomocí služby stream ExitTime. Spojte dva proudy vstupní na stejné odpovídající TollId a LicencePlate sloupce. **Připojení** operátor vyžaduje nastavení dočasné volnost, který popisuje přijatelný časový rozdíl mezi připojeného k události. Použití **DATEDIFF** funkce určete, že se události mají více než 15 minut od sebe navzájem. Platí také **DATEDIFF** funkce ukončíte a časy vstupu pro výpočet skutečný čas automobilu stráví linka stanice. Všimněte si rozdílu použití **DATEDIFF** když se používá v **vyberte** příkaz spíše než **připojte se k** podmínku.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Chcete-li aktualizovat TollApp streamování syntaxe dotazu úlohy:

1. Na **přehled** stránka úlohy, vyberte **Zastavit**.

2. Chvíli počkejte, než pro oznámení, že úloha se zastavila.

3. V části TOPOLOGIE úlohy, vyberte **< > dotazu**

4. Vložte upravené streamování jazyka SQL.

5. Vyberte **Uložit** Uložte dotaz. Potvrďte **Ano** a uložte změny.

6. Na **přehled** stránka úlohy, vyberte **Start**.

7. Na **spuštění úlohy** vyberte **nyní**.

### <a name="review-the-total-time-in-the-output"></a>Zkontrolujte celkový čas ve výstupu
Opakujte kroky v předchozí části zkontrolovat CosmosDB výstupních dat úlohy streamování. Přečtěte si nejnovější dokumenty JSON. 

Například tento dokument ukazuje automobilu příklad některých registrační, času entrytime a ukončení a pole DATEDIFF vypočtená doba trvání v minutách, zobrazuje dobu trvání z mýtných bran linka jako dvě minuty: 
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

## <a name="report-vehicles-with-expired-registration"></a>Sestava vozidla s vypršenou platností registrací
Azure Stream Analytics můžete použít snímky statická referenční data spojovat s dočasné datové proudy. Abychom si předvedli tuto funkci, použijte následující ukázkový dotaz. Vstupní pole registrace je soubor json statických objektů blob, který obsahuje seznam vypršení platnosti licence značek. Díky připojení ke službě na desce licence, referenční data porovnání s každou vozidel procházející linka obě. 

Pokud je komerční vozidla zaregistrován u linka společnosti, můžete předat prostřednictvím z mýtných bran linka bez zastavení pro kontrolu. Pomocí registrace vyhledávací tabulky Identifikujte všechny komerční vozidla, jejichž platnost vypršela registrace.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Opakujte kroky v předchozí části, chcete-li aktualizovat TollApp syntaxe dotazu úlohy streamování.

2. Opakujte kroky v předchozí části zkontrolovat CosmosDB výstupních dat úlohy streamování. 

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

## <a name="scale-out-the-job"></a>Horizontální navýšení kapacity úlohu
Azure Stream Analytics byla navržena Elasticky škálovat tak, aby ho může zpracovávat velké objemy dat. Dotaz Azure Stream Analytics můžete používat **PARTITION BY** klauzule oznámit systému, že tento krok horizontálně navyšuje jeho kapacita. **ID oddílu** je speciální sloupcem, která přidá systém tak, aby odpovídaly ID oddílu vstupu (centra událostí).

Pro horizontální navýšení kapacity dotazu do oddílů, upravte syntaxi dotazů k následujícím kódem:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Vertikálně navýšit kapacitu na víc jednotek streamování datové proudy úlohy:

1. **Zastavit** aktuální úlohu. 

2. Aktualizace v syntaxi dotazu **< > dotazu** stránce a uložte změny.

3. V části konfigurace na úlohu streamování, vyberte **škálování**.
   
4. Nastavte posuvník **jednotek streamování** posuvníku z 1 až 6. Jednotky streamování definovat množství výpočetní výkon, který může přijímat úlohy. Vyberte **Uložit**.

5. **Spustit** úlohu streamování předvést další škálování. Azure Stream Analytics rozděluje práci mezi víc výpočetních prostředků a dosáhnout lepší propustnosti, rozdělení práce napříč prostředky pomocí sloupce určené v klauzuli PARTITION BY. 

## <a name="monitor-the-job"></a>Monitorování úlohy
**Monitorování** oblasti obsahuje statistické údaje o běžící úlohu. První konfigurace je potřeba pro použití účtu úložiště ve stejné oblasti (název linka jako zbytek tohoto dokumentu).   

![Monitorování úloh v Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Můžete přistupovat **protokoly aktivit** z řídicího panelu úloha **nastavení** také oblast.

## <a name="clean-up-the-tollapp-resources"></a>Vyčištění prostředků TollApp
1. Po zastavení úlohy Stream Analytics na portálu Azure portal.

2. Vyhledejte skupinu prostředků, který obsahuje osm prostředky vztahující se k šabloně TollApp.

3. Vyberte **Odstranit skupinu prostředků**. Zadejte název skupiny prostředků pro potvrzení odstranění.

## <a name="conclusion"></a>Závěr
Toto řešení je zavedená ve službě Azure Stream Analytics. Jsme vám ukázali jak nakonfigurovat vstupy a výstupy pro úlohu Stream Analytics. Použití dat linka scénáře, řešení je vysvětleno běžné typy problémů, které vznikají v prostoru dat v pohybu a jak je možné řešit pomocí jednoduchých dotazů podobném SQL ve službě Azure Stream Analytics. Řešení popsaná konstrukce rozšíření SQL pro práci s dočasná data. To jsme si ukázali, jak propojit datových proudů, jak rozšířit datový proud s statická referenční data a jak horizontálně navyšte kapacitu dotazu, abyste dosáhli vyšší propustnosti.

I když toto řešení poskytuje vhodným úvodem, není kompletní jakýmikoli prostředky. Můžete najít další vzory dotazů pomocí jazyka SAQL na [příklady pro běžné vzory využití Stream Analytics dotazů](stream-analytics-stream-analytics-query-patterns.md).
