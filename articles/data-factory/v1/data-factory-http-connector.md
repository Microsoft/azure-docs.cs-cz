---
title: Přesun dat ze zdroje HTTP – Azure
description: Naučte se, jak přesouvat data z místního nebo cloudového zdroje HTTP pomocí Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/22/2018
robots: noindex
ms.openlocfilehash: ce29b5a112d70575a721b0b527947fd95868da80
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382918"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Přesun dat ze zdroje HTTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](data-factory-http-connector.md)
> * [Verze 2 (aktuální verze)](../connector-http.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Azure Data Factory, přečtěte si téma [konektor http ve verzi v2](../connector-http.md).

Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory přesouvat data z místního nebo cloudového koncového bodu HTTP do podporovaného úložiště dat jímky. Tento článek [sestaví přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md), která představuje obecný přehled přesunu dat pomocí aktivity kopírování. Článek také obsahuje seznam úložišť dat, která aktivita kopírování podporuje jako zdroje a jímky.

Data Factory aktuálně podporuje pouze přesun dat ze zdroje HTTP do jiných úložišť dat. Nepodporuje přesouvání dat z jiných úložišť dat do cíle HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování

Tento konektor HTTP můžete použít k načtení dat z *cloudu i místního koncového bodu http/S* pomocí metod http **Get** nebo **post** . Podporovány jsou následující typy ověřování: **anonymní**, **základní**, **Digest**, **Windows** a **ClientCertificate**. Všimněte si rozdílů mezi tímto konektorem a [konektorem webové tabulky](data-factory-web-table-connector.md). Konektor webové tabulky extrahuje obsah tabulky z webové stránky HTML.

Když kopírujete data z místního koncového bodu HTTP, musíte nainstalovat Správa dat bránu v místním prostředí nebo ve virtuálním počítači Azure. Další informace o službě Správa dat Gateway a podrobné pokyny k nastavení brány najdete v tématu [přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Začínáme

Můžete vytvořit kanál, který má aktivitu kopírování pro přesun dat ze zdroje HTTP pomocí různých nástrojů nebo rozhraní API:

- Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce Kopírování dat. Rychlý návod k vytvoření kanálu pomocí Průvodce Kopírování dat najdete v tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

- K vytvoření kanálu můžete také použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **šablonu Azure Resource Manager**, rozhraní **.NET API** nebo **REST API**. Podrobné pokyny, jak vytvořit kanál s aktivitou kopírování, najdete v [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Ukázky JSON, které kopírují data ze zdroje HTTP do úložiště objektů BLOB v Azure, najdete v [příkladech JSON](#json-examples).

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka popisuje elementy JSON, které jsou specifické pro propojenou službu HTTP:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| typ | Vlastnost **Type** musí být nastavená na **http**. | Yes |
| url | Základní adresa URL webového serveru. | Yes |
| authenticationType | Určuje typ ověřování. Povolené hodnoty jsou **anonymní**, **základní**, **Digest**, **Windows** a **ClientCertificate**. <br><br> Další vlastnosti a ukázky JSON pro tyto typy ověřování najdete v dalších částech tohoto článku. | Yes |
| enableServerCertificateValidation | Určuje, jestli se má povolit ověřování certifikátu TLS/SSL serveru, pokud je zdrojem webovým serverem HTTPS. Pokud server HTTPS používá certifikát podepsaný svým držitelem, nastavte tuto hodnotu na **false**. | No<br /> (výchozí hodnota je **true**) |
| gatewayName | Název instance Správa dat brány, která se má použít pro připojení k místnímu zdroji HTTP | Ano, pokud kopírujete data z místního zdroje HTTP |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup ke koncovému bodu HTTP Hodnota je generována automaticky při konfiguraci ověřovacích informací v průvodci kopírováním nebo pomocí dialogového okna **ClickOnce** . | No<br /> (platí jenom při kopírování dat z místního serveru HTTP) |

Podrobnosti o nastavení přihlašovacích údajů pro zdroj dat konektoru HTTP najdete v tématu [přesun dat mezi místními zdroji a cloudem pomocí Správa dat brány](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Použití ověřování Basic, Digest nebo Windows

Nastavte **AuthenticationType** na **Basic**, **Digest** nebo **Windows**. Kromě obecných vlastností konektoru protokolu HTTP popsaných v předchozích částech nastavte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| userName | Uživatelské jméno, které se má použít pro přístup ke koncovému bodu HTTP. | Yes |
| heslo | Heslo pro uživatele (**uživatelské jméno**). | Yes |

**Příklad: použití ověřování Basic, Digest nebo Windows**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Použití ověřování ClientCertificate

Pokud chcete použít základní ověřování, nastavte **AuthenticationType** na **ClientCertificate**. Kromě obecných vlastností konektoru protokolu HTTP popsaných v předchozích částech nastavte následující vlastnosti:

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| embeddedCertData | Obsah binárních dat v souboru PFX kódovaný ve formátu base64 | Zadejte buď **embeddedCertData** nebo **certThumbprint** |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován v úložišti certifikátů počítače brány. Platí pouze při kopírování dat z místního zdroje HTTP. | Zadejte buď **embeddedCertData** nebo **certThumbprint** |
| heslo | Heslo, které je přidruženo k certifikátu. | No |

Pokud pro ověřování používáte **certThumbprint** a certifikát se instaluje do osobního úložiště místního počítače, udělte službě brány oprávnění ke čtení:

1. Otevřete konzolu MMC (Microsoft Management Console). Přidejte modul snap-in **certifikáty** , který cílí na **místní počítač**.
2. Rozbalte položku **certifikáty**  >  **osobní** a pak vyberte možnost **certifikáty**.
3. Pravým tlačítkem myši klikněte na certifikát z osobního úložiště a pak vyberte **všechny úlohy**  > **spravovat soukromé klíče**.
3. Na kartě **zabezpečení** přidejte uživatelský účet, pod kterým je spuštěná hostitelská služba brány Správa dat, s přístupem pro čtení k certifikátu.  

**Příklad: použití klientského certifikátu**

Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá klientský certifikát, který je nainstalovaný na počítači, který má nainstalovanou bránu Správa dat.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

**Příklad: použití klientského certifikátu v souboru**

Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá soubor certifikátu klienta na počítači, který má nainstalovanou bránu Správa dat.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Některé oddíly souboru JSON datové sady, jako je například struktura, dostupnost a zásada, jsou podobné pro všechny typy datových sad (Azure SQL Database, Azure Blob Storage, Azure Table Storage).

Úplný seznam sekcí a vlastností, které jsou k dispozici pro definování datových sad, najdete v tématu [vytváření datových sad](data-factory-create-datasets.md).

Oddíl **typeProperties** se liší pro každý typ datové sady. Část **typeProperties** poskytuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **http** má následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | **Typ** datové sady musí být nastaven na **http**. | Yes |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Když cesta není zadaná, použije se jenom adresa URL zadaná v definici propojené služby. <br><br> Chcete-li vytvořit dynamickou adresu URL, můžete použít [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md). Příklad: **RelativeURL**: **$ $text. Format ('/My/Report? month = {0: rrrr}-{0: mm} &FMT = CSV ', vlastnosti slicestart)**. | No |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **Get** a **post**. | No <br />(výchozí nastavení se **získá**) |
| additionalHeaders | Další hlavičky požadavku HTTP | No |
| částmi | Tělo požadavku HTTP | No |
| formát | Pokud chcete *načíst data z koncového bodu http, jak je* bez analýzy, přeskočte nastavení **formátu** . <br><br> Pokud chcete analyzovat obsah odpovědi HTTP během kopírování, podporují se tyto typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** a **ParquetFormat**. Další informace najdete v tématech [Formát textu](data-factory-supported-file-and-compression-formats.md#text-format), [formát JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), formát [ORC](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| komprese | Zadejte typ a úroveň komprese dat. Podporované typy: **gzip**, **Deflate**, **bzip2** a **ZipDeflate**. Podporované úrovně: **optimální** a **nejrychlejší**. Další informace naleznete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Příklad: použití metody GET (default)**

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
          "relativeUrl": "XXX/test.xml",
          "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Příklad: použití metody POST**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
       "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Pro všechny typy aktivit jsou k dispozici vlastnosti, jako je název, popis, vstupní a výstupní tabulka a zásada.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). 

Vlastnosti, které jsou k dispozici v části **typeProperties** v aktivitě, se liší podle typu aktivity. V případě aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů a jímky.

V současné době platí, že pokud je zdroj v aktivitě kopírování **HttpSource** typu, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový limit (hodnota **TimeSpan** ) požadavku HTTP získat odpověď. Je časový limit pro získání odpovědi, nikoli časový limit pro čtení dat odpovědi. | No<br />(výchozí hodnota: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese

Další informace najdete [v tématu formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples"></a>Příklady JSON

V následujících příkladech jsou uvedeny ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí sady [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak kopírovat data ze zdroje HTTP do úložiště objektů BLOB v Azure. Data se ale dají zkopírovat *přímo* ze všech zdrojů do jakékoli z jímky [, které jsou podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

**Příklad: kopírování dat ze zdroje HTTP do Azure Blob Storage**

Řešení Data Factory pro tuto ukázku obsahuje následující Data Factory entity:

*   Propojená služba typu [http](#linked-service-properties).
*   Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Vstupní [datová sada](data-factory-create-datasets.md) typu [http](#dataset-properties).
*   Výstupní [datová sada](data-factory-create-datasets.md) typu [azureblobu](data-factory-azure-blob-connector.md#dataset-properties).
*   [Kanál](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [HttpSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data ze zdroje HTTP do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto ukázkách jsou popsány v částech, které následují po ukázkách.

### <a name="http-linked-service"></a>Propojená služba HTTP

V tomto příkladu se používá propojená služba HTTP s anonymním ověřováním. Různé typy ověřování, které můžete použít, najdete v tématu [propojená služba HTTP](#linked-service-properties) .

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Vstupní datová sada HTTP

Nastavení **externí** na **true** informuje Data Factory službu, že datová sada je externí pro objekt pro vytváření dat a není vytvořená aktivitou v datové továrně.

```json
{
  "name": "HttpSourceDataInput",
    "properties": {
    "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
        "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
    },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Výstupní datová sada Azure Blob

Data se zapisují do nového objektu BLOB každou hodinu (**frekvence**: **hodina**, **interval**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Kanál, který používá aktivitu kopírování

Kanál obsahuje aktivitu kopírování, která je nakonfigurovaná tak, aby používala vstupní a výstupní datové sady. Aktivita kopírování je naplánována ke spuštění každou hodinu. V definici JSON kanálu je typ **zdroje** nastavený na **HttpSource** a typ **jímky** je nastavený na **BlobSink**.

Seznam vlastností, které **HttpSource** podporuje, najdete v tématu [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Chcete-li namapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si téma [mapování sloupců datové sady v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění

Další informace o klíčových faktorech, které mají vliv na výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jejich optimalizace, najdete v [Průvodci výkonem a optimalizací aktivity kopírování](data-factory-copy-activity-performance.md).
