---
title: Odolnost aktivity kopírování ve službě Azure Data Factory proti chybám
description: Informace o tom, jak přidat odolnost proti chybám pro aktivitu kopírování v Azure Data Factory vynecháním nekompatibilních dat.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390959"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Odolnost aktivity kopírování ve službě Azure Data Factory proti chybám
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Aktuální verze](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Když kopírujete data ze zdrojového do cílového úložiště, Azure Data Factory aktivita kopírování poskytuje určitou úroveň odolnosti proti chybám, aby nedocházelo k výpadkům v průběhu přesunu dat. Například kopírujete miliony řádků ze zdrojového do cílového úložiště, kde byl v cílové databázi vytvořen primární klíč, ale zdrojová databáze neobsahuje žádné definované primární klíče. Když narazíte na kopírování duplicitních řádků ze zdroje do cíle, dojde k selhání porušení PK v cílové databázi. V tuto chvíli vám aktivita kopírování nabízí dva způsoby, jak tyto chyby zpracovat: 
- Jakmile dojde k chybě, můžete aktivitu kopírování přerušit. 
- Pokračováním můžete zkopírovat zbývající data povolením odolnosti proti chybám, aby se přeskočila nekompatibilní data. V tomto případě můžete například přeskočit duplicitní řádek. Vynecháváná data můžete také zaprotokolovat povolením protokolu relace v rámci aktivity kopírování. Další podrobnosti najdete v tématu věnovaném [protokolu relace v aktivitě kopírování](copy-activity-log.md) .

## <a name="copying-binary-files"></a>Kopírování binárních souborů 

ADF podporuje při kopírování binárních souborů následující scénáře odolnosti proti chybám. Můžete zvolit, že se má aktivita kopírování přerušit, nebo pokračovat ve kopírování zbývajících v následujících scénářích:

1. Soubory, které mají být kopírovány pomocí ADF, jsou odstraňovány jinými aplikacemi současně.
2. Některé konkrétní složky nebo soubory nedovolují přístup k ADF, protože seznamy ACL těchto souborů nebo složek vyžadují vyšší úroveň oprávnění než informace o připojení nakonfigurované v ADF.
3. Pokud povolíte nastavení ověřování konzistence dat v podavači ADF, jeden nebo více souborů se neověří tak, aby byly konzistentní mezi zdrojovým a cílovým úložištěm.

### <a name="configuration"></a>Konfigurace 
Když kopírujete binární soubory mezi úložišti úložiště, můžete zapnout odolnost proti chybám následujícím způsobem: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | -------- 
skipErrorFile | Skupina vlastností pro určení typů selhání, které chcete během přesunu dat přeskočit. | | No
Chybí chybějící | Jedna z párů klíč-hodnota v kontejneru objektů a dat skipErrorFile slouží k určení, jestli chcete přeskočit soubory, které při každém kopírování ADF odstraňují jiné aplikace. <br/> -True: chcete zkopírovat zbývající soubory přeskočením souborů odstraněných jinými aplikacemi. <br/> -False: chcete přerušit aktivitu kopírování, jakmile se odstraní nějaké soubory ze zdrojového úložiště v průběhu přesunu dat. <br/>Upozorňujeme, že tato vlastnost je jako výchozí nastavená na true. | True (výchozí) <br/>Ne | No
Zakázaný | Jedna z párů klíč-hodnota v kontejneru objektů a dat skipErrorFile k určení, jestli chcete přeskočit konkrétní soubory, když seznamy ACL těchto souborů nebo složek vyžadují vyšší úroveň oprávnění než připojení nakonfigurované v podavači ADF. <br/> -True: chcete zkopírovat zbývající soubory přeskočením souborů. <br/> -False: chcete přerušit aktivitu kopírování, když se dostanete k potížím s oprávněním pro složky nebo soubory. | Ano <br/>False (výchozí) | No
dataInconsistency | Jedna z párů klíč-hodnota v kontejneru vlastností skipErrorFile k určení, jestli chcete přeskočit nekonzistentní data mezi zdrojovým a cílovým úložištěm. <br/> -True: chcete zkopírovat zbývající data vynecháním nekonzistentních dat. <br/> -False: chcete přerušit aktivitu kopírování po nalezení nekonzistentních dat. <br/>Počítejte s tím, že tato vlastnost je platná, pouze pokud nastavíte validateDataConsistency jako true. | Ano <br/>False (výchozí) | No
invalidFileName | Jedna z párů klíč-hodnota v kontejneru vlastností skipErrorFile k určení, jestli chcete přeskočit konkrétní soubory, pokud jsou názvy souborů pro cílové úložiště neplatné. <br/> -True: chcete zkopírovat zbytek vynecháním souborů, které mají neplatné názvy souborů. <br/> -False: chcete přerušit aktivitu kopírování, protože některé soubory mají neplatné názvy souborů. <br/>Upozorňujeme, že tato vlastnost funguje při kopírování binárních souborů ze všech úložišť úložiště do ADLS Gen2 nebo kopírování binárních souborů z AWS S3 do libovolného úložiště úložiště. | Ano <br/>False (výchozí) | No
logSettings  | Skupina vlastností, které lze zadat, pokud chcete protokolovat vynechané názvy objektů. | &nbsp; | No
linkedServiceName | Propojená služba [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) ukládat soubory protokolu relace. | Názvy `AzureBlobStorage` `AzureBlobFS` propojených služeb typu nebo, které odkazují na instanci, kterou použijete k uložení souboru protokolu. | No
program | Cesta souborů protokolu. | Zadejte cestu, kterou použijete k uložení souborů protokolu. Pokud cestu nezadáte, služba vytvoří kontejner. | No

> [!NOTE]
> Níže jsou uvedené předpoklady pro povolení odolnosti proti chybám v aktivitě kopírování při kopírování binárních souborů.
> Pro přeskočení určitých souborů při jejich odstraňování ze zdrojového úložiště:
> - Zdrojová datová sada a datová sada jímky musí být binární formát a nelze zadat typ komprese. 
> - Podporované typy úložiště dat jsou služby Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, systém souborů, FTP, SFTP, Amazon S3, Google Cloud Storage a HDFS.
> - Pouze pokud zadáte více souborů ve zdrojové datové sadě, což může být složka, zástupný znak nebo seznam souborů, aktivita kopírování může přeskočit konkrétní soubory chyb. Pokud je ve zdrojové datové sadě zadaný jeden soubor, který se má zkopírovat do cíle, aktivita kopírování selže, pokud dojde k nějaké chybě.
>
> Pro přeskočení určitých souborů, když je jejich přístup zakázán ze zdrojového úložiště:
> - Zdrojová datová sada a datová sada jímky musí být binární formát a nelze zadat typ komprese. 
> - Podporované typy úložiště dat jsou služby Azure Blob Storage, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure File Storage, SFTP, Amazon S3 a HDFS.
> - Pouze pokud zadáte více souborů ve zdrojové datové sadě, což může být složka, zástupný znak nebo seznam souborů, aktivita kopírování může přeskočit konkrétní soubory chyb. Pokud je ve zdrojové datové sadě zadaný jeden soubor, který se má zkopírovat do cíle, aktivita kopírování selže, pokud dojde k nějaké chybě.
>
> Pro přeskočení konkrétních souborů v případě, že jsou ověřeny jako nekonzistentní mezi zdrojovým a cílovým úložištěm:
> - Další podrobnosti z dokumentu konzistence dat najdete [tady](./copy-activity-data-consistency.md).

### <a name="monitoring"></a>Monitorování 

#### <a name="output-from-copy-activity"></a>Výstup aktivity kopírování
Počet souborů, které jsou čteny, zapisovány a přeskočeny, můžete získat pomocí výstupu jednotlivých spuštění aktivit kopírování. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Protokol relace z aktivity kopírování

Pokud nakonfigurujete protokolování na vynechané názvy souborů, můžete najít soubor protokolu z této cesty: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Soubory protokolu musí být soubory CSV. Schéma souboru protokolu je následující:

Sloupec | Popis 
-------- | -----------  
Timestamp | Časové razítko, když ADF přeskočí soubor.
Level | Úroveň protokolu této položky. U položky, která zobrazuje přeskočení souboru, bude na úrovni upozornění.
OperationName | Provozní chování aktivity kopírování ADF u každého souboru Pokud chcete určit soubor, který se má přeskočit, bude přeskočena.
OperationItem | Názvy souborů, které se mají přeskočit.
Zpráva | Další informace, které znázorňují, proč byl soubor vynechán.

Příklad souboru protokolu je následující: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
Z výše uvedeného protokolu vidíte, že bigfile.csv bylo přeskočeno z důvodu jiné aplikace, kterou tento soubor odstranil při kopírování ADF. A 3_nopermission.txt se přeskočilo, protože ADF nemá povolený přístup k němu kvůli problému s oprávněním.


## <a name="copying-tabular-data"></a>Kopírování tabulkových dat 

### <a name="supported-scenarios"></a>Podporované scénáře
Aktivita kopírování podporuje tři scénáře pro zjišťování, přeskočení a protokolování nekompatibilních tabulkových dat:

- **Nekompatibilita mezi zdrojovým datovým typem a nativním typem jímky**. 

    Například: Zkopírujte data ze souboru CSV v úložišti objektů blob do databáze SQL s definicí schématu, která obsahuje tři sloupce typu INT. Řádky souboru CSV, které obsahují číselná data, například 123 456 789, se úspěšně zkopírují do úložiště jímky. Řádky, které obsahují jiné než číselné hodnoty, jako například 123 456, ABC jsou však zjištěny jako nekompatibilní a jsou vynechány.

- **Neshoda v počtu sloupců mezi zdrojem a jímkou**.

    Například: Zkopírujte data ze souboru CSV v úložišti objektů blob do databáze SQL s definicí schématu, která obsahuje šest sloupců. Řádky souboru CSV, které obsahují šest sloupců, se úspěšně zkopírují do úložiště jímky. Řádky souboru CSV, které obsahují více než šest sloupců, se zjišťují jako nekompatibilní a přeskočí se.

- **Při zápisu do SQL Server/Azure SQL Database/Azure Cosmos DB došlo k porušení primárního klíče**.

    Příklad: kopírování dat z SQL serveru do databáze SQL. Primární klíč je definovaný v databázi SQL jímky, ale ve zdrojovém SQL serveru není definovaný žádný takový primární klíč. Duplicitní řádky, které existují ve zdroji, nelze zkopírovat do jímky. Aktivita kopírování kopíruje do jímky pouze první řádek zdrojových dat. Následné zdrojové řádky, které obsahují duplicitní hodnotu primárního klíče, jsou zjištěny jako nekompatibilní a jsou vynechány.

>[!NOTE]
>- Pokud chcete načíst data do Azure synapse Analytics pomocí základu, nakonfigurujte nativní nastavení odolnosti proti chybám, a to tak, že v aktivitě kopírování zadáte odmítnout zásady prostřednictvím "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)". Přesto můžete povolit přesměrování základních nekompatibilních řádků do objektů BLOB nebo ADLS jako normální, jak je znázorněno níže.
>- Tato funkce se nepoužije, když je aktivita kopírování nakonfigurovaná tak, aby vyvolala službu [Amazon RedShift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).
>- Tato funkce se nepoužije, když je aktivita kopírování nakonfigurovaná tak, aby vyvolala [uloženou proceduru z jímky SQL](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink).

### <a name="configuration"></a>Konfigurace
Následující příklad poskytuje definici JSON pro konfiguraci přeskočení nekompatibilních řádků v aktivitě kopírování:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Určuje, zda se během kopírování mají přeskočit nekompatibilní řádky. | Ano<br/>False (výchozí) | No
logSettings | Skupina vlastností, které lze zadat, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | No
linkedServiceName | Propojená služba [Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) ukládat protokol, který obsahuje vynechané řádky. | Názvy `AzureBlobStorage` `AzureBlobFS` propojených služeb typu nebo, které odkazují na instanci, kterou použijete k uložení souboru protokolu. | No
program | Cesta souborů protokolu, které obsahují vynechané řádky. | Zadejte cestu, kterou chcete použít k protokolování nekompatibilních dat. Pokud cestu nezadáte, služba vytvoří kontejner. | No

### <a name="monitor-skipped-rows"></a>Sledování vynechaných řádků
Po dokončení aktivity kopírování můžete zobrazit počet vynechaných řádků ve výstupu aktivity kopírování:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

Pokud nakonfigurujete, aby protokoloval nekompatibilní řádky, můžete najít soubor protokolu z této cesty: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Soubory protokolu budou soubory CSV. Schéma souboru protokolu je následující:

Sloupec | Popis 
-------- | -----------  
Timestamp | Časové razítko, když ADF přeskočí nekompatibilní řádky
Level | Úroveň protokolu této položky. Pokud tato položka zobrazuje vynechané řádky, bude se zobrazovat na úrovni upozornění.
OperationName | Provozní chování aktivity kopírování ADF na každém řádku Bude se jednat o "TabularRowSkip", aby bylo možné určit, že konkrétní nekompatibilní řádek byl vynechán
OperationItem | Vynechané řádky ze zdrojového úložiště dat.
Zpráva | Další informace, které znázorňují, proč nekompatibilita tohoto konkrétního řádku


Příkladem obsahu souboru protokolu je následující:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

Z výše uvedeného souboru protokolu můžete zobrazit jeden řádek "DATA1, Data2, DATA3", který se přeskočí z důvodu problému s převodem typu ze zdroje do cílového úložiště. Z důvodu problému s porušením PK ze zdroje do cílového úložiště se přeskočil jiný řádek DATA4, DATA5, data6. 


## <a name="copying-tabular-data-legacy"></a>Kopírování tabulkových dat (starší verze):

Následuje starší způsob, jak povolit odolnost proti chybám pouze při kopírování tabulkových dat. Pokud vytváříte nový kanál nebo aktivitu, doporučujeme [místo toho začít](#copying-tabular-data) .

### <a name="configuration"></a>Konfigurace
Následující příklad poskytuje definici JSON pro konfiguraci přeskočení nekompatibilních řádků v aktivitě kopírování:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | Určuje, zda se během kopírování mají přeskočit nekompatibilní řádky. | Ano<br/>False (výchozí) | No
redirectIncompatibleRowSettings | Skupina vlastností, které lze zadat, pokud chcete protokolovat nekompatibilní řádky. | &nbsp; | No
linkedServiceName | Propojená služba [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) nebo [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) k uložení protokolu, který obsahuje vynechané řádky. | Názvy `AzureStorage` `AzureDataLakeStore` propojených služeb typu nebo, které odkazují na instanci, kterou chcete použít k uložení souboru protokolu. | No
program | Cesta k souboru protokolu, který obsahuje vynechané řádky. | Zadejte cestu, kterou chcete použít k protokolování nekompatibilních dat. Pokud cestu nezadáte, služba vytvoří kontejner. | No

### <a name="monitor-skipped-rows"></a>Sledování vynechaných řádků
Po dokončení aktivity kopírování můžete zobrazit počet vynechaných řádků ve výstupu aktivity kopírování:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
Pokud nakonfigurujete, aby protokoloval nekompatibilní řádky, můžete najít soubor protokolu v této cestě: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Soubory protokolu můžou být jenom soubory CSV. Původní data, která se přeskočí, budou v případě potřeby protokolována čárkou jako oddělovač sloupců. Do původního zdrojového data v souboru protokolu přidáme další dva sloupce "ErrorCode" a "ErrorMessage", kde vidíte hlavní příčinu nekompatibility. Kód chyby a ErrorMessage se bude nabízet pomocí dvojitých uvozovek. 

Příkladem obsahu souboru protokolu je následující:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Další kroky
Další články o aktivitě kopírování najdete v článcích:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)