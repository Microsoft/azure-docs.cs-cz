---
title: LightIngest je nástroj příkazového řádku pro ingestování do Průzkumníka dat Azure.
description: Další informace o LightIngest, nástroj příkazového řádku pro ad-hoc data ingestion do Průzkumníka dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 95d943685cf511acb88f9e48d36a9dd43b0a27d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548006"
---
# <a name="install-and-use-lightingest"></a>Instalace a použití LightIngest

LightIngest je nástroj příkazového řádku pro ad-hoc data ingestion do Průzkumníka dat Azure.
Nástroj můžete vyžádat zdrojová data z místní složky nebo z kontejneru úložiště objektů blob Azure.

## <a name="prerequisites"></a>Požadavky

* LightIngest – stáhněte si ji jako součást [balíčku Microsoft.Azure.Kusto.Tools NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)

    ![Nejsvítivější stažení](media/lightingest/lightingest-download-area.png)

* WinRAR - stáhnout z [www.win-rar.com/download.html](http://www.win-rar.com/download.html)

## <a name="install-lightingest"></a>Instalace LightIngest

1. Přejděte do umístění v počítači, kde jste stáhli LightIngest. 
1. Pomocí WinRAR, extrahovat *nástroje* adresář e-počítače.

## <a name="run-lightingest"></a>Spustit LightIngest

1. Přejděte do extrahovaného *adresáře nástrojů* v počítači.
1. Odstraňte existující informace o poloze z panelu umístění.
    
      ![Odstranit informace o poloze](media/lightingest/lightingest-location-bar.png)

1. Zadejte `cmd` a stiskněte **Enter**.
1. Na příkazovém `LightIngest.exe` řádku zadejte následovaný příslušným argumentem příkazového řádku.

    > [!Tip]
    > Seznam podporovaných argumentů příkazového řádku `LightIngest.exe /help`zadejte .
    >
    >![Nápověda k příkazovému řádku](media/lightingest/lightingest-cmd-line-help.png)

1. Zadejte `ingest-` následovaný připojovací řetězec do clusteru Azure Data Explorer, který bude spravovat ingestování.
    Připojovací řetězec uzavřete do dvojitých uvozovek a postupujte podle [specifikace připojovacích řetězců Kusto](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto).

    Například:
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* Doporučená metoda je pro LightIngest pracovat s cílovým `https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`parametrem požití na . Tímto způsobem služba Azure Data Explorer můžete spravovat zatížení přijím a můžete snadno obnovit z přechodných chyb. Můžete však také nakonfigurovat LightIngest pro práci`https://{yourClusterNameAndRegion}.kusto.windows.net`přímo s koncovým bodem motoru ( ).

> [!Note]
> Pokud ingestujete přímo s koncovým bodem motoru, nemusíte zahrnout `ingest-`, ale nebude existovat funkce DM k ochraně motoru a zlepšení úspěšnosti při ingestování.

* Pro optimální výkon při ingestování je důležité, aby LightIngest znal nezpracovaná velikost dat, a tak LightIngest odhadne nekomprimovanou velikost místních souborů. LightIngest však nemusí být schopen správně odhadnout hrubou velikost komprimovaných objektů BLOB bez jejich prvního stažení. Proto při ingestování komprimovaných objektů `rawSizeBytes` BLOB nastavte vlastnost metadat objektů blob na nekomprimovanou velikost dat v bajtech.

## <a name="general-command-line-arguments"></a>Obecné argumenty příkazového řádku

|Název argumentu         |Krátký název   |Typ    |Povinné |Popis                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |řetězec  |Povinné |[Připojovací řetězec Průzkumníka dat Azure](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto) určující koncový bod Kusto, který bude zpracovávat ingestování. By měla být uzavřena v uvozovkách |
|-databáze             |-db          |řetězec  |Nepovinné  |Cílová název databáze Průzkumníka dat Azure |
|-tabulka                |             |řetězec  |Povinné |Název tabulky cílového Průzkumníka dat Azure |
|-sourcePath           |-zdroj      |řetězec  |Povinné |Cesta ke zdrojovým souborům nebo kořenový identifikátor URI kontejneru objektů blob. Pokud jsou data v objektech BLOB, musí obsahovat klíč účtu úložiště nebo SAS. Doporučujeme uzavřete do dvojitých uvozovek |
|-prefix               |             |řetězec  |Nepovinné  |Když se zdrojová data ingestuje v úložišti objektů blob, tato předpona adresy URL je sdílena všemi objekty BLOB, s výjimkou názvu kontejneru. <br>Pokud jsou například data `MyContainer/Dir1/Dir2`v , měla `Dir1/Dir2`by být předpona . Doporučujese ohraničující v uvozovkách |
|-vzor              |             |řetězec  |Nepovinné  |Vzorek, podle kterého jsou vyskladněny zdrojové soubory/objekty BLOB. Podporuje zástupné znaky. Například, `"*.csv"`. Doporučujeme uzavřete do dvojitých uvozovek |
|-zipPattern           |             |řetězec  |Nepovinné  |Regulární výraz, který se má použít při výběru souborů v archivu ZIP k ingestování.<br>Všechny ostatní soubory v archivu budou ignorovány. Například `"*.csv"`. Doporučuje se ji obklopit v uvozovkách |
|-formát               |-f           |řetězec  |Nepovinné  |Formát zdrojových dat. Musí být jeden z [podporovaných formátů.](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) |
|-ingestionMappingPath |-mappingPath |řetězec  |Nepovinné  |Cesta k souboru mapování sloupců ingestování (povinné pro formáty Json a Avro). Zobrazit [mapování dat](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-ingestionMappingRef  |-mappingRef  |řetězec  |Nepovinné  |Název předem vytvořeného mapování sloupců ingestování (povinné pro formáty Json a Avro). Zobrazit [mapování dat](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-creationTimePattern  |             |řetězec  |Nepovinné  |Když je nastavena, slouží k extrahování CreationTime vlastnost ze souboru nebo cesty objektu blob. Viz [Using CreationTimePattern argument](#using-creationtimepattern-argument) |
|-ignoreFirstRow       |-ignoreFirst |bool    |Nepovinné  |Pokud je nastavena, první záznam každého souboru/objektu blob je ignorován (například pokud zdrojová data mají záhlaví) |
|-značka                  |             |řetězec  |Nepovinné  |[Značky,](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) které chcete přidružit k ingemovaným datům. Více násobných výskytů je povoleno |
|-dontWait             |             |bool    |Nepovinné  |Pokud je nastavena na 'true', nečeká na dokončení požití. Užitečné při ingestování velkého množství souborů/objektů BLOB |

### <a name="using-creationtimepattern-argument"></a>Použití argumentu CreationTimePattern

Argument `-creationTimePattern` extrahuje Vlastnost CreationTime ze souboru nebo cesty k blob. Vzorek nemusí odrážet celou cestu k položce, pouze oddíl, který obklopuje časové razítko, které chcete použít.

Hodnoty argumentů musí obsahovat:
* Konstantní test bezprostředně předcházející časovému razítku, uzavřený v jednoduchých uvozovkách
* Formát časového razítka ve [standardním zápisu DateTime .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* Konstantní text bezprostředně za časovým razítkem. Pokud například názvy objektů `historicalvalues19840101.parquet` blob končí (časové razítko je čtyři číslice pro rok, dvě číslice pro měsíc a `-creationTimePattern` dvě číslice pro den v měsíci), odpovídající hodnota argumentu je:

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>Argumenty příkazového řádku pro pokročilé scénáře

|Název argumentu         |Krátký název   |Typ    |Povinné |Popis                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-komprese          |-cr          |double  |Nepovinné  |Nápověda k kompresnímu poměru. Užitečné při ingestování komprimovaných souborů nebo objektů BLOB, které vám pomohou azure data Explorer posoudit velikost nezpracovaných dat. Vypočteno jako původní velikost dělená komprimovolouženou velikostí |
|-limit                |-l           |celé číslo |Nepovinné  |Pokud je nastaveno, omezí požití na první N soubory |
|-listOnly             |-seznam        |bool    |Nepovinné  |Pokud je nastaveno, zobrazí pouze položky, které by byly vybrány pro ingestování.| 
|-ingestTimeout        |             |celé číslo |Nepovinné  |Časový čas v minutách pro dokončení všech operací ingestování. Výchozí nastavení`60`|
|-forceSync            |             |bool    |Nepovinné  |Pokud je nastaveno, vynutí synchronní ingestování. Výchozí nastavení`false` |
|-dataBatchSize        |             |celé číslo |Nepovinné  |Nastaví limit celkové velikosti (MB, nekomprimovaný) každé operace ingestování. |
|-filesInBatch         |             |celé číslo |Nepovinné  |Nastaví limit počtu souborů/objektů blob pro každou operaci ingestování. |
|-devTrasování           |-stopa       |řetězec  |Nepovinné  |Pokud je nastaveno, diagnostické protokoly jsou zapsány do místního adresáře (ve výchozím nastavení, `RollingLogs` v aktuálním adresáři nebo mohou být změněny nastavením hodnoty přepínače) |

## <a name="blob-metadata-properties"></a>Vlastnosti metadat objektu Blob
Při použití s objekty BLOB Azure lightingest použije určité vlastnosti metadat objektu blob k rozšíření procesu ingestování.

|Metadata, vlastnost                            | Využití                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | Pokud je nastavena, bude interpretována jako velikost nekomprimovaných dat.                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | Interpretováno jako časové razítko UTC. Pokud je nastavena, bude použita k přepsání času vytvoření v Kusto. Užitečné pro scénáře zasypávání |

## <a name="usage-examples"></a>Příklady použití

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>Ingestování objektů BLOB pomocí klíče účtu úložiště nebo tokenu SAS

* Ingestovat 10 objektů BLOB `ACCOUNT`pod `DIR`zadaným `CONT`účtem úložiště , ve složce , v kontejneru a odpovídající vzoru`*.csv.gz`
* Cíl je `DB`databáze `TABLE`, tabulka a mapování `MAPPING` ingestování je předem vytvořeno v cílovém cíli.
* Nástroj počká, dokud nebudou dokončeny operace ingestování.
* Všimněte si různých možností pro určení cílové databáze a klíče účtu úložiště vs. token SAS

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>Ingestování všech objektů BLOB v kontejneru, včetně řádků záhlaví

* Ingestovat všechny objekty BLOB pod zadaným účtem `ACCOUNT`úložiště , ve složce `DIR1/DIR2`, v kontejneru `CONT`a odpovídající vzoru`*.csv.gz`
* Cíl je `DB`databáze `TABLE`, tabulka a mapování `MAPPING` ingestování je předem vytvořeno v cílovém cíli.
* Zdrojové objekty BLOB obsahují řádek záhlaví, takže nástroj má pokyn k přetažení prvního záznamu každého objektu blob
* Nástroj zaúčtuje data pro požití a nebude čekat na dokončení ingestování operací

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>Ingestování všech souborů JSON z cesty

* Ingestovat všechny `PATH`soubory pod cestou , odpovídající vzorek`*.json`
* Cíl je `DB`databáze `TABLE`, tabulka a mapování ingestování je definováno v místním souboru.`MAPPING_FILE_PATH`
* Nástroj zaúčtuje data pro požití a nebude čekat na dokončení ingestování operací

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>Ingestování souborů a zápis diagnostických trasovacích souborů

* Ingestovat všechny `PATH`soubory pod cestou , odpovídající vzorek`*.json`
* Cíl je `DB`databáze `TABLE`, tabulka a mapování ingestování je definováno v místním souboru.`MAPPING_FILE_PATH`
* Nástroj zaúčtuje data pro požití a nebude čekat na dokončení ingestování operací
* Diagnostické trasovací soubory budou zapsány místně do složky`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>Changelog
|Version        |Změny                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>Přidaný `-zipPattern` argument</li><li>Přidaný `-listOnly` argument</li><li>Před zahájením spuštění se zobrazí souhrn argumentů.</li><li>CreationTime se čte z vlastností metadat objektu blob nebo `-creationTimePattern` z objektu blob nebo názvu souboru podle argumentu</li></ul>|
