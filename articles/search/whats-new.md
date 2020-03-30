---
title: Oznámení o nových funkcích
titleSuffix: Azure Cognitive Search
description: Oznámení o nových a vylepšených funkcích, včetně přejmenování služby Azure Search na Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: 475f89fc5b33948864fd83c39ee8058ab6908cad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247193"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Cognitive Search

Přečtěte si, co je ve službě nového. Záložka tuto stránku udržovat aktuální informace o službě.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nový název služby

Azure Search se teď přejmenuje na **Azure Cognitive Search** tak, aby odráželo rozšířené (ale volitelné) využití kognitivních dovedností a zpracování ai v základních operacích. Verze rozhraní API, balíčky NuGet, obory názvů a koncové body se nemění. Změna názvu služby neovlivní nová a existující řešení vyhledávání.

## <a name="feature-announcements"></a>Oznámení o funkcích

### <a name="march-2020"></a>Březen 2020

+ [Nativní odstranění měkkým odstraněním objektu blob (preview)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) znamená, že indexer úložiště objektů blob Azure v Azure Cognitive Search rozpozná objekty BLOB, které jsou v obnovitelném odstraněném stavu, a během indexování odebere odpovídající dokument hledání.

+ Nyní je k dispozici nové stabilní [rozhraní REST API pro správu (2020-03-13).](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) 

### <a name="february-2020"></a>Únor 2020

+ [PiI Detekce (náhled)](cognitive-search-skill-pii-detection.md) je kognitivní dovednost používaná při indexování, která extrahuje osobně identifikovatelné informace ze vstupního textu a poskytuje možnost maskovat z tohoto textu různými způsoby.

+ [Vlastní vyhledávání entit (náhled)](cognitive-search-skill-custom-entity-lookup.md ) hledá text z vlastního, uživatelem definovaného seznamu slov a frází. Pomocí tohoto seznamu označí všechny dokumenty odpovídajícími entitami. Dovednost také podporuje stupeň fuzzy odpovídající, které lze použít k nalezení shody, které jsou podobné, ale ne zcela přesné. 

### <a name="january-2020"></a>Leden 2020

+ [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) jsou nyní obecně dostupné. Pokud používáte rest, můžete k `api-version=2019-05-06`této funkci přistupovat pomocí aplikace . Pro spravovaný kód je správný balíček stále [.NET SDK verze 8.0-preview,](search-dotnet-sdk-migration-version-9.md) i když je funkce mimo náhled. 

+ Soukromý přístup k vyhledávací službě je k dispozici prostřednictvím dvou mechanismů, které jsou aktuálně ve verzi preview:

  + Můžete omezit přístup k určité IP adresy pomocí `api-version=2019-10-01-Preview` rozhraní REST API pro správu k vytvoření služby. Rozhraní PREVIEW API má nové vlastnosti **IpRule** a **NetworkRuleSet** v [rozhraní CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Tato funkce náhledu je k dispozici ve vybraných oblastech. Další informace naleznete [v tématu Jak používat rozhraní REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + V současné době je k dispozici prostřednictvím náhledu s omezeným přístupem, můžete zřídit službu Azure Search, která podporuje Azure Private Endpoint pro připojení od klientů ve stejné virtuální síti. Další informace naleznete [v tématu Vytvoření soukromého koncového bodu pro zabezpečené připojení](service-create-private-endpoint.md).

### <a name="december-2019"></a>Prosinec 2019

+ [Vytvořit aplikaci (náhled)](search-create-app-portal.md) je nový průvodce na portálu, který generuje soubor HTML ke stažení. Soubor je dodáván s vloženým skriptem, který vykresluje provozní webovou aplikaci ve stylu localhost, vázanou na index ve vyhledávací službě. Stránky lze konfigurovat v průvodci a mohou obsahovat panel hledání, oblast výsledků, navigaci na postranním panelu a podporu dotazů dopředného psaní. Kód HTML můžete upravit offline a rozšířit nebo přizpůsobit pracovní postup nebo vzhled.

+ [Vytvořte soukromý koncový bod pro zabezpečená připojení (náhled)](service-create-private-endpoint.md) vysvětluje, jak nastavit privátní odkaz pro zabezpečené připojení k vyhledávací službě. Tato funkce náhledu je k dispozici na vyžádání a používá [Azure Private Link](../private-link/private-link-overview.md) a Azure Virtual [Network](../virtual-network/virtual-networks-overview.md) jako součást řešení.

### <a name="november-2019---ignite-conference"></a>Listopad 2019 - Ignite konference

+ [Přírůstkové obohacení (náhled)](cognitive-search-incremental-indexing-conceptual.md) přidá ukládání do mezipaměti a stavufullness do kanálu obohacení, takže můžete pracovat na konkrétní kroky nebo fáze bez ztráty obsahu, který je již zpracován. Dříve každá změna kanálu obohacení vyžadovala úplné sestavení. Při postupném obohacení je zachován výstup nákladné analýzy, zejména analýzy obrazu.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrakce dokumentu (náhled)](cognitive-search-skill-document-extraction.md) je kognitivní dovednost používaná při indexování, která umožňuje extrahovat obsah souboru z v rámci sady dovedností. Dříve došlo k prolomení dokumentu pouze před provedením skillset. S přidáním této dovednosti můžete také provést tuto operaci v rámci provádění skillset.

+ [Překlad textu](cognitive-search-skill-text-translation.md) je kognitivní dovednost používaná při indexování, která vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka.

+ [Šablony Power BI můžou](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) nastartovat vizualizace a analýzu obohaceného obsahu v úložišti znalostí na ploše Power BI. Tato šablona je určena pro projekce tabulek Azure vytvořené pomocí [Průvodce importem dat](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md)a [Rozhraní API Cosmos DB Cassandra (preview)](search-howto-index-cosmosdb.md) jsou teď podporované v indexerech. Můžete se zaregistrovat pomocí [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Jakmile budete přijati do programu preview, obdržíte potvrzovací e-mail.

### <a name="july-2019"></a>Červenec 2019

+ Obecně dostupné v [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizaci služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure Cognitive Search najdete na webu Azure.