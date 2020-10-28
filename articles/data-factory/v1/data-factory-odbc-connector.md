---
title: Přesun dat z úložišť dat rozhraní ODBC
description: Přečtěte si, jak přesunout data z úložišť dat ODBC pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3c68b1f4d76a1899ce473c57f3a6d5de1eab71c6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636863"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Přesun dat z úložišť dat ODBC pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-odbc-connector.md)
> * [Verze 2 (aktuální verze)](../connector-odbc.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [konektor ODBC ve verzi v2](../connector-odbc.md).


Tento článek vysvětluje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místního úložiště dat ODBC. Sestavuje se podle článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , který prezentuje obecný přehled přesunu dat s aktivitou kopírování.

Data z úložiště dat ODBC můžete kopírovat do libovolného podporovaného úložiště dat jímky. Seznam úložišť dat, která aktivita kopírování podporuje jako jímky, najdete v tabulce [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Data Factory aktuálně podporuje pouze přesouvání dat z úložiště dat ODBC do jiných úložišť dat, ale ne pro přesun dat z jiných úložišť dat do úložiště dat ODBC.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Povolení připojení
Služba Data Factory podporuje připojení k místním zdrojům rozhraní ODBC pomocí brány Správa dat. Další informace o Správa dat bráně a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a v cloudovém](data-factory-move-data-between-onprem-and-cloud.md) článku. Bránu můžete použít pro připojení k úložišti dat ODBC i v případě, že je hostovaný na virtuálním počítači Azure s IaaS.

Bránu můžete nainstalovat na stejný místní počítač nebo virtuální počítač Azure jako úložiště dat ODBC. Doporučujeme ale nainstalovat bránu na samostatném virtuálním počítači s IaaS nebo Azure, abyste se vyhnuli kolizím prostředků a dosažení lepšího výkonu. Když bránu nainstalujete do samostatného počítače, počítač by měl mít přístup k počítači s úložištěm dat ODBC.

Kromě Správa dat brány je také potřeba nainstalovat ovladač ODBC pro úložiště dat na počítači brány.

> [!NOTE]
> Tipy k odstraňování potíží souvisejících s připojením nebo bránou najdete v tématu řešení potíží s [bránou](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Začínáme
Můžete vytvořit kanál s aktivitou kopírování, která přesouvá data z úložiště dat ODBC pomocí různých nástrojů/rozhraní API.

Nejjednodušší způsob, jak vytvořit kanál, je použít **Průvodce kopírováním** . Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) .

K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager template** , **.NET API** a **REST API** . Podrobné pokyny k vytvoření kanálu s aktivitou kopírování najdete v [kurzu kopírování aktivit](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Bez ohledu na to, jestli používáte nástroje nebo rozhraní API, provedete následující kroky k vytvoření kanálu, který přesouvá data ze zdrojového úložiště dat do úložiště dat jímky:

1. Vytvořte **propojené služby** , které propojí vstupní a výstupní úložiště dat s datovou továrnou.
2. Vytvořte datové **sady** , které reprezentují vstupní a výstupní data pro operaci kopírování.
3. Vytvořte **kanál** s aktivitou kopírování, která převezme datovou sadu jako vstup a datovou sadu jako výstup.

Při použití Průvodce se automaticky vytvoří definice JSON pro tyto Entity Data Factory (propojené služby, datové sady a kanál). Pokud používáte nástroje/rozhraní API (s výjimkou rozhraní .NET API), definujete tyto Data Factory entit pomocí formátu JSON. Ukázku s definicemi JSON pro Data Factory entity, které se používají ke kopírování dat z úložiště dat ODBC, najdete v části [příklad JSON: kopírování dat z úložiště dat ODBC do Azure Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) tohoto článku.

Následující části obsahují podrobné informace o vlastnostech JSON, které se používají k definování Data Factory entit specifických pro úložiště dat rozhraní ODBC:

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka uvádí popis pro prvky JSON specifické pro propojenou službu ODBC.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ |Vlastnost Type musí být nastavená na: **OnPremisesOdbc** . |Ano |
| připojovací řetězec |Část připojovacího řetězce bez přístupu k přístupu a volitelné šifrované přihlašovací údaje. Podívejte se na příklady v následujících oddílech. <br/><br/>Můžete zadat připojovací řetězec se vzorem, jako je `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` , nebo použít systémový název DSN (název zdroje dat), který jste nastavili na počítači brány s nástrojem `"DSN=<name of the DSN>;"` (pro odpovídající část přihlašovací údaje v propojené službě). |Ano |
| pověření |Část přístupového pověření v připojovacím řetězci, kterou jste zadali ve formátu hodnoty vlastnosti specifické pro ovladač. Příklad: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Ne |
| authenticationType |Typ ověřování, který se používá pro připojení k úložišti dat ODBC. Možné hodnoty jsou: Anonymous a Basic. |Ano |
| userName |Pokud používáte základní ověřování, zadejte uživatelské jméno. |Ne |
| heslo |Zadejte heslo pro uživatelský účet, který jste zadali pro uživatelské jméno. |Ne |
| gatewayName |Název brány, kterou by služba Data Factory měla použít pro připojení k úložišti dat ODBC. |Ano |

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
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Použití základního ověřování s šifrovanými přihlašovacími údaji
Přihlašovací údaje můžete šifrovat pomocí rutiny [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (1,0 verze Azure PowerShell) nebo rutiny [New-AzureDataFactoryEncryptValue](/previous-versions/azure/dn834940(v=azure.100)) (0,9 nebo starší verze Azure PowerShell).

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

### <a name="using-anonymous-authentication"></a>Použití anonymního ověřování

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
Úplný seznam sekcí & vlastností dostupných pro definování datových sad naleznete v článku [vytvoření datových sad](data-factory-create-datasets.md) . Oddíly, jako je například struktura, dostupnost a zásada pro datovou sadu JSON, jsou podobné pro všechny typy datových sad (Azure SQL, Azure Blob, tabulka Azure atd.).

Oddíl **typeProperties** se liší pro každý typ datové sady a poskytuje informace o umístění dat v úložišti dat. Oddíl typeProperties pro datovou sadu **relačních** objektů typu (která zahrnuje datovou sadu ODBC) má následující vlastnosti.

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| tableName |Název tabulky v úložišti dat rozhraní ODBC. |Ano |

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování
Úplný seznam sekcí & vlastností dostupných pro definování aktivit najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásady.

Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity, se liší v závislosti na jednotlivých typech aktivit. U aktivity kopírování se liší v závislosti na typech zdrojů a jímky.

Pokud je zdroj v aktivitě kopírování typu **RelationalSource** (který zahrnuje rozhraní ODBC), jsou v části typeProperties k dispozici následující vlastnosti:

| Vlastnost | Popis | Povolené hodnoty | Povinné |
| --- | --- | --- | --- |
| query |Pomocí vlastního dotazu můžete číst data. |Řetězec dotazu SQL. Příklad: SELECT * FROM MyTable. |Ano |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>Příklad JSON: kopírování dat z úložiště dat ODBC do Azure Blob
Tento příklad poskytuje definice JSON, které lze použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ukazuje, jak kopírovat data ze zdroje ODBC do Azure Blob Storage. Data však lze zkopírovat do kterékoli z těchto umyvadel, které jsou [zde](data-factory-data-movement-activities.md#supported-data-stores-and-formats) uvedeny, pomocí aktivity kopírování v Azure Data Factory.

Ukázka má následující Entity Data Factory:

1. Propojená služba typu [OnPremisesOdbc](#linked-service-properties).
2. Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstupní [datová sada](data-factory-create-datasets.md) typu [relačních](#dataset-properties)objektů.
4. Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
5. [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [RelationalSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z výsledků dotazu v úložišti dat ODBC do objektu BLOB každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v oddílech následujících po ukázkách.

Jako první krok nastavte bránu pro správu dat. Pokyny najdete v článku [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md) .

**Propojená služba ODBC** V tomto příkladu se používá základní ověřování. Různé typy ověřování, které můžete použít, najdete v části [propojená služba ODBC](#linked-service-properties) .

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

**Vstupní datová sada ODBC**

Ukázka předpokládá, že jste v databázi ODBC vytvořili tabulku "MyTable" a obsahuje sloupec s názvem "timestampcolumn" pro data časové řady.

Nastavení "externí": "true" informuje službu Data Factory o tom, že datová sada je externí pro objekt pro vytváření dat, a není vytvořena aktivitou v datové továrně.

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

Data se zapisují do nového objektu BLOB každou hodinu (frekvence: hodina, interval: 1). Cesta ke složce pro objekt BLOB je dynamicky vyhodnocována na základě počátečního času zpracovávaného řezu. Cesta ke složce používá části rok, měsíc, den a hodiny v počátečním čase.

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

**Aktivita kopírování v kanálu se zdrojem ODBC (RelationalSource) a jímky objektů BLOB (BlobSink)**

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala tyto vstupní a výstupní datové sady a má naplánované spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **RelationalSource** a typ **jímky** je nastavený na **BlobSink** . Dotaz SQL zadaný pro vlastnost **dotazu** vybere data během uplynulé hodiny ke zkopírování.

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
### <a name="type-mapping-for-odbc"></a>Mapování typů pro rozhraní ODBC
Jak je uvedeno v článku [aktivity přesunu dat](data-factory-data-movement-activities.md) , aktivita kopírování provádí automatické převody typů ze zdrojových typů do typů jímky s následujícím dvěma kroky:

1. Převod z nativních zdrojových typů na typ .NET
2. Převést z typu .NET na nativní typ jímky

Při přesouvání dat z úložišť dat ODBC jsou datové typy ODBC mapovány na typy .NET, jak je uvedeno v tématu [mapování datových typů ODBC](/dotnet/framework/data/adonet/odbc-data-type-mappings) .

## <a name="map-source-to-sink-columns"></a>Mapovat zdroj na sloupce jímky
Další informace o mapování sloupců ve zdrojové datové sadě na sloupce v datové sadě jímky najdete v tématu [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn. Viz [opakované čtení z relačních zdrojů](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Řešení potíží s připojením
Pokud chcete řešit problémy s připojením, **Diagnostics** použijte kartu Diagnostika **Správa dat brány Configuration Manager** .

1. Spusťte **Configuration Manager Správa dat brány** . Můžete buď spustit "C:\Program Files\Microsoft Správa dat Gateway\1.0\Shared\ConfigManager.exe" přímo (nebo) vyhledat **bránu** , abyste našli odkaz na aplikaci **Microsoft Správa dat Gateway** , jak je znázorněno na následujícím obrázku.

    ![Hledat bránu](./media/data-factory-odbc-connector/search-gateway.png)
2. Přepněte na kartu **Diagnostika** .

    ![Diagnostika brány](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Vyberte **typ** úložiště dat (propojená služba).
4. Zadejte **ověřování** a zadejte **přihlašovací údaje** (nebo) zadejte **připojovací řetězec** , který se používá pro připojení k úložišti dat.
5. Klikněte na **Test připojení** a otestujte připojení k úložišti dat.

## <a name="performance-and-tuning"></a>Výkon a optimalizace
Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v tématu [Průvodce optimalizací aktivity kopírování &](data-factory-copy-activity-performance.md) .