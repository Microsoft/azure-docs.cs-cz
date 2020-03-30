---
title: Poradce při potížích s běžným indexerem hledání
titleSuffix: Azure Cognitive Search
description: Opravte chyby a běžné problémy s indexery v Azure Cognitive Search, včetně připojení ke zdroji dat, brány firewall a chybějících dokumentů.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1e3692920c35a6965a23c0305aeeebfc80505d85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190934"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Řešení problémů s běžným indexerem v Azure Cognitive Search

Indexery můžete spustit do řady problémů při indexování dat do Azure Cognitive Search. Mezi hlavní kategorie selhání patří:

* [Připojení ke zdroji dat nebo jiným prostředkům](#connection-errors)
* [Zpracování dokumentů](#document-processing-errors)
* [Požití dokumentu do indexu](#index-errors)

## <a name="connection-errors"></a>Chyby připojení

> [!NOTE]
> Indexery mají omezenou podporu pro přístup ke zdrojům dat a dalším prostředkům, které jsou zabezpečené mechanismy zabezpečení sítě Azure. V současné době mohou indexery přistupovat ke zdrojům dat pouze prostřednictvím odpovídajících mechanismů omezení rozsahu IP adres nebo pravidel nsg, pokud je to možné. Podrobnosti pro přístup ke každému podporovanému zdroji dat naleznete níže.
>
> IP adresu vaší vyhledávací služby zjistíte pomocí příkazu ping na `<your-search-service-name>.search.windows.net`její plně kvalifikovaný název domény (např.).
>
> Rozsah IP adres `AzureCognitiveSearch` [servisní značky](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) můžete zjistit buď pomocí [souborů JSON ke stažení,](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files) nebo pomocí [rozhraní API pro zjišťování výrobních značek](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview). Rozsah IP adres se aktualizuje každý týden.

### <a name="configure-firewall-rules"></a>Konfigurace pravidel brány firewall

Azure Storage, CosmosDB a Azure SQL poskytují konfigurovatelnou bránu firewall. Pokud je povolena brána firewall, nezobrazí se žádná konkrétní chybová zpráva. Chyby brány firewall jsou obvykle `The remote server returned an error: (403) Forbidden` `Credentials provided in the connection string are invalid or have expired`obecné a vypadají jako nebo .

Existují 2 možnosti umožňující indexerům přístup k těmto prostředkům v takové instanci:

* Zakažte bránu firewall povolením přístupu ze **všech sítí** (pokud je to možné).
* Případně můžete povolit přístup k IP adrese vyhledávací služby a `AzureCognitiveSearch` rozsahu IP adresy [značky služby](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) v pravidlech brány firewall vašeho prostředku (omezení rozsahu IP adres).

Podrobnosti pro konfiguraci omezení rozsahu IP adres pro každý typ zdroje dat naleznete z následujících odkazů:

* [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Cosmos DB](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)

* [Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#create-and-manage-ip-firewall-rules)

**Omezení**: Jak je uvedeno v dokumentaci výše pro Azure Storage, omezení rozsahu IP adres bude fungovat jenom v případě, že vaše vyhledávací služba a účet úložiště jsou v různých oblastech.

Funkce Azure (které by se daly použít jako [dovednost vlastního webového rozhraní )](cognitive-search-custom-skill-web-api.md)také podporují omezení IP [adres](https://docs.microsoft.com/azure/azure-functions/ip-addresses#ip-address-restrictions). Seznam adres IP, které chcete konfigurovat, by byla ADRESA IP vyhledávací `AzureCognitiveSearch` služby a rozsah IP adres servisní značky.

Podrobnosti pro přístup k datům na serveru SQL na virtuálním počítači Azure jsou popsané [zde](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurace pravidel skupiny zabezpečení sítě (NSG)

Při přístupu k datům ve spravované instanci SQL nebo při použití virtuálního počítače Azure jako identifikátor URI webové služby pro [vlastní webové rozhraní API dovednosti](cognitive-search-custom-skill-web-api.md), zákazníci nemusí být zabývající se konkrétní IP adresy.

V takových případech lze virtuální počítač Azure nebo spravovaná instance SQL nakonfigurovat tak, aby se nachovali ve virtuální síti. Skupinu zabezpečení sítě pak lze nakonfigurovat tak, aby filtrovala typ síťového provozu, který může proudit do podsítí virtuálních sítí a síťových rozhraní a z těchto sítí.

Výrobní `AzureCognitiveSearch` číslo lze přímo použít v [příchozích pravidel nsg](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#work-with-security-rules) bez nutnosti vyhledat rozsah ip adres.

Další podrobnosti pro přístup k datům ve spravované instanci SQL jsou uvedeny [zde](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexování" není povoleno

Azure Cognitive Search má implicitní závislost na indexování Cosmos DB. Pokud vypnete automatické indexování v Cosmos DB, Azure Cognitive Search vrátí úspěšný stav, ale nepodaří indexovat obsah kontejneru. Pokyny ke kontrole nastavení a zapnutí indexování najdete v tématu [Správa indexování v Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-azure-portal).

## <a name="document-processing-errors"></a>Chyby zpracování dokumentů

### <a name="unprocessable-or-unsupported-documents"></a>Nezpracovatelné nebo nepodporované dokumenty

Indexátor objektů blob [dokumentuje, které formáty dokumentů jsou explicitně podporovány.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). . Kontejner úložiště objektů blob někdy obsahuje nepodporované dokumenty. Jindy mohou existovat problematické dokumenty. Vyhněte se zastavení indexeru v těchto dokumentech [změnou možností konfigurace](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

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

Indexer objektů blob [vyhledá a extrahuje text z objektů BLOB v kontejneru](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Některé problémy s extrahování textu patří:

* Dokument obsahuje pouze naskenované obrázky. Objekty BLOB PDF, které mají netextový obsah, například naskenované obrázky (JPEG), nevytvářejí výsledky ve standardním kanálu indexování objektů blob. Pokud máte obsah obrazu s textovými prvky, můžete použít [kognitivní vyhledávání](cognitive-search-concept-image-scenarios.md) k vyhledání a extrahování textu.
* Indexer objektů blob je nakonfigurovaný pouze na indexová metadata. Chcete-li extrahovat obsah, musí být indexer objektů blob nakonfigurován tak, aby [extrahoval obsah i metadata](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed):

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

Indexery nacházejí dokumenty ze [zdroje dat](https://docs.microsoft.com/rest/api/searchservice/create-data-source). Někdy se zdá, že v indexu chybí dokument ze zdroje dat, který měl být indexován. Existuje několik běžných důvodů, proč k těmto chybám může dojít:

* Dokument nebyl indexován. Zkontrolujte, zda portál pro úspěšné spuštění indexeru.
* Dokument byl aktualizován po spuštění indexeru. Pokud je indexer v [plánu](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-schedule), nakonec se znovu spustí a vyzvedne dokument.
* [Dotaz](/rest/api/searchservice/create-data-source) zadaný ve zdroji dat vylučuje dokument. Indexery nemohou indexovat dokumenty, které nejsou součástí zdroje dat.
* [Mapování polí](https://docs.microsoft.com/rest/api/searchservice/create-indexer#fieldmappings) nebo [obohacení ai](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) změnily dokument a vypadá jinak, než očekáváte.
* K vyhledání dokumentu použijte [rozhraní API vyhledávacího dokumentu.](https://docs.microsoft.com/rest/api/searchservice/lookup-document)
