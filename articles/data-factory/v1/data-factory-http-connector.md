---
title: Přesun dat ze zdroje HTTP - Azure | Microsoft Docs
description: Další informace o tom, jak přesunout data z místní nebo zdroji HTTP cloudu pomocí Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 29281843dc1b375182eb3dafe95ad86c89217671
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37052269"
---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Přesun dat z HTTP zdroje pomocí Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-http-connector.md)
> * [Verze 2 (aktuální verze)](../connector-http.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [HTTP konektoru V2](../connector-http.md).


Tento článek popisuje, jak pomocí aktivity kopírování v Azure Data Factory pro přesun dat z koncový bod HTTP lokální/Cloudová k úložišti dat podporovaných jímky. Tento článek vychází [aktivity přesunu dat](data-factory-data-movement-activities.md) článek, který uvádí obecný přehled přesun dat s aktivitou kopírování a seznam úložiště dat, které jsou podporované jako zdroje nebo jímky.

Objekt pro vytváření dat aktuálně podporuje pouze přesunutí dat z HTTP zdroje k jiným úložištím dat, ale není přesouvání dat od ostatních dat ukládá na umístění protokolu HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování
Tento konektor HTTP můžete použít k načtení dat z **cloudové i místní koncový bod HTTP/s** pomocí protokolu HTTP **získat** nebo **POST** metoda. Jsou podporovány následující typy ověřování: **anonymní**, **základní**, **Digest**, **Windows**, a  **ClientCertificate**. Všimněte si rozdíl mezi tohoto konektoru a [konektor tabulky webových](data-factory-web-table-connector.md) je: se používá k extrahování obsahu tabulky z HTML webové stránky.

Při kopírování dat z místní koncový bod protokolu HTTP, je nutné nainstalovat brána pro správu dat v prostředí nebo Azure místní počítač. V tématu [přesouvání dat mezi místní umístění a cloudem](data-factory-move-data-between-onprem-and-cloud.md) článku se dozvíte o Brána pro správu dat a podrobné pokyny o nastavení brány.

## <a name="getting-started"></a>Začínáme
Vytvoření kanálu s aktivitou kopírování, který přesouvá data z zdroje HTTP pomocí různých nástrojů nebo rozhraní API.

- Nejjednodušší způsob, jak vytvořit kanál je použití **Průvodce kopírováním**. V tématu [kurz: vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md) podrobný rychlé vytvoření kanálu pomocí Průvodce kopírováním data.

- Tyto nástroje můžete také použít k vytvoření kanálu: **portál Azure**, **Visual Studio**, **prostředí Azure PowerShell**, **šablony Azure Resource Manageru** , **.NET API**, a **rozhraní REST API**. V tématu [kurzu aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) podrobné pokyny k vytvoření kanálu s aktivitou kopírování. JSON ukázky ke zkopírování dat z HTTP zdroje do Azure Blob Storage, najdete v části [JSON příklady](#json-examples) části Tento článek.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby
Následující tabulka obsahuje popis JSON elementy, které jsou specifické pro protokol HTTP propojené služby.

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type | Vlastnost typu musí být nastavena na: `Http`. | Ano |
| url | Základní adresu URL na webový server | Ano |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou: **anonymní**, **základní**, **Digest**, **Windows**, **ClientCertificate**. <br><br> Naleznete v části dál v této tabulce na další vlastnosti a ukázky JSON pro tyto typy ověřování v uvedeném pořadí. | Ano |
| enableServerCertificateValidation | Určete, zda chcete povolit ověřování certifikátu protokolu SSL serveru, pokud je zdroj Webový Server HTTPS. Pokud váš server HTTPS používá certifikát podepsaný svým držitelem, nastavte na hodnotu false. | Ne, výchozí hodnota je true |
| gatewayName | Název brány pro správu dat pro připojení k místnímu zdroji HTTP. | Ano, pokud kopírování dat z místního zdroje HTTP. |
| encryptedCredential | Šifrovaný přihlašovací údaje pro přístup k koncový bod HTTP. Automaticky vygenerované při konfiguraci informace o ověřování v Průvodce kopírováním nebo dialogové okno místní ClickOnce. | Ne. Platí jenom v případě, že kopírování dat z místního serveru HTTP. |

V tématu [přesun dat mezi místní zdroje a cloudu s Brána pro správu dat](data-factory-move-data-between-onprem-and-cloud.md) podrobnosti o nastavení přihlašovacích údajů pro zdroj dat konektor místní HTTP.

### <a name="using-basic-digest-or-windows-authentication"></a>Pomocí ověřování Basic, ověřování algoritmem Digest nebo systému Windows

Nastavit `authenticationType` jako `Basic`, `Digest`, nebo `Windows`a zadejte následující vlastnosti kromě konektor HTTP obecné ty, které jsou zavedené výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno | Uživatelské jméno pro přístup k koncový bod HTTP. | Ano |
| heslo | Heslo pro uživatele (uživatelské jméno). | Ano |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Příklad: použití ověřování Basic, ověřování algoritmem Digest nebo systému Windows

```JSON
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

### <a name="using-clientcertificate-authentication"></a>Pomocí ClientCertificate ověřování

Chcete-li základní ověřování použijte, nastavte `authenticationType` jako `ClientCertificate`a zadejte následující vlastnosti kromě konektor HTTP obecné ty, které jsou zavedené výše:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| embeddedCertData | Obsah s kódováním base64, pomocí binárních dat soubor Personal Information Exchange (PFX). | Zadejte buď `embeddedCertData` nebo `certThumbprint`. |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován v úložišti certifikátů počítače brány. Platí jenom v případě, že kopírování dat z místního zdroje HTTP. | Zadejte buď `embeddedCertData` nebo `certThumbprint`. |
| heslo | Heslo přidružené k certifikátu. | Ne |

Pokud používáte `certThumbprint` pro ověřování a certifikát nainstalovaný v osobním úložišti místního počítače, musí udělit oprávnění ke čtení ke službě brány:

1. Spusťte konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in zacílený **místního počítače**.
2. Rozbalte položku **certifikáty**, **osobní**a klikněte na tlačítko **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a vyberte **všechny úlohy**->**spravovat privátní klíče...**
3. Na **zabezpečení** přidejte uživatelský účet, pod kterou je spuštěna hostitelská služba brány správy dat s přístupem pro čtení k certifikátu.  

#### <a name="example-using-client-certificate"></a>Příklad: pomocí klientského certifikátu
Tato propojená služba propojuje datovou továrnu místní webový server HTTP. Používá klientský certifikát, který je nainstalován na počítači s brána správy dat nainstalována.

```JSON
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

#### <a name="example-using-client-certificate-in-a-file"></a>Příklad: pomocí klientského certifikátu do souboru
Tato propojená služba propojuje datovou továrnu místní webový server HTTP. Používá soubor certifikátu klienta na počítači s brána správy dat nainstalována.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [vytváření datových sad](data-factory-create-datasets.md) článku. Oddíly, jako je například struktura, dostupnost a zásad JSON datové sady jsou podobné pro všechny typy datovou sadu (Azure SQL Azure blob, tabulky Azure, atd.).

**Rámci typeProperties** oddílu se liší pro jednotlivé typy datovou sadu a informace o umístění dat v úložišti dat. Rámci typeProperties část datové sady typ **Http** má následující vlastnosti

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Zadaný typ datové sady. musí být nastavena na `Http`. | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Pokud cesta není zadána, je použít jenom adresu URL, zadaný v definici propojené služby. <br><br> Vytvořit dynamické adresy URL, můžete použít [funkce pro vytváření dat a systémové proměnné](data-factory-functions-variables.md), například "relativeUrl": "$$Text.Format ('/ my/sestavy? měsíc = {0:yyyy}-{0:MM} & fmt = csv', SliceStart)". | Ne |
| requestMethod | Metoda HTTP. Povolené hodnoty jsou **získat** nebo **POST**. | Ne. Výchozí hodnota je `GET`. |
| additionalHeaders | Další hlavičky žádosti HTTP. | Ne |
| requestBody | Text pro požadavek HTTP. | Ne |
| Formát | Pokud chcete jednoduše **načtou data z koncový bod HTTP jako-je** bez analýza ho, přeskočte tento formát nastavení. <br><br> Pokud chcete analyzovat během kopírování obsahu odpovědi HTTP, jsou podporovány následující typy formátu: **TextFormat**, **JsonFormat**, **AvroFormat**,  **OrcFormat**, **ParquetFormat**. Další informace najdete v tématu [textovém formátu](data-factory-supported-file-and-compression-formats.md#text-format), [formátu Json](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formát](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc formátu](data-factory-supported-file-and-compression-formats.md#orc-format), a [Parquet formát](data-factory-supported-file-and-compression-formats.md#parquet-format) oddíly. |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy jsou: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Jsou podporované úrovně: **Optimal** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

### <a name="example-using-the-get-default-method"></a>Příklad: použití metody GET (výchozí)

```JSON
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

### <a name="example-using-the-post-method"></a>Příklad: pomocí metody POST

```JSON
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
Úplný seznam oddílů & vlastnosti, které jsou k dispozici pro definování aktivity, najdete v článku [vytváření kanálů](data-factory-create-pipelines.md) článku. Vlastnosti, například název, popis, vstupní a výstupní tabulky a zásad jsou dostupné pro všechny typy aktivit.

Vlastnosti, které jsou k dispozici v **rámci typeProperties** části aktivity na druhé straně lišit každý typ aktivity. Pro aktivitu kopírování budou lišit v závislosti na typech zdrojů a jímky.

V současné době po zdroji v aktivitě kopírování typu **HttpSource**, jsou podporovány následující vlastnosti.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový limit (TimeSpan) pro získání odezvy požadavku HTTP. Získání odezvy, není časový limit číst data odpovědi je časový limit. | Ne. Výchozí hodnota: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese
V tématu [formáty souborů a komprese v Azure Data Factory](data-factory-supported-file-and-compression-formats.md) článek na podrobnosti.

## <a name="json-examples"></a>Příklady JSON
Následující příklad zadejte ukázka JSON definice, které můžete použít k vytvoření kanálu pomocí [portál Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) nebo [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Se ukazují, jak zkopírovat data ze zdroje HTTP do Azure Blob Storage. Nicméně je možné zkopírovat data **přímo** ze všech zdrojů do jakéhokoli z jímky uvádí [sem](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování v Azure Data Factory.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Příklad: Kopírování dat ze zdroje HTTP do Azure Blob Storage
Řešení Data Factory pro tato ukázka obsahuje následující entity služby Data Factory:

1. Propojené služby typu [HTTP](#linked-service-properties).
2. Propojené služby typu [azurestorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Vstup [datovou sadu](data-factory-create-datasets.md) typu [Http](#dataset-properties).
4. Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [HttpSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka zkopíruje data z HTTP zdroje do objektu blob Azure každou hodinu. Vlastnostech JSON použitých ve tyto ukázky jsou popsané v části následující ukázky.

### <a name="http-linked-service"></a>Služba HTTP propojené
Tento příklad používá služba HTTP propojené s anonymní ověřování. V tématu [HTTP propojená služba](#linked-service-properties) části pro různé typy ověřování můžete použít.

```JSON
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

```JSON
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

### <a name="http-input-dataset"></a>Vstupní datové sady HTTP
Nastavení **externí** k **true** služba Data Factory informuje, že datová sada je externí k objektu pro vytváření dat a není vyprodukované aktivitu v datové továrně.

```JSON
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

Data se zapisují do nového objektu blob každou hodinu (frekvence: hodiny, interval: 1).

```JSON
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

### <a name="pipeline-with-copy-activity"></a>Kanál s aktivitou kopírování

Kanál obsahuje aktivitu kopírování, který je nakonfigurovaný na použití vstupní a výstupní datové sady a je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **HttpSource** a **podřízený** je typ nastaven na **BlobSink**.

V tématu [HttpSource](#copy-activity-properties) pro seznam vlastností HttpSource nepodporuje.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
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
> Mapování sloupců z datové sady zdroje na sloupce ze sady jímku dat naleznete v tématu [mapování sloupců datovou sadu v Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění
V tématu [výkonu kopie aktivity & ladění průvodce](data-factory-copy-activity-performance.md) Další informace o klíčových faktorů, že dopad výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat ho.
