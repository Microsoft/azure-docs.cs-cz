---
title: Řešení běžných potíží s indexerem vyhledávání – Azure Search
description: Opravte chyby a běžné problémy s Indexery v Azure Search, včetně připojení ke zdroji dat, brány firewall a chybějících dokumentů.
author: mgottein
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.openlocfilehash: 4692be287e9b38cf116107d2e7c1043f23a6b34b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640603"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Řešení běžných potíží indexeru v Azure Search

Indexery mohou být spuštěny v množství problémů při indexování dat do Azure Search. Mezi hlavní kategorie selhání patří:

* [Připojení ke zdroji dat](#data-source-connection-errors)
* [Zpracování dokumentů](#document-processing-errors)
* [Přijímání dokumentů do indexu](#index-errors)

## <a name="data-source-connection-errors"></a>Chyby připojení ke zdroji dat

### <a name="blob-storage"></a>Úložiště objektů blob

#### <a name="storage-account-firewall"></a>Brána firewall účtu úložiště

Azure Storage poskytuje konfigurovatelnou bránu firewall. Ve výchozím nastavení je brána firewall zakázaná, takže Azure Search se může připojit k vašemu účtu úložiště.

V případě povolení brány firewall není k dispozici žádná konkrétní chybová zpráva. Chyby brány firewall obvykle vypadají jako `The remote server returned an error: (403) Forbidden`.

Můžete ověřit, jestli je na [portálu](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal)povolená brána firewall. Jediným podporovaným řešením je zakázat bránu firewall tak, že vyberete možnost povolit přístup ze [všech sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Pokud indexer nemá připojen dovednosti, můžete se pokusit [Přidat výjimku](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) pro IP adresy vaší vyhledávací služby. Tento scénář však není podporován a není zaručena funkčnost.

IP adresu vaší vyhledávací služby můžete zjistit pomocí testu svého plně kvalifikovaného názvu domény (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Databáze Cosmos

#### <a name="indexing-isnt-enabled"></a>Indexování není povoleno.

Azure Search má implicitní závislost na Cosmos DB indexování. Vypnete-li automatické indexování v Cosmos DB, Azure Search vrátí úspěšný stav, ale index obsahu kontejneru se nezdařil. Pokyny, jak kontrolovat nastavení a zapnout indexování, najdete v tématu [Správa indexování v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Chyby zpracování dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nezpracované nebo nepodporované dokumenty

Indexer objektu BLOB [dokumentuje explicitně podporované formáty dokumentů.](search-howto-indexing-azure-blob-storage.md#supported-document-formats) V některých případech kontejner úložiště objektů BLOB obsahuje nepodporované dokumenty. Jindy může dojít k problematickým dokumentům. Můžete se vyhnout zastavení indexeru na těchto dokumentech [změnou možností konfigurace](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

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
* [Mapování polí](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) nebo [hledání rozpoznávání](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) se změnilo v dokumentu a vypadá jinak, než očekáváte.
* K vyhledání dokumentu použijte [rozhraní API pro vyhledávání v dokumentu](https://docs.microsoft.com/rest/api/searchservice/lookup-document) .
