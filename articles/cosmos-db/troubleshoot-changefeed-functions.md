---
title: Řešení problémů při použití aktivační události Azure funkce pro Cosmos DB
description: Běžné problémy, řešení a diagnostické kroky při použití aktivační události Azure Funkce pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365504"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>Diagnostika a řešení problémů při použití aktivační události Azure Funkce pro Cosmos DB

Tento článek popisuje běžné problémy, řešení a diagnostické kroky při použití [aktivační události Funkce Azure pro Cosmos DB](change-feed-functions.md).

## <a name="dependencies"></a>Závislosti

Aktivační událost Azure Functions a vazby pro Cosmos DB závisí na balíčky rozšíření přes základní azure functions runtime. Tyto balíčky vždy aktualizujte, protože mohou zahrnovat opravy a nové funkce, které by mohly řešit případné problémy, se kterými se můžete setkat:

* Informace o funkcích Azure V2 najdete v tématu [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Informace o funkcích Azure V1 najdete v tématu [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Tento článek bude vždy odkazovat na Funkce Azure V2 vždy, když je uvedeno za běhu, pokud není výslovně zadáno.

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>Využití sady Azure Cosmos DB SDK nezávisle

Klíčovou funkcí balíčku rozšíření je poskytnout podporu pro azure funkce aktivační události a vazby pro Cosmos DB. Obsahuje také [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), což je užitečné, pokud chcete pracovat s Azure Cosmos DB programově bez použití aktivační události a vazby.

Pokud chcete použít Azure Cosmos DB SDK, ujistěte se, že nepřidáte do projektu jiný odkaz na balíček NuGet. Místo toho **nechte odkaz sady SDK vyřešit prostřednictvím balíčku rozšíření Azure Functions .** Využití sady Azure Cosmos DB SDK nezávisle na aktivační události a vazbách

Navíc pokud ručně vytváříte vlastní instanci [klienta Azure Cosmos DB SDK](./sql-api-sdk-dotnet-core.md), měli byste postupovat podle vzoru, který má pouze jednu instanci klienta [pomocí přístupu Singleton pattern](../azure-functions/manage-connections.md#documentclient-code-example-c). Tento proces zabrání potenciální problémy soketu ve vašich operacích.

## <a name="common-scenarios-and-workarounds"></a>Běžné scénáře a řešení

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Function selže s kolekce chybových zpráv neexistuje

Azure Function se nezdaří s chybovou zprávou "Buď zdrojová kolekce 'název kolekce' (v databázi 'název databáze') nebo kolekce zapůjčení 'collection2-name' (v databázi 'název database2') neexistuje. Obě kolekce musí existovat před spuštěním naslouchacího procesu. Chcete-li automaticky vytvořit kolekci zapůjčení, nastavte hodnotu CreateLeaseCollectionIfNotExists na hodnotu true.

To znamená, že jeden nebo oba kontejnery Azure Cosmos potřebné pro aktivační událost pracovat neexistují nebo nejsou dostupné pro funkci Azure. **Samotná chyba vám řekne, která databáze a kontejner Azure Cosmos je aktivační událost,** která hledá na základě vaší konfigurace.

1. Ověřte `ConnectionStringSetting` atribut a odkazuje **na nastavení, které existuje ve vaší aplikaci Azure Function App**. Hodnota tohoto atributu by neměla být samotná připojovací řetězec, ale název nastavení konfigurace.
2. Ověřte, že `databaseName` a `collectionName` existují ve vašem účtu Azure Cosmos. Pokud používáte automatické nahrazení `%settingName%` hodnot (pomocí vzorů), ujistěte se, že název nastavení existuje ve vaší aplikaci Azure Function App.
3. Pokud nezadáte `LeaseCollectionName/leaseCollectionName`, výchozí hodnota je "zapůjčení". Ověřte, zda takový kontejner existuje. Volitelně můžete nastavit `CreateLeaseCollectionIfNotExists` atribut v `true` aktivační události tak, aby jej automaticky vytvořil.
4. Ověřte [konfiguraci brány firewall účtu Azure Cosmos,](how-to-configure-firewall.md) abyste viděli, že to není to, že neblokuje funkci Azure.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure Function se nedaří začínat "Sdílená kolekce propustnost by měla mít klíč oddílu"

Předchozí verze rozšíření Azure Cosmos DB nepodporovaly použití kontejneru zapůjčení, který byl vytvořen v rámci [databáze sdílené propustnosti](./set-throughput.md#set-throughput-on-a-database). Chcete-li tento problém vyřešit, aktualizujte rozšíření [Microsoft.Azure.WebJobs.Extensions.CosmosDB,](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) abyste získali nejnovější verzi.

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure Funkce se nepodaří spustit s "PartitionKey musí být zadán pro tuto operaci."

Tato chyba znamená, že aktuálně používáte rozdělenou kolekci zapůjčení se starým [rozšířením](#dependencies). Upgradujte na nejnovější dostupnou verzi. Pokud aktuálně běžíte na Azure Functions V1, budete muset upgradovat na Funkce Azure V2.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure Function se nepodaří začínat "kolekce zapůjčení, pokud partitioned, musí mít klíč oddílu rovná id."

Tato chyba znamená, že aktuální zapůjčení kontejneru je rozdělena, ale cesta klíče oddílu není `/id`. Chcete-li tento problém vyřešit, je třeba `/id` znovu vytvořit kontejner zapůjčení s jako klíč oddílu.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Zobrazí se "Hodnota nemůže být null. Název parametru: o" v protokolech funkcí Azure při pokusu o spuštění aktivační události

Tento problém se zobrazí, pokud používáte portál Azure a pokusíte se vybrat tlačítko **Spustit** na obrazovce při kontrole funkce Azure, která používá aktivační událost. Aktivační událost nevyžaduje, abyste vybrali Spustit ke spuštění, automaticky se spustí při nasazení funkce Azure. Pokud chcete zkontrolovat datový proud protokolu funkce Azure na webu Azure Portal, stačí přejít na monitorovaný kontejner a vložit některé nové položky, automaticky uvidíte spuštění aktivační události.

### <a name="my-changes-take-too-long-to-be-received"></a>Přijetí změn trvá příliš dlouho

Tento scénář může mít více příčin a všechny z nich by měly být kontrolovány:

1. Je vaše funkce Azure nasazená ve stejné oblasti jako váš účet Azure Cosmos? Pro zajištění optimální latence sítě by se funkce Azure i váš účet Azure Cosmos měly nacházet ve stejné oblasti Azure.
2. Jsou změny, ke kterým dochází ve vašem kontejneru Azure Cosmos, průběžné nebo sporadické?
Pokud jde o sporadické změny, může docházet k určité prodlevě mezi ukládáním změn a jejich vyzvedáváním funkcí Azure. Důvodem je to, že když trigger interně kontroluje změny v kontejneru Azure Cosmos a zjistí, že žádné nečekají na načtení, na určitou nastavitelnou dobu (ve výchozím nastavení 5 sekund) přejde do režimu spánku, a teprve pak zkontroluje nové změny (aby se zabránilo vysoké spotřebě RU). Tuto dobu do režimu spánku můžete nakonfigurovat pomocí nastavení `FeedPollDelay/feedPollDelay` v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) vašeho triggeru (hodnota by měla být v milisekundách).
3. Váš kontejner Azure Cosmos může být [omezenou sazbou](./request-units.md).
4. `PreferredLocations` Atribut v aktivační události můžete použít k určení seznamu oblastí Azure oddělených čárkami k definování vlastního upřednostňovaného pořadí připojení.

### <a name="some-changes-are-repeated-in-my-trigger"></a>Některé změny se v spouštěči opakují

Koncept "změny" je operace v dokumentu. Nejběžnější scénáře, kde jsou přijímány události pro stejný dokument jsou:
* Účet používá eventuální konzistenci. Při využívání kanálu změn v konečné úrovni konzistence může být duplicitní události mezi následné operace čtení kanálu změn (poslední událost jedné operace čtení se zobrazí jako první další).
* Dokument je aktualizován. Kanál změn může obsahovat více operací pro stejné dokumenty, pokud tento dokument přijímá aktualizace, může zachytit více událostí (jednu pro každou aktualizaci). Jeden snadný způsob, jak rozlišovat mezi různými operacemi pro stejný dokument je sledovat `_lsn` vlastnost pro každou [změnu](change-feed.md#change-feed-and-_etag-_lsn-or-_ts). Pokud se neshodují, jedná se o různé změny ve stejném dokumentu.
* Pokud identifikujete dokumenty pouze `id`podle , nezapomeňte, že jedinečný `id` identifikátor dokumentu je a jeho klíč `id` oddílu (mohou existovat dva dokumenty se stejným, ale odlišným klíčem oddílu).

### <a name="some-changes-are-missing-in-my-trigger"></a>V aktivační události chybí některé změny

Pokud zjistíte, že některé změny, ke kterým došlo v kontejneru Azure Cosmos nejsou zvedl funkce Azure, je počáteční krok šetření, které je třeba provést.

Když vaše funkce Azure obdrží změny, často je zpracuje a může volitelně odeslat výsledek do jiného cíle. Při zkoumání chybějící změny, ujistěte se, **že měření změny jsou přijímány v bodě přijetí** (při spuštění funkce Azure), nikoli v cíli.

Pokud některé změny chybí v cílovém umístění, to může znamenat, že se jedná o některé chyby děje během spuštění funkce Azure po přijetí změny.

V tomto scénáři nejlepší postup je `try/catch` přidat bloky v kódu a uvnitř smyčky, které mohou být zpracování změn, zjistit jakékoli selhání pro konkrétní podmnožinu položek a odpovídajícím způsobem zpracovat je (odeslat je do jiného úložiště pro další analýzu nebo opakování). 

> [!NOTE]
> Trigger služby Azure Functions pro službu Cosmos DB ve výchozím nastavení neopakuje dávku změn, pokud při provádění kódu dojde k neošetřené výjimce. To znamená, že důvod, proč změny nedorazily do cíle, je proto, že se vám nedaří je zpracovat.

Pokud zjistíte, že některé změny nebyly přijaty vůbec aktivační událost, nejběžnější scénář je, že je **spuštěna další funkce Azure**. Může to být další funkce Azure nasazená v Azure nebo funkce Azure spuštěná místně v počítači vývojáře, která má **přesně stejnou konfiguraci** (stejné monitorované a zapůjčené kontejnery) a tato funkce Azure krade podmnožinu změn, které byste očekávali, že vaše funkce Azure zpracuje.

Kromě toho scénář může být ověřena, pokud víte, kolik instancí Azure Function App máte spuštěny. Pokud zkontrolujete kontejner zapůjčení a spočítáte počet položek `Owner` zapůjčení v rámci, odlišné hodnoty vlastnosti v nich by měla být rovna počtu instancí aplikace funkce. Pokud existuje více vlastníků než známé instance Aplikace funkce Azure, znamená to, že tyto další vlastníci jsou ty "krást" změny.

Jeden snadný způsob, jak obejít `LeaseCollectionPrefix/leaseCollectionPrefix` tuto situaci, je použít funkce s novou/ jinou hodnotu, nebo alternativně test s novým zapůjčení kontejneru.

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>Potřebujete restartovat a znovu zpracovat všechny položky v kontejneru od začátku 
Chcete-li znovu zpracovat všechny položky v kontejneru od začátku:
1. Zastavte funkci Azure, pokud je aktuálně spuštěná. 
1. Odstranit dokumenty v kolekci zapůjčení (nebo odstranit a znovu vytvořit kolekci zapůjčení tak, aby byla prázdná)
1. Nastavte [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger atribut ve vaší funkci na true. 
1. Restartujte funkci Azure. Nyní bude číst a zpracovávat všechny změny od začátku. 

Nastavení [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) na true bude říkat Azure funkce začít číst změny od začátku historie kolekce namísto aktuálníčas. To funguje pouze v případě, že neexistují žádné již vytvořené zapůjčení (to znamená dokumenty v kolekci zapůjčení). Nastavení této vlastnosti na hodnotu true, pokud jsou již vytvořeny zapůjčení, nemá žádný vliv. V tomto scénáři při zastavení funkce a restartování, začne čtení z posledního kontrolního bodu, jak je definováno v kolekci zapůjčení. Chcete-li znovu zpracovat od začátku, postupujte podle výše uvedených kroků 1-4.  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>Vazbu lze provést pouze pomocí\<> dokumentu IReadOnlyList nebo JArray.

K této chybě dojde, pokud váš projekt Azure Functions (nebo jakýkoli odkazovaný projekt) obsahuje ruční odkaz NuGet na sadku Azure Cosmos DB SDK s jinou verzí, než je verze poskytovaná [rozšířením Azure Functions Cosmos DB](./troubleshoot-changefeed-functions.md#dependencies).

Chcete-li tuto situaci obejít, odeberte ruční odkaz NuGet, který byl přidán, a nechte odkaz Azure Cosmos DB SDK vyřešit prostřednictvím balíčku rozšíření Azure Functions Cosmos DB Extension.

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>Změna intervalu dotazování funkce Azure pro zjišťování změn

Jak je vysvětleno dříve pro [moje změny trvat příliš dlouho na přijetí](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received), Azure funkce bude spát po konfigurovatelné množství času (5 sekund, ve výchozím nastavení) před kontrolou nové změny (aby se zabránilo vysoké spotřebě RU). Tuto dobu do režimu spánku můžete nakonfigurovat pomocí nastavení `FeedPollDelay/feedPollDelay` v [konfiguraci](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) vašeho triggeru (hodnota by měla být v milisekundách).

## <a name="next-steps"></a>Další kroky

* [Povolení monitorování funkcí Azure](../azure-functions/functions-monitoring.md)
* [Řešení potíží s azure cosmos DB .NET SDK](./troubleshoot-dot-net-sdk.md)
