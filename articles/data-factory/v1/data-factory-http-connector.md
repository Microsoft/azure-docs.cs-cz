---
title: Přesun dat z HTTP zdroje – Azure | Dokumentace Microsoftu
description: Zjistěte, jak přesunout data z místní nebo cloudové zdroje pomocí protokolu HTTP s použitím služby Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f7e070788d2fc11addcafc30d9f232f194f44782
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017255"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Přesun dat z zdroje HTTP pomocí Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1](data-factory-http-connector.md)
> * [Verze 2 (aktuální verze)](../connector-http.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Azure Data Factory, přečtěte si téma [konektor HTTP ve V2](../connector-http.md).


Tento článek popisuje, jak používat aktivitu kopírování, která ve službě Azure Data Factory k přesunu dat z místní nebo cloudové koncový bod HTTP do úložiště dat jímky podporované. Tento článek vychází [přesun dat pomocí aktivity kopírování](data-factory-data-movement-activities.md), který nabízí obecný přehled o přesun dat pomocí aktivity kopírování. Tento článek obsahuje také seznam úložišť dat podporovaných aktivitou kopírování jako zdroje a jímky.

Data Factory aktuálně podporuje pouze přesouvá data z HTTP zdroje do dalších úložišť dat. Nepodporuje přesouvá data z jiných úložišť dat do umístění protokolu HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Podporované scénáře a typy ověřování

Můžete použít tento konektor HTTP pro načtení dat z *cloudové a místní koncový bod HTTP/S* pomocí HTTP **získat** nebo **příspěvek** metody. Jsou podporovány následující typy ověřování: **Anonymní**, **základní**, **Digest**, **Windows**, a **ClientCertificate**. Všimněte si rozdílu mezi tento konektor a [webový tabulky konektor](data-factory-web-table-connector.md). Webový konektor tabulky extrahuje obsah tabulky z webové stránce HTML.

Při kopírování dat z koncového bodu HTTP místní, musíte nainstalovat bránu správy dat v místním prostředí nebo ve Virtuálním počítači Azure. Další informace o bráně pro správu dat a podrobné pokyny o tom, jak nastavit bránu najdete v tématu [přesun dat mezi místními umístěními a cloudu](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Začínáme

Vytvoříte kanál s aktivitou kopírování pro přesun dat z zdroje HTTP pomocí různých nástrojů nebo rozhraní API:

- Pomocí Průvodce kopírování dat je nejjednodušší způsob, jak vytvořit kanál. Rychlý postup vytvoření kanálu pomocí Průvodce kopírování dat, najdete v části [kurzu: Vytvoření kanálu pomocí Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md).

- Tyto nástroje můžete také použít k vytvoření kanálu: **webu Azure portal**, **sady Visual Studio**, **prostředí Azure PowerShell**, **Azure Resource Manageru Šablona**, **rozhraní .NET API**, nebo **rozhraní REST API**. Podrobné pokyny k vytvoření kanálu obsahujícího aktivitu kopírování, najdete v článku [kurz aktivity kopírování](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). JSON – ukázky této kopírování dat z HTTP zdroje do služby Azure Blob storage, najdete v části [JSON příklady](#json-examples).

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Následující tabulka popisuje elementy JSON, které jsou specifické pro HTTP propojené služby:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| type | **Typ** musí být vlastnost nastavena na **Http**. | Ano |
| url | Základní adresa URL webového serveru. | Ano |
| authenticationType. | Určuje typ ověřování. Povolené hodnoty jsou **anonymní**, **základní**, **Digest**, **Windows**, a **ClientCertificate**. <br><br> Naleznete v dalších částech tohoto článku pro další vlastnosti a ukázky JSON pro typy ověřování. | Ano |
| enableServerCertificateValidation | Určuje, zda povolit ověření certifikátu serveru SSL, pokud se zdrojový webový server služby protokolu HTTPS. Pokud váš server HTTPS používá certifikát podepsaný svým držitelem, nastavte na **false**. | Ne<br /> (výchozí hodnota je **true**) |
| Název brány | Název instance brány správy dat pro připojení ke zdroji v místním HTTP. | Ano, pokud se kopírují data z místní zdroje HTTP |
| encryptedCredential | Šifrované přihlašovací údaje pro přístup ke koncovému bodu HTTP. Hodnota se automaticky generuje při konfiguraci ověřovací údaje v Průvodci kopírovat nebo s použitím **ClickOnce** dialogové okno. | Ne<br /> (platí jenom v případě, že kopírování dat z místní server HTTP) |

Podrobnosti o nastavení přihlašovacích údajů pro zdroj dat místní HTTP konektoru najdete v tématu [přesun dat mezi místním zdrojům a cloudem pomocí brány správy dat](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Používá ověřování Basic, Digest nebo Windows

Nastavte **authenticationType** k **základní**, **Digest**, nebo **Windows**. Kromě obecných vlastností konektoru HTTP je popsáno v předchozích částech nastavte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| uživatelské jméno | Uživatelské jméno pro použití pro přístup ke koncovému bodu HTTP. | Ano |
| heslo | Heslo pro uživatele (**uživatelské jméno**). | Ano |

**Příklad: Používá ověřování Basic, Digest nebo Windows**

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

### <a name="using-clientcertificate-authentication"></a>Pomocí ověřování ClientCertificate

Chcete-li použít základní ověřování, nastavte **authenticationType** k **ClientCertificate**. Kromě obecných vlastností konektoru HTTP je popsáno v předchozích částech nastavte následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| embeddedCertData | Obsah s kódováním Base64 binární data ze souboru PFX. | Zadejte buď **embeddedCertData** nebo **certThumbprint** |
| certThumbprint | Kryptografický otisk certifikátu, který byl nainstalován na počítači brány úložiště certifikátů. Platí jenom v případě, že kopírování dat z místních zdroje HTTP. | Zadejte buď **embeddedCertData** nebo **certThumbprint** |
| heslo | Heslo, který je spojen s certifikátem. | Ne |

Pokud používáte **certThumbprint** pro ověřování a certifikát, který je nainstalován v osobním úložišti místního počítače, udělte oprávnění ke čtení pro službu brány:

1. Otevřete konzolu Microsoft Management Console (MMC). Přidat **certifikáty** modul snap-in, zaměřuje **místního počítače**.
2. Rozbalte **certifikáty** > **osobní**a pak vyberte **certifikáty**.
3. Klikněte pravým tlačítkem na certifikát z osobního úložiště a pak vyberte **všechny úkoly** >**spravovat soukromé klíče**.
3. Na **zabezpečení** kartu, přidejte uživatelský účet, pod kterým běží služba hostitele brány pro správu dat s přístupem pro čtení k certifikátu.  

**Příklad: Pomocí klientského certifikátu**

Tato propojená služba propojuje svou datovou továrnu místní webový server HTTP. Používá klientský certifikát, který je nainstalován na počítači, který má brána správy dat nainstalovaná.

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

**Příklad: Pomocí klientského certifikátu do souboru**

Tato propojená služba propojuje svou datovou továrnu místní webový server HTTP. Používá soubor certifikátu klienta na počítači, který má brána správy dat nainstalovaná.

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

Některé části souboru JSON datové sady, jako je například struktura, dostupnost a zásady, jsou podobné pro všechny datové sady typy (Azure SQL Database, Azure Blob storage, Azure Table storage).

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, naleznete v tématu [vytváření datových sad](data-factory-create-datasets.md).

**TypeProperties** oddílu se liší pro každý typ datové sady. **TypeProperties** část obsahuje informace o umístění dat v úložišti. **TypeProperties** části datové sady **Http** typ má následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | **Typ** datové sady, musí být nastaveno na **Http**. | Ano |
| relativeUrl | Relativní adresa URL k prostředku, který obsahuje data. Když cestu nezadáte, použije se pouze adresu URL, která je zadána v definici propojené služby. <br><br> K vytvoření dynamické adresy URL, můžete použít [funkce Data Factory a systémové proměnné](data-factory-functions-variables.md). Příklad: **relativeUrl**: **$$Text.Format ("/ Moje/tuto sestavu? měsíc = {0: yyyy} – {0:MM} & fmt = csv", SliceStart)**. | Ne |
| requestMethod | Metoda protokolu HTTP. Povolené hodnoty jsou **získat** a **příspěvek**. | Ne <br />(výchozí hodnota je **získat**) |
| additionalHeaders | Další hlavičky požadavků HTTP. | Ne |
| Includesearchresults: true | Obsah žádosti protokolu HTTP. | Ne |
| formát | Pokud chcete *načtení dat z koncového bodu HTTP jako-je* bez je analýza kódu, přejděte **formátu** nastavení. <br><br> Pokud chcete analyzovat obsah odpovědi HTTP při kopírování, jsou podporovány následující typy formátů: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, a **ParquetFormat**. Další informace najdete v tématu [textový formát](data-factory-supported-file-and-compression-formats.md#text-format), [formátu JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formát Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formát Orc](data-factory-supported-file-and-compression-formats.md#orc-format), a [formát Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |Ne |
| Komprese | Zadejte typ a úroveň komprese pro data. Podporované typy: **GZip**, **Deflate**, **BZip2**, a **ZipDeflate**. Podporované úrovně: **Optimální** a **nejrychlejší**. Další informace najdete v tématu [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Ne |

**Příklad: Pomocí metody GET (výchozí)**

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

**Příklad: Pomocí metody POST**

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

Vlastnosti, jako je název, popis, vstupní a výstupní tabulky a zásady jsou k dispozici pro všechny typy aktivit.

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v tématu [vytváření kanálů](data-factory-create-pipelines.md). 

Vlastnosti, které jsou k dispozici v **typeProperties** části aktivity se liší s jednotlivými typu aktivity. Pro aktivitu kopírování vlastnosti lišit v závislosti na typy zdroje a jímky.

V současné době když zdroj v aktivitě kopírování je **HttpSource** zadejte následující vlastnosti jsou podporovány:

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| httpRequestTimeout | Časový limit ( **TimeSpan** hodnotu) pro požadavek HTTP získat odpověď. Časový limit je získat odpověď, nevypršel časový limit pro čtení dat odpovědi. | Ne<br />(výchozí hodnota: **00: 01:40**) |

## <a name="supported-file-and-compression-formats"></a>Podporované formáty souborů a komprese

Zobrazit [formáty souborů a komprese ve službě Azure Data Factory](data-factory-supported-file-and-compression-formats.md) Další informace.

## <a name="json-examples"></a>Příklady pro JSON

Následující příklady popisují ukázkový JSON definice, které můžete použít k vytvoření kanálu pomocí [webu Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [sady Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), nebo [prostředí Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Příklady ukazují, jak kopírovat data z HTTP zdroje do úložiště objektů Blob v Azure. Nicméně je možné zkopírovat data *přímo* z jakéhokoli zdroje, které chcete některý z jímky [, které jsou podporované](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pomocí aktivity kopírování ve službě Azure Data Factory.

**Příklad: Kopírování dat z HTTP zdroje do úložiště objektů Blob v Azure**

Řešení Data Factory pro tento příklad obsahuje následující entity služby Data Factory:

*   Propojené služby typu [HTTP](#linked-service-properties).
*   Propojené služby typu [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Vstupní hodnota [datovou sadu](data-factory-create-datasets.md) typu [Http](#dataset-properties).
*   Výstup [datovou sadu](data-factory-create-datasets.md) typu [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   A [kanálu](data-factory-create-pipelines.md) s aktivitou kopírování, která používá [HttpSource](#copy-activity-properties) a [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Ukázka kopíruje data z HTTP zdroje do objektu blob Azure každou hodinu. Vlastnostech JSON použitých v těchto ukázkách, jsou popsány v části Ukázky.

### <a name="http-linked-service"></a>HTTP propojené služby

Tento příklad používá službu HTTP propojené s anonymní ověřování. Zobrazit [HTTP propojená služba](#linked-service-properties) pro různé typy ověřování můžete použít.

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

### <a name="azure-storage-linked-service"></a>Propojená služba úložiště Azure

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

Nastavení **externí** k **true** služby Data Factory informuje, že datová sada je externí do služby data factory a není vytvořen aktivitou ve službě data factory.

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

Data se zapisují do nového objektu blob každou hodinu (**frekvence**: **hodinu**, **interval**: **1**).

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

### <a name="pipeline-that-uses-a-copy-activity"></a>Kanál, který využívá aktivitu kopírování

Kanálu obsahujícího aktivitu kopírování, který je nakonfigurován na použití vstupních a výstupních datových sad. Aktivita kopírování je naplánováno spuštění každou hodinu. V definici JSON kanálu **zdroj** je typ nastaven na **HttpSource** a **jímky** je typ nastaven na **BlobSink**.

Pro seznam vlastností, které **HttpSource** podporuje, najdete v článku [HttpSource](#copy-activity-properties).

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
> Pokud chcete namapovat sloupce ze zdrojovou datovou sadu na sloupce z datové sady jímky, najdete v článku [mapování sloupců v datové sadě ve službě Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Výkon a ladění

Další informace o klíčových faktorů, které mají vliv na výkon přesun dat (aktivita kopírování) v Azure Data Factory a různé způsoby, jak optimalizovat, najdete v článku [Průvodce laděním a výkonem aktivity kopírování](data-factory-copy-activity-performance.md).