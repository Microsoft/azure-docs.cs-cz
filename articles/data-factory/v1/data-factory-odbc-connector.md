---
title: Přesun dat z úložiště dat rozhraní ODBC | Microsoft Docs
description: Další informace o tom, jak přesun dat z úložiště dat rozhraní ODBC pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4db720b2140a5208acc582b01b0b9613a7fd827a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621373"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Přesun dat z rozhraní ODBC datová úložiště pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](data-factory-odbc-connector.md)
> * [Verze 2 – Preview](../connector-odbc.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [ODBC konektoru V2](../connector-odbc.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z úložiště dat rozhraní ODBC místně. Vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který představuje obecný přehled přesun dat s aktivitou kopírování.

Můžete zkopírovat data z úložiště dat rozhraní ODBC do úložiště dat žádné podporované jímky. Seznam úložišť dat jako jímky nepodporuje aktivitě kopírování najdete v tématu [podporovanými úložišti dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tabulky. Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z úložiště dat rozhraní ODBC do jiným úložištím dat, ale ne pro přesun dat z jiných úložišť dat k úložišti dat ODBC. 

## <a name="enabling-connectivity"></a>Povolení připojení
Služba data Factory podporuje připojení k místní zdroje ODBC pomocí Brána pro správu dat. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány. Použijte bránu pro připojení k úložišti dat rozhraní ODBC i v případě, že je hostovaná ve virtuálním počítači Azure IaaS.

Bránu můžete nainstalovat na stejný místní počítač nebo virtuální počítač Azure jako úložiště dat ODBC. Nicméně doporučujeme nainstalovat bránu na samostatný počítač nebo Azure IaaS virtuální počítač, abyste předešli sporu prostředků a pro dosažení vyššího výkonu. Při instalaci brány na samostatný počítač, na počítač byste měli mít přístup k počítači s úložištěm dat ODBC.

Kromě Brána pro správu dat budete také muset nainstalovat ovladač ODBC pro úložiště dat na počítači s bránou.

> [!NOTE]
> V tématu [potíží brány](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) tipy k řešení potíží s připojení nebo brány související s problémy.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z úložiště dat rozhraní ODBC pomocí různých nástrojů nebo rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru**, **.NET API**, a **REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. 

Jestli používáte nástroje nebo rozhraní API, je třeba provést následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat podřízený: 

1. Vytvoření **propojené služby** propojení vstupní a výstupní data ukládá do data factory.
2. Vytvoření **datové sady** představují vstupní a výstupní data pro kopírování. 
3. Vytvoření **kanálu** s aktivitou kopírování, která přebírá datovou sadu jako vstup a datovou sadu jako výstup. 

Když použijete průvodce, jsou automaticky vytvoří definice JSON pro tyto entity služby Data Factory (propojené služby, datové sady a kanál). Při použití nástroje nebo rozhraní API (s výjimkou .NET API), definujete tyto entity služby Data Factory pomocí formátu JSON.  Příklad s definicemi JSON entit služby Data Factory, které se používají ke zkopírování dat z úložiště dat rozhraní ODBC, naleznete v tématu [JSON příklad: kopírování dat z dat ODBC uložit do objektu Blob Azure](#json-example-copy-data-from-odbc-data-store-to-azure-blob) tohoto článku. 

Následující části obsahují podrobnosti o vlastnostech formátu JSON, které slouží k určení konkrétní entity služby Data Factory k úložišti dat rozhraní ODBC:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro ODBC propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type |Vlastnost typu musí být nastavena na: **OnPremisesOdbc** |Ano |
| připojovací řetězec |Přístup k pověření část připojovací řetězec a volitelné šifrovat přihlašovací údaje. Příklady v následujících částech. <br/><br/>Můžete zadat připojovací řetězec pomocí vzoru jako `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, nebo pomocí systému DSN (název zdroje dat), nastavení na počítači s bránou s `"DSN=<name of the DSN>;"` (třeba stále zadáte části přihlašovací údaje v propojené službě odpovídajícím způsobem). |Ano |
| pověření |Část přístup přihlašovacích údajů z připojovacího řetězce zadaného ve formátu ovladačem vlastnost hodnota. Příklad: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Ne |
| authenticationType. |Typ ověřování používaný pro připojení k úložišti dat ODBC. Možné hodnoty jsou: anonymní a Basic. |Ano |
| uživatelské jméno |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou služba Data Factory měla použít pro připojení k úložišti dat ODBC. |Ano |

### <a name="using-basic-authentication"></a>Použití základního ověřování

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Základní ověřování pomocí zašifrované přihlašovací údaje
Můžete šifrovat přihlašovací údaje pomocí [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) rutiny (1.0 verzi prostředí Azure PowerShell) nebo [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx) (0.9 nebo starší verzi prostředí Azure PowerShell).  

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Pomocí anonymní ověřování

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```


## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **RelationalTable** (která zahrnuje datovou sadu ODBC) má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| tableName |Název tabulky v úložišti dat ODBC. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v **rámci typeProperties** části aktivity na druhé straně lišit každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

Při aktivitě kopírování, pokud je zdroj typu **RelationalSource** (která zahrnuje rozhraní ODBC), v rámci typeProperties části jsou k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Požaduje se |
| --- | --- | --- | --- |
| query |Čtení dat pomocí vlastního dotazu. |Řetězec dotazu SQL. Příklad: vybrat * z MyTable. |Ano |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Příklad JSON: kopírování dat z dat ODBC uložit do objektu Blob Azure
Tento příklad obsahuje definice JSON, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak zkopírovat data ze zdroje ODBC do Azure Blob Storage. Však lze zkopírovat data do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující entity objektu pro vytváření dat:

1. Propojené služby typu [OnPremisesOdbc](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [RelationalTable](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z výsledku dotazu v úložišti dat rozhraní ODBC do objektu blob každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

Jako první krok nastavte Brána pro správu dat. Tyto pokyny jsou v [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku.

**ODBC propojená služba** tento příklad používá základní ověřování. V tématu [ODBC propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Propojená služba Azure Storage**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
    }
}
```

**Vstupní datové sady rozhraní ODBC**

Příkladu se předpokládá, jste vytvořili tabulku "MyTable" v databázi ODBC a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "PRAVDA" informuje služba Data Factory, datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Výstupní datová sada Azure Blob**

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1). Cesta ke složce pro tento objekt blob je vyhodnocován dynamicky podle času zahájení řezu, které jsou zpracovávány. Cesta ke složce používá rok, měsíc, den a čas částí čas spuštění.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


**Aktivita kopírování v kanálu s zdroje ODBC (RelationalSource) a podřízený objekt Blob (BlobSink)**

Kanál obsahuje aktivitu kopírování, která je konfigurovaná pro používání těchto vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **RelationalSource** a **podřízený** je typ nastaven na **BlobSink**. Zadané pro dotaz SQL **dotazu** vlastnost vybere data za poslední hodinu pro kopírování.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Mapování typu pro rozhraní ODBC
Jak je uvedeno v [aktivity přesunu dat](data-factory-data-movement-activities.md) článku aktivita kopírování provádí automatické typ převody z typů zdroje do jímky typů s následující postup ve dvou krocích:

1. Převést na typ .NET typy nativní zdrojů
2. Převést na typ jímky nativní typ formátu .NET

Při přesouvání dat z úložiště dat rozhraní ODBC, typy dat rozhraní ODBC jsou namapované na typy .NET, jak je uvedeno v [mapování datového typu ODBC](https://msdn.microsoft.com/library/cc668763.aspx) tématu.

## <a name="map-source-to-sink-columns"></a>Mapování zdroje jímky sloupců
Další informace o mapování sloupců v datové sadě zdrojového sloupce v datové sadě podřízený najdete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit. V tématu [Repeatable číst z relačními zdroji](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="ge-historian-store"></a>GE Historian úložiště
Vytvoření služby ODBC propojené propojení [GE Proficy Historian (teď GE Historian)](http://www.geautomation.com/products/proficy-historian) úložiště dat do služby Azure data factory, jak je znázorněno v následujícím příkladu:

```json
{
    "name": "HistorianLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "connectionString": "DSN=<name of the GE Historian store>;",
            "gatewayName": "<gateway name>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": "<password>"
        }
    }
}
```

Nainstalujte Brána pro správu dat na místním počítači a zaregistrujte bránu pomocí portálu. Brány nainstalované na místním počítači používá k připojení k úložišti dat GE Historian ovladač ODBC pro GE Historian. Nainstalujte ovladač, proto, pokud ještě není nainstalovaná na počítači s bránou. V tématu [povolení připojení](#enabling-connectivity) podrobnosti.

Než použijete úložišti GE Historian v řešení pro vytváření dat, ověřte, zda brána se může připojit k úložišti dat pomocí pokynů v další části.

Čtení článku od začátku podrobnější přehled použití dat ODBC ukládá jako zdrojové úložiště dat v operaci kopírování.  

## <a name="troubleshoot-connectivity-issues"></a>Řešení potíží s problémy s připojením
K odstraňování problémů s připojením, použijte **diagnostiky** kartě **Správce konfigurace brány pro správu dat**.

1. Spusťte **Správce konfigurace brány pro správu dat**. Buď můžete spustit "C:\Program Files\Microsoft Data správy Gateway\1.0\Shared\ConfigManager.exe" přímo (nebo) vyhledávání pro **brány** najít odkaz na **Brána pro správu dat** aplikace, jak je znázorněno na následujícím obrázku.

    ![Hledání brány](./media/data-factory-odbc-connector/search-gateway.png)
2. Přepnout **diagnostiky** kartě.

    ![Diagnostiku brány](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Vyberte **typ** dat uložit (propojené služby).
4. Zadejte **ověřování** a zadejte **pověření** (nebo) zadejte **připojovací řetězec** používané pro připojení k úložišti.
5. Klikněte na tlačítko **testovací připojení** k testování připojení k úložišti.

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
