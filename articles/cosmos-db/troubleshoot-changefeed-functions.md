---
title: Řešení potíží při použití triggeru Azure Functions pro Cosmos DB
description: Běžné problémy, alternativní řešení a diagnostické kroky při použití triggeru Azure Functions pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/29/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1b7b82ea07b7e00d281739011c9c9f83ab4dff73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825616"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnostika a řešení potíží při použití triggeru Azure Functions pro Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje běžné problémy, alternativní řešení a diagnostické kroky při použití [triggeru Azure Functions pro Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Závislosti

Aktivační událost Azure Functions a vazby pro Cosmos DB závisí na balíčcích rozšíření v rámci základního Azure Functions modulu runtime. Vždy udržujte tyto balíčky aktualizované, protože můžou zahrnovat opravy a nové funkce, které mohou řešit případné problémy, se kterými se můžete setkat:

* Azure Functions v2 najdete v tématu [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Azure Functions v1 najdete v tématu [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Tento článek se vždy odkazuje na Azure Functions v2 vždy, když je zmíněn modul runtime, pokud není explicitně zadán.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Využívat sadu SDK pro Azure Cosmos DB nezávisle

Klíčovou funkcí balíčku rozšíření je poskytnutí podpory Azure Functions triggeru a vazeb pro Cosmos DB. Zahrnuje také [sadu Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), která je užitečná, pokud chcete pracovat s Azure Cosmos DB programově bez použití triggeru a vazeb.

Pokud chcete použít sadu Azure Cosmos DB SDK, ujistěte se, že do projektu nepřidáte další odkaz na balíček NuGet. Místo toho je třeba **pomocí balíčku rozšíření Azure Functions vyhodnotit odkaz na sadu SDK**. Využití sady SDK Azure Cosmos DB odděleně od triggeru a vazeb

Pokud navíc ručně vytváříte vlastní instanci [klienta Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), měli byste postupovat podle vzoru, který má pouze jednu instanci klienta s [přístupem ke vzorům typu Singleton](../azure-functions/manage-connections.md#documentclient-code-example-c). Tento proces se zabrání potenciálním problémům soketu v rámci vašich operací.

## <a name="common-scenarios-and-workarounds"></a>Běžné scénáře a alternativní řešení

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Funkce Azure Functions se nezdařila, protože kolekce chybových zpráv neexistuje.

Funkce Azure Functions se nezdařila s chybovou zprávou "buď zdrojová kolekce ' Collection-Name" (v databázi ' Database-name ') nebo kolekce zapůjčení ' Collection2-name ' (v databázi ' databáze 2-Name ') neexistuje. Obě kolekce musí existovat před spuštěním naslouchacího procesu. Pokud chcete automaticky vytvořit kolekci zapůjčení, nastavte ' CreateLeaseCollectionIfNotExists ' na ' true '.

To znamená, že jeden nebo oba kontejnery Azure Cosmos, které má aktivační událost fungovat, neexistují nebo nejsou dosažitelné pro funkci Azure Functions. **Samotná chyba vám řekne, ke které službě Azure Cosmos Database a kontejneru bude na základě vaší konfigurace vyhledána aktivační událost** .

1. Ověřte `ConnectionStringSetting` atribut, který **odkazuje na nastavení, které existuje ve vašem Azure Function App**. Hodnota tohoto atributu nesmí být samotný připojovací řetězec, ale název nastavení konfigurace.
2. Ověřte, jestli `databaseName` v `collectionName` účtu Azure Cosmos existují a. Pokud používáte automatické nahrazení hodnoty (pomocí `%settingName%` vzorů), ujistěte se, že název nastavení existuje ve vašem Azure Function App.
3. Pokud nezadáte a `LeaseCollectionName/leaseCollectionName` , výchozí hodnota je "zapůjčení". Ověřte, že tento kontejner existuje. Volitelně můžete nastavit `CreateLeaseCollectionIfNotExists` atribut v triggeru tak, aby `true` se automaticky vytvořil.
4. Ověřte [konfiguraci brány firewall účtu Azure Cosmos](how-to-configure-firewall.md) , abyste viděli, že neblokuje funkci Azure Functions.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Funkci Azure Functions se nepodařilo spustit, protože sdílená kolekce propustnosti by měla mít klíč oddílu.

Předchozí verze rozšíření Azure Cosmos DB nepodporovaly používání kontejneru zapůjčení, který byl vytvořen v rámci [sdílené databáze propustnosti](./set-throughput.md#set-throughput-on-a-database). Pokud chcete tento problém vyřešit, aktualizujte rozšíření [Microsoft. Azure. WebJobs. Extensions. CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) , aby se získala nejnovější verze.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Funkce Azure Function se nespustí, protože pro tuto operaci se musí zadat PartitionKey.

Tato chyba znamená, že aktuálně používáte dělenou kolekci zapůjčení se starou [závislostí rozšíření](#dependencies). Upgradujte na nejnovější dostupnou verzi. Pokud aktuálně používáte Azure Functions V1, budete muset upgradovat na Azure Functions v2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Funkce Azure Function se nespustí s "kolekce zapůjčení, pokud je rozdělená na oddíly, musí mít klíč oddílu roven ID."

Tato chyba znamená, že váš aktuální kontejner zapůjčení je rozdělený na oddíly, ale cesta ke klíči oddílu není `/id` . Chcete-li tento problém vyřešit, je nutné znovu vytvořit kontejner zapůjčení s `/id` klíčem oddílu.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Zobrazí se hodnota nemůže mít hodnotu null. Název parametru: o v protokolech Azure Functions při pokusu o spuštění triggeru

K tomuto problému dochází, pokud používáte Azure Portal a při kontrole funkce Azure, která používá aktivační událost, se pokusíte vybrat tlačítko **Spustit** na obrazovce. Aktivační událost nevyžaduje, abyste vybrali možnost spustit pro spuštění, automaticky se spustí při nasazení funkce Azure. Pokud chcete v Azure Portal kontrolovat Stream protokolu Azure Functions, stačí přejít do monitorovaného kontejneru a vložit nějaké nové položky, automaticky se zobrazí aktivační událost, která se spouští.

### <a name="my-changes-take-too-long-to-be-received"></a>Doručení změn trvá příliš dlouho.

Tento scénář může mít několik příčin a všechny by měly být zkontrolovány:

1. Je vaše funkce Azure nasazená ve stejné oblasti jako váš účet Azure Cosmos? Pro zajištění optimální latence sítě by se funkce Azure i váš účet Azure Cosmos měly nacházet ve stejné oblasti Azure.
2. Jsou změny, ke kterým dochází ve vašem kontejneru Azure Cosmos, průběžné nebo sporadické?
Pokud jde o sporadické změny, může docházet k určité prodlevě mezi ukládáním změn a jejich vyzvedáváním funkcí Azure. Důvodem je to, že když trigger interně kontroluje změny v kontejneru Azure Cosmos a zjistí, že žádné nečekají na načtení, na určitou nastavitelnou dobu (ve výchozím nastavení 5 sekund) přejde do režimu spánku, a teprve pak zkontroluje nové změny (aby se zabránilo vysoké spotřebě RU). Tuto dobu do režimu spánku můžete nakonfigurovat pomocí nastavení `FeedPollDelay/feedPollDelay` v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) vašeho triggeru (hodnota by měla být v milisekundách).
3. Váš kontejner Azure Cosmos může být [omezený na míru](./request-units.md).
4. Pomocí `PreferredLocations` atributu v triggeru můžete zadat seznam oblastí Azure oddělených čárkou, abyste mohli definovat vlastní preferované pořadí připojení.

### <a name="some-changes-are-repeated-in-my-trigger"></a>V mém triggeru se opakují některé změny.

Koncept "změny" je operace na dokumentu. Nejběžnější scénáře, kdy se přijímají události pro stejný dokument, jsou:
* Účet používá konečnou konzistenci. Při využívání kanálu změn v konečné úrovni konzistence může dojít k duplicitním událostem v-mezi následnými operacemi čtení změn kanálu (poslední událost operace čtení se zobrazí jako první z následujících).
* Probíhá aktualizace dokumentu. Kanál změn může obsahovat více operací pro stejné dokumenty, pokud tento dokument přijímá aktualizace, může vybrat více událostí (jeden pro každou aktualizaci). Jedním z jednoduchých způsobů, jak rozlišovat mezi různými operacemi pro stejný dokument, je sledovat `_lsn` [vlastnost pro každou změnu](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Pokud se neshodují, jedná se o různé změny v rámci stejného dokumentu.
* Pokud identifikujete dokumenty pouze pomocí `id` , pamatujte, že jedinečný identifikátor dokumentu je `id` a jeho klíč oddílu (může se jednat o dva dokumenty stejného, `id` ale s jiným klíčem oddílu).

### <a name="some-changes-are-missing-in-my-trigger"></a>V aktivační události chybí některé změny.

Pokud zjistíte, že některé změny, ke kterým došlo v kontejneru Azure Cosmos, nejsou vyzvednuty funkcí Azure nebo některé změny v cíli chybějí při jejich kopírování, postupujte podle následujících kroků.

Když vaše funkce Azure přijme změny, často je zpracuje a může volitelně odeslat výsledek do jiného cíle. Při zkoumání chybějících změn nezapomeňte **změřit, které změny jsou přijímány v bodu** příjmu (při spuštění funkce Azure), nikoli v cíli.

Pokud v cíli chybějí nějaké změny, může to znamenat, že došlo k chybě při provádění funkce Azure po přijetí změn.

V tomto scénáři je nejlepší akcí přidání `try/catch` bloků do kódu a uvnitř smyček, které mohou zpracovávat změny, zjistit případné selhání pro určitou podmnožinu položek a odpovídajícím způsobem je zpracovat (Odeslat je do jiného úložiště pro další analýzu nebo opakovat).

> [!NOTE]
> Trigger služby Azure Functions pro službu Cosmos DB ve výchozím nastavení neopakuje dávku změn, pokud při provádění kódu dojde k neošetřené výjimce. To znamená, že důvodem nedoručení změn do cíle je to, že nebudete schopni je zpracovat.

Pokud je cílem jiný kontejner Cosmos a provádíte operace Upsert ke zkopírování položek, **Ověřte, že definice klíče oddílu na monitorovaném i cílovém kontejneru je stejná**. Operace Upsert mohly uložit více zdrojových položek jako jeden v cíli z důvodu tohoto rozdílu v konfiguraci.

Pokud zjistíte, že Trigger vůbec nepřijal nějaké změny, nejběžnějším scénářem je, že je **spuštěná jiná funkce Azure**. Může to být jiná funkce Azure nasazená v Azure nebo funkce Azure spuštěná místně na počítači vývojáře, který má **přesně stejnou konfiguraci** (stejný monitorované a zapůjčení), a tato funkce Azure ukrást podmnožinu změn, které byste očekávali při zpracování funkce Azure.

Pokud víte, kolik instancí Azure Function App máte spuštěné, můžete taky ověřit scénář. Pokud provedete kontrolu kontejneru zapůjčení a určíte počet položek zapůjčení v rámci, budou jedinečné hodnoty `Owner` vlastnosti v nich rovny počtu instancí Function App. Pokud jsou k dispozici více vlastníků než známé instance služby Azure Function App, znamená to, že tyto další vlastníci budou tyto změny ukrást.

Jedním z jednoduchých způsobů, jak tuto situaci vyřešit, je použít `LeaseCollectionPrefix/leaseCollectionPrefix` pro vaši funkci novou/odlišnou hodnotu nebo případně testovat s novým kontejnerem zapůjčení.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Je nutné restartovat a znovu zpracovat všechny položky v kontejneru od začátku. 
Chcete-li znovu zpracovat všechny položky v kontejneru od začátku:
1. Pokud je služba Azure Functions momentálně spuštěná, zastavte ji. 
1. Odstraňte dokumenty v kolekci zapůjčení (nebo odstraňte a znovu vytvořte kolekci zapůjčení, aby byla prázdná).
1. Ve své funkci nastavte atribut [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger na hodnotu true. 
1. Restartujte funkci Azure Functions. Nyní bude číst a zpracovávat všechny změny od začátku. 

Nastavení [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) na hodnotu true oznámí službě Azure Function, aby začaly číst změny od začátku historie kolekce namísto aktuálního času. To funguje jenom v případě, že už nejsou vytvořená zapůjčení (to znamená dokumenty v kolekci zapůjčení). Nastavení této vlastnosti na hodnotu true, pokud jsou již vytvořené zapůjčené adresy nijak ovlivněny. v tomto scénáři se při zastavení a opětovném spuštění funkce začne číst z posledního kontrolního bodu, jak je definováno v kolekci zapůjčení. Chcete-li znovu zpracovat od začátku, postupujte podle výše uvedených kroků 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Vazbu lze provést pouze s IReadOnlyList \<Document> nebo JArray

K této chybě dochází, pokud váš Azure Functions projekt (nebo jakýkoli odkazovaný projekt) obsahuje ruční odkaz na sadu NuGet na Azure Cosmos DB SDK s jinou verzí, než je ta, kterou poskytuje [rozšíření Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Tuto situaci můžete vyřešit tak, že odeberete ruční odkaz na NuGet, který jste přidali, a ponecháte odkaz Azure Cosmos DB SDK vyřešit prostřednictvím balíčku rozšíření Azure Functions Cosmos DB.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Změna intervalu dotazování funkce Azure Functions pro zjišťování změn

Jak už bylo vysvětleno dříve, [Pokud moje změny trvá příliš dlouho](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received), služba Azure Functions se ve výchozím nastavení v režimu spánku po dobu (5 sekund), než začne kontrolovat nové změny (aby se zabránilo vysoké spotřebě v řádu ru). Tuto dobu do režimu spánku můžete nakonfigurovat pomocí nastavení `FeedPollDelay/feedPollDelay` v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) vašeho triggeru (hodnota by měla být v milisekundách).

## <a name="next-steps"></a>Další kroky

* [Povolit monitorování pro Azure Functions](../azure-functions/functions-monitoring.md)
* [Řešení potíží s Azure Cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)
