---
title: Řešení běžných potíží hledání indexer – Azure Search
description: Opravte chyby a problémy s indexery ve službě Azure Search, včetně připojení ke zdroji dat, brány firewall a chybějící dokumenty.
author: mgottein
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 1cb3260fa11354de963318a023fec912d082eae4
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653401"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-search"></a>Řešení běžných potíží indexeru ve službě Azure Search

Indexery můžete spustit do celé řady důvodů, názvy při indexování dat do Azure Search. Hlavní kategorie selhání patří:

* [Připojení ke zdroji dat](#data-source-connection-errors)
* [Zpracování dokumentů](#document-processing-errors)
* [Ingestování dokumentů do indexu](#index-errors)

## <a name="data-source-connection-errors"></a>Chyby připojení zdroje dat

### <a name="blob-storage"></a>Blob Storage

#### <a name="storage-account-firewall"></a>Brány firewall na účet úložiště

Azure Storage poskytuje konfigurovat bránu firewall. Brána firewall je ve výchozím nastavení zakázána proto Azure Search můžete připojit k vašemu účtu úložiště.

Neexistuje žádná konkrétní chybová zpráva, pokud je povolena brána firewall. Obvykle, brána firewall chyby vypadat `The remote server returned an error: (403) Forbidden`.

Můžete ověřit, že brána firewall je povolena v [portál](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal). Pouze podporované alternativním řešením je zakázat bránu firewall, že vyberete možnost povolit přístup z ["Všechny sítě"](https://docs.microsoft.com/azure/storage/common/storage-network-security#azure-portal).

Pokud indexer nemá žádné připojené dovednosti, můžete _může_ pokusí [přidat výjimku](https://docs.microsoft.com/azure/storage/common/storage-network-security#managing-ip-network-rules) pro IP adresy vaší služby search. Ale tento scénář není podporován a nemusí fungovat.

IP adresu vaší služby search můžete zjistit pomocí příkazu ping jeho plně kvalifikovaný název domény (`<your-search-service-name>.search.windows.net`).

### <a name="cosmos-db"></a>Databáze Cosmos

#### <a name="indexing-isnt-enabled"></a>Indexování není povoleno.

Služba Azure Search má implicitní závislost na indexování Cosmos DB. Pokud vypnete automatické indexování ve službě Cosmos DB, Azure Search vrací úspěšný stav, ale nedokáže index obsahu kontejneru. Pokyny o tom, jak zkontrolovat nastavení a zapnout indexování najdete v tématu [správy indexování ve službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Zpracování chyb dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nezpracované nebo nepodporovaný dokumentů

Indexování objektů blob [dokumenty, které dokumentu formáty jsou explicitně podporovány.](search-howto-indexing-azure-blob-storage.md#supported-document-formats). V některých případech kontejneru úložiště objektů blob obsahuje nepodporovaný dokumenty. Jindy může být problematické dokumenty. Probíhá zastavení indexer na tyto dokumenty podle se můžete vyhnout [změnou konfigurace možností](search-howto-indexing-azure-blob-storage.md#dealing-with-errors):

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

Indexování objektů blob [vyhledává a extrahuje text z objektů BLOB v kontejneru](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Některé problémy se extrahují text patří:

* Dokument obsahuje pouze naskenované bitových kopií. Objekty BLOB PDF, které mají netextový obsah, jako jsou obrázky prohledaná formátu JPG (využívá), nemusíte vytvářet výsledky v kanál indexování objektů blob standardní. Pokud máte obsahu bitové kopie s elementy textu, můžete použít [kognitivního vyhledávání](cognitive-search-concept-image-scenarios.md) vyhledat a extrahovat text.
* Indexování objektů blob je nakonfigurován na pouze metadata indexu. Extrahovat obsah, musí být nakonfigurován indexeru blob pro [extrahovat obsah a metadata](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Index chyby

### <a name="missing-documents"></a>Chybějící dokumenty

Indexery najít dokumenty [zdroj dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Někdy se pravděpodobně chybí indexu zobrazí dokument ze zdroje dat, které by měl indexovat. Existuje několik běžných příčin, že se může stát v těchto chyb:

* Dokument nebyl indexovat. Podívejte se na portál pro spuštění úspěšné indexeru.
* Dokument byl aktualizován po spuštění indexeru. Pokud je vaše indexer na [plán](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), bude nakonec spusťte znovu a získaly dokumentu.
* [Dotazu](https://docs.microsoft.com/rest/api/searchservice/create-data-source#request-body-syntax) zadané v datech zdrojové vyloučí dokumentu. Indexery nemůže indexovat dokumenty, které nejsou součástí datového zdroje.
* [Mapování polí](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) nebo [kognitivního vyhledávání](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) změnily dokumentu a vypadá jinak, než byste očekávali.
* Použití [vyhledávání dokumentů API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) najít dokumentu.
