---
title: Co je nového v Azure Kognitivní hledání
description: Oznámení nových a vylepšených funkcí, včetně přejmenování služby Azure Search do Azure Kognitivní hledání.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/09/2021
ms.custom: references_regions
ms.openlocfilehash: 6de91d51f3700c25ba1e0f361c948dc8ab338c43
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590545"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Co je nového v Azure Kognitivní hledání

Zjistěte, co je nového ve službě. Pomocí záložky můžete tuto stránku udržovat v aktualizovaném stavu. Podívejte se na [seznam funkcí ve verzi Preview](search-api-preview.md) a podívejte se na funkce ve verzi Public Preview.

## <a name="february-2021"></a>Únor 2021

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Dostupnost  |
|------------------------------|---------------|---------------|
| [Resetovat dokumenty (Preview)](search-howto-run-reset-indexers.md) |  Znovu zpracuje jednotlivě vybrané dokumenty hledání v úlohách indexeru. | [Search REST API 2020-06-30 – Preview](/rest/api/searchservice/index-preview) |
| [Zóny dostupnosti](search-performance-optimization.md#availability-zones)| Vyhledávací služby se dvěma nebo více replikami v určitých oblastech, jak je uvedeno v [rozsahu pro výkon](search-performance-optimization.md#availability-zones), získáte odolnost při používání replik ve dvou nebo více různých fyzických umístěních.  | Oblast a datum vytvoření služby vyhledávání, která určuje dostupnost. Podrobnosti najdete v článku věnovaném škálování na výkon. |
| [Azure CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Nové revize teď poskytují celou škálu operací v rámci správy REST API 2020-08-01, včetně podpory pravidel brány firewall protokolu IP a privátního koncového bodu. | Všeobecně k dispozici. |

## <a name="january-2021"></a>Leden 2021

|Zapnut&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Dostupnost  |
|------------------------------|-------------|---------------|
| [Akcelerátor řešení pro Azure Kognitivní hledání a QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Vyžádá si otázky a odpovědi z dokumentu a navrhne relevantní odpovědi. Živou ukázkovou aplikaci najdete na adrese [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Open source projekt (bez smlouvy SLA) |

## <a name="2020-archive"></a>Archiv 2020

| Month (Měsíc) | Funkce | Popis |
|-------|---------|-------------|
| Listopad | [Šifrování klíče spravovaného zákazníkem (rozšířené)](search-security-manage-encryption-keys.md) | Rozšiřuje šifrování spravované zákazníkem v celém rozsahu prostředků vytvořených a spravovaných vyhledávací službou. Všeobecně k dispozici.|
| Září | [Rozšíření Visual Studio Code pro Azure Kognitivní hledání](search-get-started-vs-code.md) | Přidá pracovní prostor, navigaci, IntelliSense a šablony pro vytváření indexů, indexerů, zdrojů dat a dovednosti. | Verze Public Preview |
| Září | [Identita spravované služby (indexery)](search-howto-managed-identities-data-sources.md) | Všeobecně k dispozici.  |
| Září | [Odchozí požadavky pomocí privátního odkazu](search-indexer-howto-access-private.md) | Všeobecně k dispozici.  |
| Září | [REST API správy (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Všeobecně k dispozici. |
| Září | [REST API správy (2020-08-01 – Preview)](/rest/api/searchmanagement/management-api-versions) | Přidá prostředek sdíleného privátního propojení pro databáze Azure Functions a Azure SQL pro MySQL. |
| Září | [Sada Management .NET SDK 4,0](/dotnet/api/overview/azure/search/management) |  Aktualizace sady Azure SDK pro sadu Management SDK, cílené REST API verze 2020-08-01. Všeobecně k dispozici.|
| Srpen | [dvojité šifrování](search-security-overview.md#encryption) | Všeobecně dostupná pro všechny služby vyhledávání vytvořené 1. srpna 2020 v těchto oblastech: Západní USA 2, Východní USA, Střed USA – jih, US Gov – Virginie US Gov – Arizona. |
| Červenec | [ Klientská knihovnaAzure.Search.Documents](/dotnet/api/overview/azure/search.documents-readme) | Sada Azure SDK pro .NET, obecně dostupná. |
| Červenec | [ Klientská knihovnaazure.search.documents](/python/api/overview/azure/search-documents-readme)  | Sada Azure SDK pro Python, obecně dostupná. |
| Červenec | [@azure/search-documents Klientská knihovna](/javascript/api/overview/azure/search-documents-readme)  | Sada Azure SDK pro JavaScript, obecně dostupná. |
| Červen | [Knowledge Store](knowledge-store-concept-intro.md) | Všeobecně k dispozici. |
| Červen | [Hledat REST API 2020-06-30](/rest/api/searchservice/) | Všeobecně k dispozici. |
| Červen | [Search REST API 2020-06-30 – Preview](/rest/api/searchservice/) | Přidá Reset dovednosti k selektivnímu rezpracování dovedností a přírůstkové obohacení. |
| Červen | [Okapi BM25 – algoritmus relevance](index-ranking-similarity.md) | Všeobecně k dispozici. |
| Červen |  **executionEnvironment** (platí pro služby vyhledávání pomocí privátního propojení Azure) | Všeobecně k dispozici. |
| Červen | [AML dovednosti (Preview)](cognitive-search-aml-skill.md) | Vnímání dovedností, které rozšiřuje obohacení AI s vlastním modelem Azure Machine Learning (AML). |
| Květen | [Relace ladění (Preview)](cognitive-search-debug-session.md) | Dovednosti Debugger na portálu.  |
| Květen | [Pravidla protokolu IP pro podporu firewallu s vazbou](service-configure-firewall.md) | Všeobecně k dispozici.  |
| Květen | [Privátní odkaz na Azure pro soukromý koncový bod hledání](service-create-private-endpoint.md) | Všeobecně k dispozici.  |
| Květen | [Identita spravované služby (indexery) – (Preview)](search-howto-managed-identities-data-sources.md) | Připojte se ke zdrojům dat Azure pomocí spravované identity.  |
| Květen | [parametr dotazu SessionID](index-similarity-and-scoring.md), [scoringStatistics = Global – parametr](index-similarity-and-scoring.md#scoring-statistics)  | Statistická Statistika hledání, užitečná pro [modely strojového učení (LearnToRank) pro vyhledávání relevance](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Květen | [rozšíření skóre relevance featuresMode (Preview)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Březen  | [Obnovitelné odstranění nativního objektu BLOB (Preview)](search-howto-index-changed-deleted-blobs.md) | Odstraní dokumenty hledání, pokud je zdrojový objekt BLOB v úložišti objektů BLOB Soft odstraněný. |
|Březen  | [REST API správy (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Všeobecně k dispozici. |
|Únor | [Dovednost pro detekci PII (Preview)](cognitive-search-skill-pii-detection.md)  | Vnímání dovedností, které extrahují a maskují osobní údaje. |
|Únor | [Dovednosti při vyhledávání vlastních entit (Preview)](cognitive-search-skill-custom-entity-lookup.md) | Vnímání znalostí, které hledá slova a fráze ze seznamu a označí všechny dokumenty odpovídajícími entitami.  |
|January (Leden) | [Šifrování klíčů spravované zákazníkem](search-security-manage-encryption-keys.md) | Obecná dostupnost  |
|January (Leden) | [Pravidla protokolu IP pro podporu brány firewall vázané na hranice (Preview)](service-configure-firewall.md) | Nové vlastnosti **IpRule** a **NetworkRuleSet** v [rozhraní CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|January (Leden) | [Vytvoření privátního koncového bodu (Preview)](service-create-private-endpoint.md) | Nastavte privátní odkaz pro zabezpečená připojení k vaší vyhledávací službě. Tato funkce Preview má závislost [privátního propojení Azure](../private-link/private-link-overview.md) a [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) jako součást řešení. |

## <a name="2019-archive"></a>Archiv 2019

| Month (Měsíc) | Funkce | Popis |
|-------|---------|-------------|
|Prosinec | [Vytvořit ukázkovou aplikaci (Preview)](search-create-app-portal.md) | Průvodce, který vygeneruje soubor HTML ke stažení s dotazem (jen pro čtení) s přístupem k indexu, který je určený jako nástroj pro ověřování a testování, a ne jenom krátce vyjmutím do úplné klientské aplikace.|
|Listopad | [Přírůstkové obohacení (Preview)](cognitive-search-incremental-indexing-conceptual.md) | Ukládá do mezipaměti zpracování dovednosti pro budoucí opakované použití.  |
|Listopad | [Dovednost extrakce dokumentu (Preview)](cognitive-search-skill-document-extraction.md) | Vnímání dovedností k extrakci obsahu souboru v rámci dovednosti.|
|Listopad | [Dovednost překladu textu](cognitive-search-skill-text-translation.md) | Vnímání znalostí používané při indexování, který vyhodnocuje a překládá text. Všeobecně k dispozici.|
|Listopad | [Šablony Power BI](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Šablona pro vizualizaci obsahu ve znalostní bázi Store |
|Listopad | [Azure Data Lake Storage Gen2 (Preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md)a [Cosmos DB rozhraní API Cassandra (Preview)](search-howto-index-cosmosdb.md) | Nové zdroje dat indexeru ve verzi Public Preview. |
|Červenec | [Podpora cloudu Azure Government](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Všeobecně k dispozici.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nový název služby

Azure Search se přejmenovala na **Azure kognitivní hledání** v říjnu 2019, aby odrážela rozbalené (ještě volitelné) používání vnímání a zpracování AI v základních operacích. Verze API, balíčky NuGet, obory názvů a koncové body se nezměnily. Nová a stávající řešení hledání nejsou ovlivněna změnou názvu služby.

## <a name="service-updates"></a>Aktualizace služeb

[Oznámení o aktualizacích služby](https://azure.microsoft.com/updates/?product=search&status=all) pro Azure kognitivní hledání najdete na webu Azure.