---
title: Co je nového v Azure Kognitivní hledání
description: Oznámení nových a vylepšených funkcí, včetně přejmenování služby Azure Search do Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: ddf418c90857b5bb7eca97eb2c6943cc93fce9ab
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518921"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Kognitivní hledání

Zjistěte, co je nového ve službě. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu.

## <a name="september-2020"></a>Září 2020

Vytvořte v Azure Active Directory identitu pro vyhledávací službu a pak pomocí oprávnění Azure RBAC Udělte identitě oprávnění jen pro čtení ke zdrojům dat Azure. Volitelně můžete zvolit možnost [výjimky důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md) , pokud pravidla protokolu IP nejsou možností.


|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|------------------------------|----------|-------------|---------------|
| [Identita spravované služby](search-howto-managed-identities-data-sources.md) | Indexery, zabezpečení | Vytvořte v Azure Active Directory identitu pro vyhledávací službu a pak pomocí oprávnění Azure RBAC udělte přístup ke zdrojům dat Azure. Tento přístup eliminuje nutnost zadání přihlašovacích údajů v připojovacím řetězci. <br><br>Další způsob použití identity spravované služby je prostřednictvím [výjimky důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md) , pokud pravidla protokolu IP nejsou možností. | Všeobecně k dispozici. Přístup k této funkci při použití portálu nebo [Vytvoření zdroje dat (REST)](/rest/api/searchservice/create-data-source) s rozhraním API-Version = 2020-06-30. |
| [Odchozí požadavky pomocí privátního odkazu](search-indexer-howto-access-private.md) | Indexery, zabezpečení | Vytvoření sdíleného prostředku privátního propojení, který mohou indexery použít při přístupu k prostředkům Azure zabezpečených pomocí privátního propojení Azure. Další informace o všech způsobech, kterými můžete zabezpečit připojení indexeru, najdete v tématu [přístup k obsahu chráněnému funkcemi zabezpečení sítě Azure pomocí indexeru](search-indexer-securing-resources.md). | Všeobecně k dispozici. Přístup k této funkci při použití portálu nebo [sdíleného prostředku privátního propojení](/rest/api/searchmanagement/sharedprivatelinkresources) s rozhraním API-Version = 2020-08-01. |
| [REST API správy (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | REST | Nová stabilní REST API přidává podporu pro vytváření sdílených prostředků privátních propojení. | Všeobecně k dispozici. |
| [REST API správy (2020-08-01 – Preview)](/rest/api/searchmanagement/management-api-versions) | REST | Přidá prostředek sdíleného privátního propojení pro databáze Azure Functions a Azure SQL pro MySQL. | Verze Public Preview. |
| [Sada Management .NET SDK 4,0](/dotnet/api/overview/azure/search/management) | .NET SDK | Aktualizace sady Azure SDK pro sadu Management SDK, cílené REST API verze 2020-08-01. | Všeobecně k dispozici. |

## <a name="august-2020"></a>Srpen 2020

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [dvojité šifrování](search-security-overview.md#encryption) | Zabezpečení | Povolte dvojité šifrování ve vrstvě úložiště nakonfigurováním šifrování CMK (Customer-Managed Key) pro nové vyhledávací služby. Vytvořte novou službu, [nakonfigurujte a používejte klíče spravované zákazníkem](search-security-manage-encryption-keys.md) pro indexy nebo mapy synonym a využijte při šifrování tohoto obsahu dvojité šifrování. | Všeobecně dostupná pro všechny služby vyhledávání vytvořené 1. srpna 2020 v těchto oblastech: Západní USA 2, Východní USA, Střed USA – jih, US Gov – Virginie US Gov – Arizona. K vytvoření služby použijte portál, rozhraní REST API pro správu nebo sady SDK. |

## <a name="july-2020"></a>Červenec 2020

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [ Klientská knihovnaAzure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) | Sada Azure SDK pro .NET | Klientská knihovna .NET vydaná týmem Azure SDK navržená tak, aby byla konzistentní s jinými klientskými knihovnami .NET. <br/><br/>Verze 11 cílí na rozhraní REST API pro hledání verze = 2020-06-30, ale zatím nepodporuje znalostní bázi Knowledge Store, geoprostorové typy ani [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder). <br/><br/>Další informace najdete v tématu  [rychlý Start: vytvoření indexu](search-get-started-dotnet.md) a [upgrade na Azure.Search.Documents (V11)](search-dotnet-sdk-migration-version-11.md). | Všeobecně k dispozici. </br> Nainstalujte [ balíčekAzure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/) z NuGet. |
| [ Klientská knihovnaazure.search.documents](/python/api/overview/azure/search-documents-readme)  | Azure SDK pro Python| Klientská knihovna Python vydaná týmem Azure SDK navržená tak, aby byla konzistentní s jinými klientskými knihovnami Pythonu. <br/><br/>Verze 11 cílí na rozhraní REST API pro hledání verze = 2020-06-30. | Všeobecně k dispozici. </br> Nainstalujte [balíček Azure-Search-Documents](https://pypi.org/project/azure-search-documents/) z PyPI. |
| [@azure/search-documents Klientská knihovna](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK for JavaScript | Klientská knihovna pro JavaScript vydaná týmem Azure SDK navržená tak, aby byla konzistentní s jinými klientskými knihovnami JavaScript. <br/><br/>Verze 11 cílí na rozhraní REST API pro hledání verze = 2020-06-30. | Všeobecně k dispozici. </br> Nainstalujte [ @azure/search-documents balíček](https://www.npmjs.com/package/@azure/search-documents) z npm. |

## <a name="june-2020"></a>Červen 2020

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
[**Knowledge Store**](knowledge-store-concept-intro.md) | Obohacení AI | Výstup indexeru s obohaceným AI, který ukládá obsah v Azure Storage pro použití v jiných aplikacích a procesech. | Všeobecně k dispozici. </br> Použijte [Search REST API 2020-06-30](/rest/api/searchservice/) nebo novější nebo na portálu. |
| [**Hledat REST API 2020-06-30**](/rest/api/searchservice/) | REST | Nová stabilní verze rozhraní REST API. Kromě znalostní báze Knowledge Store zahrnuje tato verze vylepšení vyhledávání relevance a bodování. | Všeobecně k dispozici. |
| [**Okapi BM25 – algoritmus relevance**](https://en.wikipedia.org/wiki/Okapi_BM25) | Dotaz | Nový algoritmus hodnocení relevance se automaticky používá pro všechny nové služby vyhledávání vytvořené po 15. červenci. Pro služby, které jste vytvořili dřív, se můžete rozhodnout, že nastavíte `similarity` vlastnost u polí index. | Všeobecně k dispozici. </br> Použijte [Search REST API 2020-06-30](/rest/api/searchservice/) nebo novější, nebo REST API 2019-05-06. |
| **executionEnvironment** | Zabezpečení (indexery) | Explicitně nastavte tuto vlastnost konfigurace indexeru tak, aby `private` vynutila všechna připojení k externím zdrojům dat v rámci privátního koncového bodu. Platí pouze pro služby vyhledávání, které využívají privátní propojení Azure. | Všeobecně k dispozici. </br> K nastavení tohoto obecného parametru konfigurace použijte [Search REST API 2020-06-30](/rest/api/searchservice/) . |

## <a name="may-2020-microsoft-build"></a>Květen 2020 (Microsoft Build)

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [**Ladicí relace**](cognitive-search-debug-session.md) | Obohacení AI | Relace ladění poskytují rozhraní založené na portálu pro zkoumání a řešení problémů s existujícím dovednosti. Opravy vytvořené v relaci ladění se dají uložit do produkčního dovednostiu. Začněte s [tímto kurzem](cognitive-search-tutorial-debug-sessions.md). | Public Preview na portálu. |
| [**Pravidla protokolu IP pro podporu firewallu s vazbou**](service-configure-firewall.md) | Zabezpečení | Omezte přístup ke koncovému bodu vyhledávací služby na konkrétní IP adresy. | Všeobecně k dispozici. </br> Použijte [správu REST API 2020-03-13](/rest/api/searchmanagement/) nebo novější nebo na portálu. |
| [**Privátní odkaz na Azure pro soukromý koncový bod hledání**](service-create-private-endpoint.md) | Zabezpečení| Propojte vyhledávací službu z veřejného Internetu tak, že ji spustíte jako prostředek privátního propojení, který je přístupný jenom pro klientské aplikace a další služby Azure ve stejné virtuální síti. | Všeobecně k dispozici. </br> Použijte [správu REST API 2020-03-13](/rest/api/searchmanagement/) nebo novější nebo na portálu. |
| [**Identita spravovaná systémem (verze Preview)**](search-howto-managed-identities-data-sources.md) | Zabezpečení (indexery) | Zaregistrujte vyhledávací službu jako důvěryhodnou službu s Azure Active Directory pro nastavení připojení k podporovanému zdroji dat Azure pro indexování. Platí pro [indexery](search-indexer-overview.md) , které ingestují obsah ze zdrojů dat Azure, jako jsou Azure SQL Database, Azure Cosmos DB a Azure Storage. | Verze Public Preview. </br> K registraci vyhledávací služby použijte portál. |
| [**parametr dotazu SessionID**](index-similarity-and-scoring.md), [scoringStatistics = Global – parametr](index-similarity-and-scoring.md#scoring-statistics) | Dotaz (relevance) | Přidejte sessionID do dotazu a navažte relaci pro výpočet skóre hledání s scoringStatistics = Global pro shromažďování skóre ze všech horizontálních oddílů a pro přesnější výpočty skóre hledání. | Všeobecně k dispozici. </br> Použijte [Search REST API 2020-06-30](/rest/api/searchservice/) nebo novější, nebo REST API 2019-05-06. |
| [**featuresMode (Preview)**](index-similarity-and-scoring.md#featuresMode-param) | Dotaz | Přidejte tento parametr dotazu, abyste rozšířili skóre relevance tak, aby se zobrazilo více podrobností: skóre podobnosti za pole, frekvence termínů a počet jedinečných tokenů podle pole. Tyto datové body můžete využívat v algoritmech vlastního bodování. Ukázku, která tuto schopnost předvádí, najdete v tématu věnovaném [Přidání strojového učení (LearnToRank) k vyhledávání relevance](https://github.com/Azure-Samples/search-ranking-tutorial). | Verze Public Preview. </br> Použijte [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) nebo REST API 2019-05-06-Preview. |

## <a name="march-2020"></a>Březen 2020

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [**Obnovitelné odstranění nativního objektu BLOB (Preview)**](search-howto-index-changed-deleted-blobs.md) | Indexery | Indexovací člen služby Azure Blob Storage v Azure Kognitivní hledání rozpozná objekty blob, které jsou ve stavu undeleteded, a během indexování odebere odpovídající hledaný dokument. | Verze Public Preview. </br> Pomocí služby [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) a REST API 2019-05-06-Preview se spuštěním indexeru na zdroji dat objektu BLOB v Azure, který má povolené nativní "obnovitelné odstranění". |
| [**REST API správy (2020-03-13)**](/rest/api/searchmanagement/management-api-versions) | REST | Nové stabilní REST API pro vytváření a správu vyhledávací služby. Přidání podpory protokolu IP a privátních odkazů | Všeobecně k dispozici. |

## <a name="february-2020"></a>Únor 2020

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [**Detekce PII (Preview)**](cognitive-search-skill-pii-detection.md) | Obohacení AI | Nová funkce rozpoznávání, která se používá při indexování, která extrahuje osobní údaje ze vstupního textu a poskytuje možnost jejich maskování z tohoto textu různými způsoby. | Verze Public Preview. </br> Použijte portál nebo [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) nebo REST API 2019-05-06-Preview. |
| [**Vyhledávání vlastních entit (Preview)**](cognitive-search-skill-custom-entity-lookup.md )| Obohacení AI | Nová schopnost rozpoznávání, která hledá text z vlastního uživatelsky definovaného seznamu slov a frází. Pomocí tohoto seznamu jsou všechny dokumenty označeny všemi vyhovujícími entitami. Dovednost také podporuje stupeň přibližné shody, které lze použít pro hledání shod, které jsou podobné, ale ne přesně. | Verze Public Preview. </br> Použijte portál nebo [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) nebo REST API 2019-05-06-Preview. |

## <a name="january-2020"></a>Leden 2020

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategorie | Popis | Dostupnost  |
|---------|------------------|-------------|---------------|
| [**Šifrovací klíče spravované zákazníkem**](search-security-manage-encryption-keys.md) |Zabezpečení | Kromě integrovaného šifrování platformy přidá další vrstvu šifrování. Pomocí šifrovacího klíče, který vytvoříte a spravujete, můžete šifrovat obsah indexu a mapy synonym před tím, než dosáhnete datové části služby vyhledávání. | Všeobecně k dispozici. </br> Použijte Search REST API 2019-05-06 nebo novější. Pro spravovaný kód je správný balíček stále [.NET SDK verze 8,0-Preview](search-dotnet-sdk-migration-version-9.md) , i když funkce není ve verzi Preview. |
| [**Pravidla protokolu IP pro podporu brány firewall vázané na hranice (Preview)**](service-configure-firewall.md) | Zabezpečení | Omezte přístup ke koncovému bodu vyhledávací služby na konkrétní IP adresy. Rozhraní API ve verzi Preview má nové vlastnosti **IpRule** a **NetworkRuleSet** v [rozhraní CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Tato funkce ve verzi Preview je dostupná ve vybraných oblastech. |  Verze Public Preview s použitím rozhraní API-Version = 2019-10 -01-Preview.  |
| [**Privátní odkaz Azure pro soukromý koncový bod hledání (Preview)**](service-create-private-endpoint.md) | Zabezpečení| Propojte vyhledávací službu z veřejného Internetu tak, že ji spustíte jako prostředek privátního propojení, který je přístupný jenom pro klientské aplikace a další služby Azure ve stejné virtuální síti. | Verze Public Preview s použitím rozhraní API-Version = 2019-10 -01-Preview.  |

## <a name="features-in-2019"></a>Funkce v 2019

### <a name="december-2019"></a>Prosinec 2019

+ [Vytvořit ukázkovou aplikaci (Preview)](search-create-app-portal.md) je nový průvodce na portálu, který GENERUJE soubor HTML ke stažení s přístupem (jen pro čtení) k indexu. Soubor se dodává se skriptem Embedded, který vykreslí provozní webovou aplikaci ve stylu "localhost", která je svázána s indexem ve vaší vyhledávací službě. Stránky lze v průvodci konfigurovat a mohou obsahovat panel hledání, oblast výsledků, navigaci bočním panelem a podporu dotazů typeahead. Můžete upravit HTML v offline režimu pro rozšiřování nebo přizpůsobení pracovního postupu nebo vzhledu. Ukázkovou aplikaci není snadné rozšířit tak, aby zahrnovala vrstvy zabezpečení a hostování, které jsou obvykle potřeba v produkčních scénářích. Měli byste ho zvážit jako nástroj pro ověřování a testování, ale nemusíte ho zkrátit na celou klientskou aplikaci.

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

+ Obecně dostupné v [cloudu Azure Government](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nový název služby

Azure Search se teď přejmenuje na **Azure kognitivní hledání** , aby odráželo rozšířené (zatím volitelné) používání vnímání a zpracování AI v základních operacích. Verze API, balíčky NuGet, obory názvů a koncové body se nezměnily. Nová a stávající řešení hledání nejsou ovlivněna změnou názvu služby.

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure kognitivní hledání najdete na webu Azure.