---
title: Použití změn databáze Azure Cosmos DB kanálu s využitím Azure Functions
description: Použití služby Azure Cosmos DB Změna kanálu s využitím Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.openlocfilehash: ece1c123cad8403358dcc3b3626bf4b2cd756e25
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628944"
---
# <a name="how-to-use-azure-cosmos-db-change-feed-with-azure-functions"></a>Použití změn databáze Azure Cosmos DB kanálu s využitím Azure Functions

Pokud používáte Azure Functions, nejjednodušší způsob, jak připojit ke kanálu změn je přidat [aktivační událost Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger) do aplikace pro Azure Functions. Když vytvoříte aktivační událost, Cosmos DB v aplikaci Azure Functions, vyberte kontejneru Cosmos pro připojení k a funkce se aktivuje vždy, když změníte něco v kontejneru.

Aktivační události lze vytvořit na portálu Azure Functions nebo na portálu služby Azure Cosmos DB, nebo prostřednictvím kódu programu. Další informace najdete v tématu [výpočetní prostředí bez serveru databázi s využitím služby Azure Cosmos DB a Azure Functions](serverless-computing-database.md).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>Jak nakonfigurovat službu Azure functions ke čtení z konkrétní oblasti?

Je možné definovat [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations) při použití triggeru služby Azure Cosmos DB pro zadání seznamu oblastí. Je stejná jako přizpůsobení ConnectionPolicy, aby se aktivační událost číst z preferované oblasti. V ideálním případě má ke čtení z nejbližší oblasti, ve kterém je nasazená Azure Functions.

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Jaká je výchozí velikost dávek ve službě Azure Functions?

Výchozí velikost je 100 položek pro každé vyvolání sady Azure Functions. Toto číslo je však možné konfigurovat v souboru function.json. Tady je úplný [seznam možností konfigurace](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Pokud vyvíjíte místně, aktualizujte nastavení aplikace v rámci souboru local.settings.json.

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>Sleduji kontejneru a jeho kanál změn pro čtení, ale nezobrazí všechny vložené dokumenty, některé položky nebyly nalezeny?

Ujistěte se, že neexistuje žádná další funkce Azure Functions stejný kontejner pomocí stejného kontejneru zapůjčení pro čtení. Chybějící dokumenty jsou zpracovány Azure Functions, která také používají stejné zapůjčení.

Proto pokud vytváříte více funkcí Azure ke čtení, že stejné kanálu změn, musí používat různé zapůjčení kontejnery nebo použít konfiguraci "leasePrefix" sdílet stejný kontejner. Při použití knihovnou change feed processor můžete spustit více instancí nástroje vaši funkci Azure functions a sady SDK se bude dělit dokumenty mezi různými instancemi automaticky za vás.

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Azure Cosmos položky se aktualizuje každou sekundu a můžu mít všechny změny ve službě Azure Functions naslouchání kanálu změn?

Služba Azure Functions se dotazuje kanálu změn nepřetržitě, s výchozí maximální zpoždění 5 sekund změn. Pokud nejsou žádné čekající změny pro čtení, nebo pokud existují čekající změny po použití aktivační událost, bude je funkce hned číst. Ale pokud nejsou žádné čekající změny, funkce počkejte 5 sekund a dotazovat na další změny.

Pokud váš dokument přijímá více změn ve stejném intervalu, který přijal aktivační událost pro dotazování na nových změn, může se zobrazit nejnovější verzi dokumentu a není zprostředkující ten.

Pokud chcete k dotazování kanálu po dobu kratší než 5 sekund změn, například pro každou sekundu, můžete nakonfigurovat čas dotazování "feedPollDelay", naleznete v tématu [kompletní konfigurace](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations). Je definován v milisekundách, s výchozí 5000. Dotazování pro menší než 1 sekundu je možné, ale to se nedoporučuje, protože se spustí pomocí více procesorů paměti.

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>Může číst několik Azure Functions kanálu změn jednoho kontejneru?

Ano. Kanál změn stejný kontejner můžete přečíst víc funkcí Azure. Azure Functions však musí mít samostatné "leaseCollectionPrefix" definované.

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>Pokud jsem kanál pomocí Azure Functions ve službě batch 10 dokumentů, zpracování změn a zobrazí chyba. v sedmé dokumentu. Poslední tři dokumenty v takovém případě nejsou zpracovány, jak lze zahájit zpracování z neúspěšných dokumentu (tj, sedmý dokumentů) v mé další informační kanál?

Chybu zpracovat, je doporučený model zabalení svůj kód pomocí bloku try-catch a pokud jsou iterace seznamu dokumentů, zalomení každé iterace v bloku try-catch. Zachytávat chyby a daný dokument umístit do fronty (nedoručené zprávy) a pak definovat logiku a pracují s dokumenty, u kterých došlo k chybě. Pomocí této metody Pokud máte služby batch dokument 200 a pouze jeden dokument se nepovedlo, není nutné zbavovat celý batch.

V případě chyby, by neměla rewind bodu vrácení zpět na začátek jinak je se můžete stále se zobrazuje tyto dokumenty z kanálu změn. Nezapomeňte, že změna informačního kanálu zachová poslední konečné snap snímek dokumenty, protože jste to vy, může dojít ke ztrátě předchozího snímku v dokumentu. kanál změn udržuje pouze jednu poslední verzi dokumentu a mezi mohou jiné procesy pocházet a změna dokumentu.

Jak zachovat opravu kódu, bude brzy najít žádné dokumenty fronty nedoručených zpráv. Služba Azure Functions je automaticky volána informačního kanálu systémem změny a kontrolní bod se interně spravuje pomocí funkce Azure functions. Pokud chcete vrátit zpět kontrolní bod a řízení všech aspektů, měli byste zvážit použití řešení change kanálu procesoru SDK.

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>Existují žádné další náklady na použití aktivační událost Azure Cosmos DB?

Aktivační událost Azure Cosmos DB využívá knihovna Change Feed Processor interně. V důsledku toho vyžaduje další kolekce, volá kolekci zapůjčení, k zachování stavu a částečné kontrolní body. Tento stav správy je potřeba k dynamické škálování a pokračovat v případě, že chcete zastavit Azure Functions a pokračovat ve zpracování později. Další informace najdete v tématu [jak pracovat s změnit informační kanál knihovny procesoru](change-feed-processor.md).

## <a name="next-steps"></a>Další postup

Teď můžete přejít k další informace o změně v následujících článcích:

* [Přehled kanálu změn](change-feed.md)
* [Způsoby, jak čtení kanálu změn](read-change-feed.md)
* [Změna kanálu knihovny procesoru](change-feed-processor.md)
* [Jak pracovat s změnit informační kanál knihovny procesoru](change-feed-processor.md)
* [Výpočetní prostředí bez serveru databázi s využitím služby Azure Cosmos DB a Azure Functions](serverless-computing-database.md)