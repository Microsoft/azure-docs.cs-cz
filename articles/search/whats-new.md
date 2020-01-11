---
title: Oznámení nových funkcí
titleSuffix: Azure Cognitive Search
description: Oznámení nových a vylepšených funkcí, včetně přejmenování služby Azure Search do Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 2627e4be20d80251e3753e46624c58a0c6244aba
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863083"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Kognitivní hledání

Zjistěte, co je nového ve službě. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Nový název služby pro Azure Search

Azure Search se teď přejmenuje na **Azure kognitivní hledání** , aby odráželo rozšířené použití dovedností rozpoznávání a zpracování AI v základních operacích. I když vnímání dovedností přidávají nové funkce, je použití AI naprosto volitelné. Azure Kognitivní hledání bez AI můžete dál používat k vytváření bohatých fulltextových řešení pro vyhledávání v rámci privátního a heterogenního textového obsahu v indexu, který vytvoříte a spravujete v cloudu. 

Verze API, balíčky NuGet, obory názvů a koncové body se nezměnily. Vaše stávající řešení hledání nejsou ovlivněna změnou názvu služby.

## <a name="feature-announcements"></a>Oznámení funkcí

### <a name="january-2020"></a>Leden 2020

+ [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) jsou teď všeobecně dostupné. Pokud používáte REST, můžete k této funkci přistupovat pomocí `api-version=2019-05-06`. Pro spravovaný kód je správný balíček stále [.NET SDK verze 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , i když funkce není ve verzi Preview. 

+ V **rozhraní API-Version = 2019-10 -01-Preview**je teď k dispozici *omezený přístup k IP adresám a soukromým koncovým bodem (Preview)* na koncovém bodu vyhledávací služby. Zabezpečený koncový bod můžete nastavit pomocí nových vlastností **IpRule** a **NetworkRuleSet** ve REST API [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) správu. Další informace o verzích rozhraní API a regionální dostupnosti najdete v tématu [Jak používat REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>Prosinec 2019

+ [Vytvořit aplikaci (Preview)](search-create-app-portal.md) je nový průvodce na portálu, který GENERUJE soubor HTML ke stažení. Soubor se dodává se skriptem Embedded, který vykreslí provozní webovou aplikaci ve stylu "localhost", která je svázána s indexem ve vaší vyhledávací službě. Stránky lze v průvodci konfigurovat a mohou obsahovat panel hledání, oblast výsledků, navigaci bočním panelem a podporu dotazů typeahead. Můžete upravit HTML v offline režimu pro rozšiřování nebo přizpůsobení pracovního postupu nebo vzhledu.

+ [Vytvoření privátního koncového bodu pro zabezpečená připojení (Preview)](service-create-private-endpoint.md) vysvětluje, jak nastavit privátní propojení pro zabezpečená připojení k vaší vyhledávací službě. Tato funkce ve verzi Preview je dostupná po podání žádosti a jako součást řešení používá [privátní odkaz Azure](../private-link/private-link-overview.md) a [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) .

### <a name="november-2019---ignite-conference"></a>Listopadu 2019 – konference Ignite

+ [Přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md) přidá do kanálu pro rozšíření ukládání do mezipaměti a statefullness, aby bylo možné pracovat na konkrétních krocích nebo fázích, aniž by došlo ke ztrátě již zpracovávaného obsahu. Předchozí Změna kanálu rozšíření vyžadovala úplné opětovné sestavení. Při přírůstkovém obohacení se zachová výstup nákladné analýzy, zejména analýza obrazu.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrakce dokumentu (ve verzi Preview)](cognitive-search-skill-document-extraction.md) je vnímání dovedností, která se používá při indexování, která umožňuje extrakci obsahu souboru v rámci dovednosti. Dříve došlo k odhalení dokumentu pouze před dovednosti prováděním. Při přidání této dovednosti můžete tuto operaci provést i v rámci provádění dovednosti.

+ [Překlad textu (ve verzi Preview)](cognitive-search-skill-text-translation.md) je vnímání dovedností použitou při indexování, která vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka.

+ [Šablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) můžou rychlé zprovoznění vaše vizualizace a analýzu obohaceného obsahu ve znalostní bázi Power BI Desktop. Tato šablona je určená pro projekce tabulky Azure vytvořené prostřednictvím [Průvodce importem dat](knowledge-store-create-portal.md).

+ V indexerech jsou nyní podporovány [Azure Data Lake Storage Gen2 (Preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md)a [Cosmos DB rozhraní API Cassandra (Preview)](search-howto-index-cosmosdb.md) . Můžete se zaregistrovat pomocí [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Po přijetí do programu Preview obdržíte e-mail s potvrzením.

### <a name="july-2019"></a>Červenec 2019

+ Obecně dostupné v [cloudu Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Aktualizace služby

[Oznámení o aktualizacích služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure kognitivní hledání najdete na webu Azure.