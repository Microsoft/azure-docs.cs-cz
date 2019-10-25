---
title: Řešení běžných potíží s indexerem vyhledávání
titleSuffix: Azure Cognitive Search
description: Opravte chyby a běžné problémy s indexery ve službě Azure Kognitivní hledání, včetně připojení ke zdroji dat, brány firewall a chybějících dokumentů.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c5a16d957f1e0414f92d0cc03442d88d438e4c92
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793633"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Řešení běžných potíží indexerů v Azure Kognitivní hledání

Indexery můžou být spuštěné v množství problémů při indexování dat do Azure Kognitivní hledání. Mezi hlavní kategorie selhání patří:

* [Připojení ke zdroji dat](#data-source-connection-errors)
* [Zpracování dokumentů](#document-processing-errors)
* [Přijímání dokumentů do indexu](#index-errors)

## <a name="data-source-connection-errors"></a>Chyby připojení ke zdroji dat

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Brána firewall účtu úložiště

Azure Storage poskytuje konfigurovatelnou bránu firewall. Ve výchozím nastavení je brána firewall vypnutá, aby se služba Azure Kognitivní hledání mohla připojit k vašemu účtu úložiště.

V případě povolení brány firewall není k dispozici žádná konkrétní chybová zpráva. Chyby brány firewall obvykle vypadají jako `The remote server returned an error: (403) Forbidden`.

Můžete ověřit, jestli je na [portálu](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)povolená brána firewall. Jediným podporovaným řešením je zakázat bránu firewall tak, že vyberete možnost povolit přístup ze [všech sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Pokud indexer nemá připojen dovednosti _, můžete se pokusit_ [Přidat výjimku](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) pro IP adresy vaší vyhledávací služby. Tento scénář však není podporován a není zaručena funkčnost.

IP adresu vaší vyhledávací služby můžete zjistit pomocí testu jejich plně kvalifikovaného názvu domény (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Cosmos DB

#### <a name="indexing-isnt-enabled"></a>Indexování není povoleno.

Azure Kognitivní hledání má implicitní závislost na Cosmos DB indexování. Pokud automatické indexování v Cosmos DB vypnete, Azure Kognitivní hledání vrátí úspěšný stav, ale index obsahu kontejneru se nezdařil. Pokyny, jak kontrolovat nastavení a zapnout indexování, najdete v tématu [Správa indexování v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Chyby zpracování dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nezpracované nebo nepodporované dokumenty

Indexer objektu BLOB [dokumentuje explicitně podporované formáty dokumentů.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) V některých případech kontejner úložiště objektů BLOB obsahuje nepodporované dokumenty. Jindy může dojít k problematickým dokumentům. Můžete se vyhnout zastavení indexeru na těchto dokumentech [změnou možností konfigurace](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Chybějící obsah dokumentu

Indexer objektů BLOB [vyhledá a extrahuje text z objektů BLOB v kontejneru](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Mezi problémy s extrahováním textu patří:

* Dokument obsahuje pouze naskenované obrázky. Objekty blob ve formátu PDF, které obsahují netextový obsah, jako jsou například naskenované obrázky (JPGs), nepřinesí výsledky standardní kanál indexování objektů BLOB. Pokud máte obsah obrázků s textovými prvky, můžete k vyhledání a extrakci textu použít [hledání rozpoznávání](cognitive-search-concept-image-scenarios.md) .
* Indexer objektů BLOB je nakonfigurovaný jenom na metadata indexu. Pro extrakci obsahu musí být indexer objektů BLOB nakonfigurovaný tak, aby mohl [extrahovat obsah i metadata](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Chyby indexu

### <a name="missing-documents"></a>Chybějící dokumenty

Indexery hledají dokumenty ze [zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source). V některých případech se zdá, že v indexu chybí dokument ze zdroje dat, který by měl být indexován. K těmto chybám může dojít z několika běžných důvodů:

* Dokument nebyl indexován. Na portálu vyhledejte úspěšné spuštění indexeru.
* Po spuštění indexeru se dokument aktualizoval. Pokud je indexer podle [plánu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), bude ho nakonec znovu spustit a vybrat.
* [Dotaz](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) zadaný ve zdroji dat vylučuje dokument. Indexery nemůžou indexovat dokumenty, které nejsou součástí zdroje dat.
* [Mapování polí](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) nebo [rozšíření AI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) změnilo dokument a vypadá jinak, než očekáváte.
* K vyhledání dokumentu použijte [rozhraní API pro vyhledávání v dokumentu](https://docs.microsoft.com/rest/api/searchservice/lookup-document) .
