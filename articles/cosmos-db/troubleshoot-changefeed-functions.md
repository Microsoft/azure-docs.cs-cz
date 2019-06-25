---
title: Diagnostika a řešení problémů při používání aktivační událost Azure Cosmos DB ve službě Azure Functions
description: Běžné problémy, řešení a krokům diagnostiky, při použití aktivační událost Azure Cosmos DB s využitím Azure Functions
author: ealsur
ms.service: cosmos-db
ms.date: 05/23/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09ea70ac302806b4cb0e97fde92dda4208e3d659
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734518"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Diagnostika a řešení problémů při používání aktivační událost Azure Cosmos DB ve službě Azure Functions

Tento článek se věnuje běžné problémy, řešení a krokům diagnostiky, při použití [aktivační událost Azure Cosmos DB](change-feed-functions.md) s využitím Azure Functions.

## <a name="dependencies"></a>Závislosti

Azure Cosmos DB aktivační události a vazby závisí na balíčky rozšíření prostřednictvím základní modulu runtime Azure Functions. Vždy ponechte tyto balíčky se aktualizovaly, jak se může zahrnovat opravy a nové funkce, které může vyřešit všechny potenciální problémy, se můžete setkat:

* Azure Functions 2, přečtěte si [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Azure Functions V1, naleznete v tématu [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Tento článek vždy vrátíme se k Azure Functions V2 pokaždé, když modul runtime je již bylo zmíněno, pokud není explicitně zadán.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Nezávisle na sobě využívat Azure Cosmos DB SDK

Klíčové funkce balíček rozšíření je k poskytování podpory pro aktivační událost Azure Cosmos DB a vazby. Zahrnuje také [.NET SDK služby Azure Cosmos DB](sql-api-sdk-dotnet-core.md), což je užitečné, pokud budete chtít pracovat s Azure Cosmos DB prostřednictvím kódu programu bez použití aktivační události a vazby.

Pokud chcete používat Azure Cosmos DB SDK, ujistěte se, že nepřidáte do svého projektu odkaz na jiný balíček NuGet. Místo toho **nechat přeložit pomocí balíček rozšíření Azure Functions odkazu sady SDK**. Využívání Azure Cosmos DB SDK odděleně od aktivační událost a vazby

Kromě toho pokud ručně vytvoříte svoji vlastní instanci služby [klienta služby Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), měli byste postupovat podle vzor pouze jedna instance klienta s [použitím Singleton vzor přístupu](../azure-functions/manage-connections.md#documentclient-code-example-c) . Tento proces se vyhnuli případným potížím soketu ve vašich operací.

## <a name="common-scenarios-and-workarounds"></a>Běžné scénáře a řešení

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Funkce Azure selže a zobrazí se chybová zpráva kolekce neexistuje.

Funkce Azure Functions se nezdaří s chybovou zprávou "buď zdrojové kolekce"kolekce name"(v databázi"database-name") nebo kolekci zapůjčení"kolekce 2 name"(v databázi"databáze 2 name") neexistuje. Obě kolekce musí existovat před spuštěním naslouchacího procesu. Chcete-li automaticky vytvořit kolekci zapůjčení, nastavte 'CreateLeaseCollectionIfNotExists' na 'true' "

To znamená, že jedno nebo obě kontejnerů Azure Cosmos, vyžaduje se pro aktivační událost pro práci neexistují nebo nejsou dostupné pro funkce Azure functions. **Vlastní chyba vám dá vědět, kterou databázi Azure Cosmos a kontejnerů, je trigger hledáte** na základě vaší konfigurace.

1. Ověřte, `ConnectionStringSetting` atribut a že **odkazuje na nastavení, která existuje v aplikaci Azure Function App**. Hodnota tohoto atributu by neměla být samotný řetězec připojení, ale název tohoto nastavení konfigurace.
2. Ověřte, že `databaseName` a `collectionName` existovat ve vašem účtu Azure Cosmos. Pokud používáte automatické hodnoty nahrazení (pomocí `%settingName%` vzory), ujistěte se, že název nastavení existuje ve vaší aplikaci funkcí Azure.
3. Pokud nezadáte `LeaseCollectionName/leaseCollectionName`, výchozí hodnota je "zapůjčení". Ověřte, zda existuje takový kontejner. Volitelně můžete nastavit `CreateLeaseCollectionIfNotExists` atribut v aktivační událost pro `true` automaticky ji vytvořit.
4. Ověřte vaše [konfigurace brány Firewall na účtu Azure Cosmos](how-to-configure-firewall.md) zobrazíte zobrazíte, že není neblokuje funkce Azure functions.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Začít s selže funkci Azure "Shared propustnost kolekce by měly mít klíč oddílu"

Předchozí verze rozšíření Azure Cosmos DB pomocí zapůjčení kontejneru, který byl vytvořen v rámci nepodporoval [sdílené propustnosti databáze](./set-throughput.md#set-throughput-on-a-database). Pokud chcete tento problém vyřešit, aktualizujte [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) rozšíření získat nejnovější verzi.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Funkce Azure Functions se nezdaří spuštění s "kolekci zapůjčení, pokud rozdělená na oddíly, musí mít stejné ID klíče oddílu."

Tato chyba znamená, že aktuální zapůjčení kontejneru je rozdělit na oddíly, ale cesta klíče oddílu je `/id`. Pokud chcete tento problém vyřešit, budete muset znovu vytvořit kontejner zapůjčení s `/id` jako klíč oddílu.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Uvidíte "hodnota nemůže být null. Název parametru: o "v Azure Functions protokolů při pokusu o spuštění aktivační události

Tento problém se zobrazí v případě, že používáte na webu Azure portal a vyberte **spustit** tlačítko na obrazovce při kontrole funkce Azure, který používá danou aktivační událost. Aktivační událost nevyžaduje, aby pro výběr spuštěním se zahájí, se automaticky spustí po nasazení funkce Azure functions. Pokud chcete zkontrolovat datový proud protokolu funkce Azure Functions na portálu Azure portal, stačí přejít na monitorovaných kontejner a vložit některé nové položky, automaticky zobrazí se aktivační událost spouští.

### <a name="my-changes-take-too-long-be-received"></a>Přijmout moje změny trvají příliš dlouho

Tento scénář může mít několik příčin a všechny z nich by měly být porovnány:

1. Nasazení funkce Azure ve stejné oblasti jako váš účet Azure Cosmos? Pro optimální síťovou latenci funkce Azure functions a váš účet Azure Cosmos by měly být umístěny společně ve stejné oblasti Azure.
2. Probíhají změny vašeho kontejneru Azure Cosmos průběžné nebo sporadické?
Pokud je ten, může být některých zpoždění mezi uložené změny a funkce Azure Functions je výběr. Je to proto interně, když trigger zkontroluje změny ve vašem kontejneru Azure Cosmos a zjistí žádné čekající ke čtení, ji budou přejít do režimu spánku konfigurovatelnou dobu (5 sekund, ve výchozím nastavení) předtím, než zkontroluje nové změny (aby se zabránilo vysoké spotřebu RU). Můžete nakonfigurovat v současné době v režimu spánku `FeedPollDelay/feedPollDelay` nastavení [konfigurace](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) triggeru (hodnota má být v milisekundách).
3. Váš kontejner Azure Cosmos může být [míra časově omezené](./request-units.md).
4. Můžete použít `PreferredLocations` atribut v triggeru zadat čárkou oddělený seznam oblastí Azure k definování vlastní připojení upřednostňované pořadí.

### <a name="some-changes-are-missing-in-my-trigger"></a>Některé změny nebyly nalezeny v mé triggeru

Pokud zjistíte, že některé změny, ke kterým došlo ve vašem kontejneru Azure Cosmos se se nenačítají pomocí funkce Azure Functions, že je počáteční zkoumání krok, kterou je potřeba provést.

Změny přijetí vaši funkci Azure Functions je často zpracovává a může volitelně, odeslat výsledky do jiného umístění. Když prošetřujete chybějící změny, ujistěte se, že jste **měr, které změny jsou přijímány v okamžiku ingestování** (funkce Azure Functions začíná), není v cíli.

Pokud v cíli chybí některé změny, to může znamenat, že je některé chybu během spuštění funkce Azure Functions poté, co byly změny přijaty.

V tomto scénáři je nejlepší postup přidání `try/catch blocks` ve vašem kódu a uvnitř smyčky, které může zpracovávat změny, zjistit jakékoli neúspěchy pro konkrétní podmnožině položek a odpovídajícím způsobem jejich zpracování (odeslat je do jiného úložiště pro další analýzy nebo opakování). 

> [!NOTE]
> Aktivační událost Azure Cosmos DB ve výchozím nastavení, nebude akci opakovat dávce změn, pokud došlo k neošetřené výjimce během provádění kódu. To znamená, že z důvodu, že změny nepřišla v cílovém umístění je tím, že se nedaří zpracovat.

Pokud zjistíte, že některé změny nebyly přijaty ve všech vašich aktivační procedura, nejběžnější scénář, který je **je spuštěna jiná funkce Azure Functions**. Může to být jiné funkce Azure, které jsou nasazené v Azure nebo funkce Azure s místně na počítači pro vývojáře, na které má **přesně stejnou konfiguraci** (stejné monitorovat a zapůjčení kontejnerů), a tato funkce Azure Functions je krádež podmnožina změny, které by uživatel očekával vaši funkci Azure Functions pro zpracování.

Kromě toho tento scénář může být ověřen, pokud víte, kolik instancí aplikace Azure Function App máte systémem. Je-li zkontrolovat zapůjčení kontejneru a spočítat počet zapůjčení položek v rámci různých hodnot položky, které `Owner` vlastnost v nich by měl být roven počtu instancí aplikace Function App. Pokud existují další vlastníky než známé instancí aplikace Azure Function App, to znamená, že tyto dodatečné vlastníky jeden "zcizování" změny.

Jeden snadný způsob, jak řešení této situace je použít `LeaseCollectionPrefix/leaseCollectionPrefix` vaši funkci s novou/jinou hodnotu nebo alternativně test pomocí nového kontejneru zapůjčení.

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Vazby jde jenom s IReadOnlyList<Document> nebo JArray

K této chybě dochází, pokud váš projekt Azure Functions (nebo jakéhokoli odkazovaného projektu) obsahuje ruční NuGet odkaz na sadu Azure Cosmos DB SDK s jinou verzí než ten, který poskytuje [Azure Functions Cosmos DB Extension](./troubleshoot-changefeed-functions.md#dependencies).

Chcete-li vyřešit tuto situaci, odebrat vyřešit ruční odkaz NuGet, který byl přidán a nechat odkaz na sadu Azure Cosmos DB SDK prostřednictvím balíček rozšíření Azure Functions Cosmos DB.

## <a name="next-steps"></a>Další postup

* [Povolit monitorování pro službu Azure Functions](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET řešení potíží s SDK](./troubleshoot-dot-net-sdk.md)