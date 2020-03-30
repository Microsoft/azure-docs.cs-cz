---
title: Přesunutí dat ze zdroje HTTP – Azure
description: Zjistěte, jak přesunout data z místního nebo cloudového zdroje HTTP pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e668f44bbc3d2e381edeb80c568a41355584a4ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260420"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Přesunutí dat ze zdroje HTTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](data-factory-http-connector.md)
> * [Verze 2 (aktuální verze)](../connector-http.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Azure Data Factory, přečtěte si téma [HTTP konektor ve V2](../connector-http.md).


Tento článek popisuje, jak pomocí kopírovat aktivity v Azure Data Factory přesunout data z místního nebo cloudového koncového bodu HTTP do úložiště dat podporované jímky. Tento článek vytváří [data přesunutí pomocí aktivity kopírování](data-factory-data-movement-activities.md), která představuje obecný přehled přesunu dat pomocí aktivity kopírování. Článek také uvádí úložiště dat, která podporuje aktivitu kopírování jako zdroje a jímky.

Data Factory aktuálně podporuje pouze přesunutí dat ze zdroje HTTP do jiných úložišť dat. Nepodporuje přesun dat z jiných úložišť dat do cíle HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování

Tento konektor HTTP můžete použít k načtení dat z *cloudu i místního koncového bodu HTTP/S* pomocí metod HTTP **GET** nebo **POST.** Podporovány jsou následující typy ověřování: **Anonymní**, **Základní**, **Digest**, **Windows**a **ClientCertificate**. Všimněte si rozdílu mezi tímto konektorem a [konektorem webové tabulky](data-factory-web-table-connector.md). Konektor webové tabulky extrahuje obsah tabulky z webové stránky HTML.

Když kopírujete data z místního koncového bodu HTTP, musíte nainstalovat bránu pro správu dat v místním prostředí nebo v virtuálním počítači Azure. Informace o službě Data Management Gateway a podrobných pokynech k nastavení brány najdete v tématu [Přesun dat mezi místními umístěními a cloudem](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Začínáme

Pomocí různých nástrojů nebo souborů API můžete vytvořit kanál, který má aktivitu kopírování pro přesun dat ze zdroje HTTP:

- Nejjednodušší způsob, jak vytvořit kanál, je použít Průvodce kopírováním dat. Rychlý návod k vytvoření kanálu pomocí Průvodce kopírováním dat naleznete v [tématu Návod: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

- K vytvoření kanálu můžete taky použít následující nástroje: **Visual Studio**, **Azure PowerShell**, **šablonu Azure Resource Manager**, rozhraní **.NET API**nebo ROZHRANÍ REST **API**. Podrobné pokyny k vytvoření kanálu, který má aktivitu kopírování, naleznete v [tématu Copy Activity tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Ukázky JSON, které kopírují data ze zdroje HTTP do úložiště objektů blob Azure, najdete v [příkladech JSON](#json-examples).

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Následující tabulka popisuje prvky JSON, které jsou specifické pro propojenou službu HTTP:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type | Vlastnost **type** musí být nastavena na **http**. | Ano |
| url | Základní adresa URL webového serveru. | Ano |
| authenticationType | Určuje typ ověřování. Povolené hodnoty jsou **Anonymous**, **Basic**, **Digest**, **Windows**a **ClientCertificate**. <br><br> Další vlastnosti a ukázky JSON pro tyto typy ověřování naleznete v dalších částech tohoto článku. | Ano |
| enableServerCertificateValidation | Určuje, zda má být povolení ověření certifikátu SSL serveru, pokud je zdrojem webový server HTTPS. Pokud server HTTPS používá certifikát podepsaný svým držitelem, nastavte tento certifikát na **hodnotu false**. | Ne<br /> (výchozí nastavení je **pravda**) |
| název brány | Název instance Brány pro správu dat, který se má použít pro připojení k místnímu zdroji HTTP. | Ano, pokud kopírujete data z místního zdroje HTTP |
| šifrované pověření | Šifrované přihlašovací údaje pro přístup ke koncovému bodu HTTP. Hodnota je automaticky generována při konfiguraci ověřovacích informací v Průvodci kopírováním nebo pomocí dialogového okna **ClickOnce.** | Ne<br /> (platí pouze při kopírování dat z místního http serveru) |

Podrobnosti o nastavení přihlašovacích údajů pro místní zdroj dat konektoru HTTP najdete v tématu [Přesun dat mezi místními zdroji a cloudem pomocí brány pro správu dat](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Použití základního ověřování, ověřování algoritmem Digest nebo Systému Windows

Nastavte **authenticationType** na **Základní**, **Digest**nebo **Windows**. Kromě obecných vlastností konektoru HTTP popsaných v předchozích částech nastavte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| userName | Uživatelské jméno, které se má použít pro přístup ke koncovému bodu HTTP. | Ano |
| heslo | Heslo pro uživatele (**uživatelské jméno**). | Ano |

**Příklad: Použití základního ověřování, ověřování algoritmem Digest nebo Systému Windows**

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

### <a name="using-clientcertificate-authentication"></a>Použití ověřování clientcertificate

Chcete-li použít základní ověřování, nastavte **typ authenticationType** na **ClientCertificate**. Kromě obecných vlastností konektoru HTTP popsaných v předchozích částech nastavte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| embeddedCertData | Obsah kódu Base64 binárních dat souboru PFX. | Určení **vloženého certdata** nebo **certThumbprint** |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován v úložišti certifikátů počítače brány. Použít pouze v případě, že zkopírujete data z místního zdroje HTTP. | Určení **vloženého certdata** nebo **certThumbprint** |
| heslo | Heslo, které je přidruženo k certifikátu. | Ne |

Pokud používáte **certThumbprint** pro ověřování a certifikát je nainstalován v osobním úložišti místního počítače, udělte službě brány oprávnění ke čtení:

1. Otevřete konzolu MMC (Microsoft Management Console). Přidejte modul snap-in **Certifikáty,** který cílí na **místní počítač**.
2. Rozbalte **položku Certifikáty** > **osobní**a vyberte **možnost Certifikáty**.
3. Klepněte pravým tlačítkem myši na certifikát z osobního úložiště a vyberte příkaz **Všechny úkoly** >**spravovat soukromé klíče**.
3. Na kartě **Zabezpečení** přidejte uživatelský účet, pod kterým je spuštěna hostitelská služba brány pro správu dat, s přístupem pro čtení k certifikátu.  

**Příklad: Použití klientského certifikátu**

Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá klientský certifikát, který je nainstalován v počítači, ve který je nainstalována brána pro správu dat.

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

**Příklad: Použití klientského certifikátu v souboru**

Tato propojená služba propojuje vaši datovou továrnu s místním webovým serverem HTTP. Používá soubor klientského certifikátu v počítači, ve který je nainstalována brána pro správu dat.

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

Některé části souboru JSON datové sady, jako je struktura, dostupnost a zásady, jsou podobné pro všechny typy datových sad (Azure SQL Database, Azure Blob storage, Azure Table storage).

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete [v tématu Vytváření datových sad](data-factory-create-datasets.md).

Oddíl **typeProperties** se liší pro každý typ datové sady. Část **typeProperties** obsahuje informace o umístění dat v úložišti dat. Oddíl **typeProperties** pro datovou sadu typu **Http** má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** datové sady musí být nastaven na **http**. | Ano |
| Relativeurl | Relativní adresa URL prostředku, který obsahuje data. Pokud cesta není zadána, použije se pouze adresa URL zadaná v definici propojené služby. <br><br> Chcete-li vytvořit dynamickou adresu URL, můžete použít [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md). Příklad: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)**. | Ne |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **GET** a **POST**. | Ne <br />(výchozí hodnota je **GET**) |
| additionalHeaders | Další hlavičky požadavku HTTP. | Ne |
| requestBody | Tělo pro požadavek HTTP. | Ne |
| formát | Pokud chcete *načíst data z koncového bodu HTTP* bez analýzy, přeskočte nastavení **formátu.** <br><br> Chcete-li během kopírování analyzovat obsah odpovědi HTTP, jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**a **ParquetFormat**. Další informace naleznete [v tématech Textový formát](data-factory-supported-file-and-compression-formats.md#text-format), Formát [JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formát orků](data-factory-supported-file-and-compression-formats.md#orc-format)a [Formát parket](data-factory-supported-file-and-compression-formats.md#parquet-format). |Ne |
| komprese | Určete typ a úroveň komprese dat. Podporované typy: **GZip**, **Deflate**, **BZip2**a **ZipDeflate**. Podporované úrovně: **Optimální** a **nejrychlejší**. Další informace najdete [v tématu Soubory a kompresní formáty v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

**Příklad: Použití metody GET (výchozí)**

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

**Příklad: Použití metody POST**

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

## <a name="copy-activity-properties"></a>Kopírovat vlastnosti aktivity

Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete [v tématu Vytváření kanálů](data-factory-create-pipelines.md). 

Vlastnosti, které jsou k dispozici v části **typeProperties** aktivity se liší podle každého typu aktivity. U aktivity kopírování se vlastnosti liší v závislosti na typech zdrojů a propadů.

V současné době, pokud zdroj v aktivitě kopírování je **typu HttpSource,** jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový čas (hodnota **TimeSpan)** pro požadavek HTTP získat odpověď. Je to časový časový čas pro získání odpovědi, nikoli časový čas pro čtení dat odpovědi. | Ne<br />(výchozí hodnota: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese

Další informace najdete [v tématu Formáty souborů a komprese v Azure Data Factory.](data-factory-supported-file-and-compression-formats.md)

## <a name="json-examples"></a>Příklady JSON

Následující příklady poskytují ukázkové definice JSON, které můžete použít k vytvoření kanálu pomocí [Sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo Azure [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak zkopírovat data ze zdroje HTTP do úložiště objektů blob Azure. Data však můžete zkopírovat *přímo* z libovolného zdroje do libovolného jímky, [které jsou podporovány](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

**Příklad: Kopírování dat ze zdroje HTTP do úložiště objektů blob Azure**

Řešení Data Factory pro tuto ukázku obsahuje následující entity Data Factory:

*   Propojená služba typu [HTTP](#linked-service-properties).
*   Propojená služba typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Vstupní [datová sada](data-factory-create-datasets.md) typu [Http](#dataset-properties).
*   Výstupní [datová sada](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   [Kanál,](data-factory-create-pipelines.md) který má aktivitu kopírování, která používá [HttpSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data ze zdroje HTTP do objektu blob Azure každou hodinu. Vlastnosti JSON použité v těchto vzorcích jsou popsány v částech, které následují za vzorky.

### <a name="http-linked-service"></a>Propojená služba HTTP

Tento příklad používá službu propojenou s protokolem HTTP s anonymním ověřováním. Různé typy ověřování, které můžete použít, naleznete [v tématu http propojená služba.](#linked-service-properties)

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

### <a name="azure-storage-linked-service"></a>Propojená služba Azure storage

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

Nastavení **externí** na **hodnotu true** informuje službu Data Factory, že datová sada je externí pro datovou továrnu a není vytvářena aktivitou v datové továrně.

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

Data se zapisují do nového objektu blob každou hodinu (**frekvence**: **hodina**, **interval**: **1**).

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

Kanál obsahuje aktivitu kopírování, která je nakonfigurována pro použití vstupních a výstupních datových sad. Aktivita kopírování je naplánována na každou hodinu. V definici kanálu JSON je **typ zdroje** nastaven na **HttpSource** a typ **jímky** je nastaven na **Objekt blobSink**.

Seznam vlastností, které **podporuje HttpSource,** naleznete [v tématu HttpSource](#copy-activity-properties).

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
> Pokud chcete mapovat sloupce ze zdrojové datové sady na sloupce z datové sady jímky, přečtěte si témat [mapování sloupců datových sad v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění

Informace o klíčových faktorech, které ovlivňují výkon přesunu dat (aktivita kopírování) v Azure Data Factory a různých způsobech jeho optimalizace, najdete v [tématu Sledování kopírování a ladění průvodce](data-factory-copy-activity-performance.md).
