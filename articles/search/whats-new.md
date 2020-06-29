---
title: Oznámení nových funkcí
titleSuffix: Azure Cognitive Search
description: Oznámení nových a vylepšených funkcí, včetně přejmenování služby Azure Search do Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 06/23/2020
ms.openlocfilehash: 086e3cf71012f168851df1c85b19689294fe6302
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85515736"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Kognitivní hledání

Zjistěte, co je nového ve službě. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

## <a name="feature-announcements"></a>Oznámení funkcí

### <a name="june-2020"></a>Červen 2020

Azure Machine Learning dovednost je nový typ dovednosti pro integraci koncového bodu Inferencing z Azure Machine Learning. Prostředí portálu podporuje zjišťování a integraci Azure Machine Learningho koncového bodu v rámci Kognitivní hledání dovednosti. Zjišťování vyžaduje, aby vaše Kognitivní hledání a služby Azure ML byly nasazeny ve stejném předplatném. Začněte s [tímto kurzem](cognitive-search-tutorial-aml-custom-skill.md).

### <a name="may-2020-microsoft-build"></a>Květen 2020 (Microsoft Build)

+ Funkce [relace ladění](cognitive-search-debug-session.md) je teď ve verzi Preview. Relace ladění poskytují rozhraní založené na portálu pro zkoumání a řešení problémů s dovednosti. Opravy vytvořené v relaci ladění se dají uložit do produkčního dovednostiu. Začněte s [tímto kurzem](cognitive-search-tutorial-debug-sessions.md).

+ Mezi vylepšení zabezpečení patří možnost [nastavit privátní koncový bod hledání (Preview)](service-create-private-endpoint.md) , který je nepřístupný na veřejném Internetu. Můžete také [nakonfigurovat pravidla protokolu IP pro podporu integrovaného bezpečnostního brány firewall (Preview)](service-configure-firewall.md).

+ K nastavení připojení ke zdroji dat Azure pro indexování použijte [systémově spravovanou identitu (Preview)](search-howto-managed-identities-data-sources.md) . Platí pro [indexery](search-indexer-overview.md) , které ingestují obsah ze zdrojů dat Azure, jako jsou Azure SQL Database, Azure Cosmos DB a Azure Storage.

+ Změňte základ pro vypočítání výsledků hledání z horizontálních oddílů na All-horizontálních oddílů pomocí parametrů [scoringStatistics = Global](index-similarity-and-scoring.md#scoring-statistics) a SessionID.

### <a name="march-2020"></a>Březen 2020

+ Funkce [Native BLOB unnative (Preview)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) znamená, že indexovací člen služby Azure Blob Storage v Azure kognitivní hledání rozpozná objekty blob, které jsou ve stavu undeleteded, a během indexování odebere odpovídající hledaný dokument.

+ Nové stabilní [REST API pro správu (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) je teď k dispozici. 

### <a name="february-2020"></a>Únor 2020

+ [Detekce PII (Preview)](cognitive-search-skill-pii-detection.md) je způsob rozpoznávání, který se používá při indexování, která extrahuje identifikovatelné osobní údaje ze vstupního textu a poskytuje možnost jejich maskování z tohoto textu různými způsoby.

+ [Vlastní vyhledávání entit (Preview)](cognitive-search-skill-custom-entity-lookup.md ) vyhledává text z vlastního uživatelsky definovaného seznamu slov a frází. Pomocí tohoto seznamu jsou všechny dokumenty označeny všemi vyhovujícími entitami. Dovednost také podporuje stupeň přibližné shody, které lze použít pro hledání shod, které jsou podobné, ale nejsou zcela přesné. 

### <a name="january-2020"></a>Leden 2020

+ [Šifrovací klíče spravované zákazníkem](search-security-manage-encryption-keys.md) jsou teď všeobecně dostupné. Pokud používáte REST, můžete k této funkci přistupovat pomocí `api-version=2019-05-06` . Pro spravovaný kód je správný balíček stále [.NET SDK verze 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , i když funkce není ve verzi Preview. 

+ Privátní přístup k vyhledávací službě je k dispozici prostřednictvím dvou mechanismů v současnosti ve verzi Preview:

  + Přístup ke konkrétním IP adresám můžete omezit pomocí REST API pro správu `api-version=2019-10-01-Preview` a vytvořit službu. Rozhraní API ve verzi Preview má nové vlastnosti **IpRule** a **NetworkRuleSet** v [rozhraní CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Tato funkce ve verzi Preview je dostupná ve vybraných oblastech. Další informace najdete v tématu [Jak používat REST API pro správu](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Aktuálně dostupné prostřednictvím omezeného přístupu ve verzi Preview můžete zřídit Azure Search službu, která podporuje privátní koncový bod Azure pro připojení od klientů ve stejné virtuální síti. Další informace najdete v tématu [Vytvoření privátního koncového bodu pro zabezpečené připojení](service-create-private-endpoint.md).

### <a name="december-2019"></a>Prosinec 2019

+ [Vytvořit aplikaci (Preview)](search-create-app-portal.md) je nový průvodce na portálu, který GENERUJE soubor HTML ke stažení. Soubor se dodává se skriptem Embedded, který vykreslí provozní webovou aplikaci ve stylu "localhost", která je svázána s indexem ve vaší vyhledávací službě. Stránky lze v průvodci konfigurovat a mohou obsahovat panel hledání, oblast výsledků, navigaci bočním panelem a podporu dotazů typeahead. Můžete upravit HTML v offline režimu pro rozšiřování nebo přizpůsobení pracovního postupu nebo vzhledu.

+ [Vytvoření privátního koncového bodu pro zabezpečená připojení (Preview)](service-create-private-endpoint.md) vysvětluje, jak nastavit privátní propojení pro zabezpečená připojení k vaší vyhledávací službě. Tato funkce ve verzi Preview je dostupná po podání žádosti a jako součást řešení používá [privátní odkaz Azure](../private-link/private-link-overview.md) a [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) .

### <a name="november-2019---ignite-conference"></a>Listopadu 2019 – konference Ignite

+ [Přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md) přidá do kanálu pro rozšíření ukládání do mezipaměti a statefullness, aby bylo možné pracovat na konkrétních krocích nebo fázích, aniž by došlo ke ztrátě již zpracovávaného obsahu. Předchozí Změna kanálu rozšíření vyžadovala úplné opětovné sestavení. Při přírůstkovém obohacení se zachová výstup nákladné analýzy, zejména analýza obrazu.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Extrakce dokumentu (ve verzi Preview)](cognitive-search-skill-document-extraction.md) je vnímání dovedností, která se používá při indexování, která umožňuje extrakci obsahu souboru v rámci dovednosti. Dříve došlo k odhalení dokumentu pouze před dovednosti prováděním. Při přidání této dovednosti můžete tuto operaci provést i v rámci provádění dovednosti.

+ [Překlad textu](cognitive-search-skill-text-translation.md) je vnímání znalostí používané při indexování, které vyhodnocuje text a pro každý záznam vrátí text přeložený do zadaného cílového jazyka.

+ [Šablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) můžou rychlé zprovoznění vaše vizualizace a analýzu obohaceného obsahu ve znalostní bázi Power BI Desktop. Tato šablona je určená pro projekce tabulky Azure vytvořené prostřednictvím [Průvodce importem dat](knowledge-store-create-portal.md).

+ V indexerech jsou nyní podporovány [Azure Data Lake Storage Gen2 (Preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md)a [Cosmos DB rozhraní API Cassandra (Preview)](search-howto-index-cosmosdb.md) . Můžete se zaregistrovat pomocí [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Po přijetí do programu Preview obdržíte e-mail s potvrzením.

### <a name="july-2019"></a>Červenec 2019

+ Obecně dostupné v [cloudu Azure Government](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nový název služby

Azure Search se teď přejmenuje na **Azure kognitivní hledání** , aby odráželo rozšířené (zatím volitelné) používání vnímání a zpracování AI v základních operacích. Verze API, balíčky NuGet, obory názvů a koncové body se nezměnily. Nová a stávající řešení hledání nejsou ovlivněna změnou názvu služby.

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure kognitivní hledání najdete na webu Azure.