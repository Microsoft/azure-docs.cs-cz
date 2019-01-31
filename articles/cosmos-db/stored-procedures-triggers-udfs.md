---
title: Práce s uloženými procedurami, aktivačními událostmi a uživatelem definovaných funkcí ve službě Azure Cosmos DB
description: Tento článek představuje konceptů, jako jsou uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 89cc7906c0503daa11f0a34520c17552a4e6b5af
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454211"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Uložené procedury, triggery a uživatelem definovaných funkcí

Azure Cosmos DB poskytuje integrovaný jazyk, transakční provádění jazyka JavaScript. Když ve službě Azure Cosmos DB pomocí rozhraní SQL API, můžete napsat **uložených procedur komponentami TableAdapter**, **triggery**, a **uživatelem definované funkce (UDF)** v jazyce JavaScript. Zapisovat logiku v jazyce JavaScript, který spouští v databázovém stroji. Můžete vytvářet a spouštět aktivační události, uložených procedur a uživatelem definovanými funkcemi pomocí [webu Azure portal](https://portal.azure.com/), [jazyka JavaScript integrovaná rozhraní API pro dotazy ve službě Azure Cosmos DB](javascript-query-api.md) nebo [klienta SQL API služby Cosmos DB Sady SDK](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Výhody použití programování na straně serveru

Zápis v jazyce JavaScript uložené procedury, triggery a uživatelem definované funkce (UDF) umožňuje vytvářet komplexní aplikace, které a mají následující výhody:

* **Procesní logiky:** JavaScript zde slouží jako základní programovací jazyk, který poskytuje bohatý a dobře známého rozhraní pro expresní obchodní logiku. Můžete provádět řadu složité operace s daty.

* **Atomické transakce:** Azure Cosmos DB zaručuje, že databázových operací, které se provádí v rámci jedné uložené procedury nebo aktivační události jsou atomické. Tato funkce atomic umožňuje kombinovat souvisejících operací do jedné dávce, tak, aby všechny operace úspěšná nebo žádné z nich úspěšné aplikace.

- **Výkon:** JSON data vnitřně namapována na systém typů jazyka JavaScript. Toto mapování umožňuje počet optimalizaci, jako je opožděné materializace dokumentů JSON ve fondu vyrovnávací paměti a které je k dispozici na vyžádání prováděním kódu. Existují další výhody výkonu přidružený k přesouvání obchodní logiky k databázi, která zahrnuje:

   * *Dávkové zpracování:* Lze seskupit operace, jako je vložení a odešlete je hromadně. Jsou výrazně snížit náklady na provoz latence sítě a režie úložiště k vytvoření samostatných transakcí.

   * *Předkompilace:* Uložené procedury, triggery a uživatelem definovanými funkcemi jsou implicitně předem kompilovaných na formát kódu byte předejdete tak náklady na kompilace při každém vyvolání skriptu. Z důvodu předkompilace volání uložených procedur je rychlá a má nízkým nárokům.

   * *Pořadí úloh:* Někdy potřebujete operations spouštěcí mechanismus, který může provést jednu nebo další aktualizace k datům. Kromě atomicitu existují také přinese zlepšení výkonu při spouštění na straně serveru.

- **Zapouzdření protokolu:** Uložené procedury lze použít k seskupení logiku na jednom místě. Zapouzdření přidá abstraktní vrstvu nad daty, která umožňuje vyvíjet aplikace nezávisle na data. Tato vrstva abstrakce je užitečné, když data bez schématu a není nutné spravovat, přidáte další logiku přímo do vaší aplikace. Abstrakce vám umožní vaší zajistit zabezpečení dat tak, že usnadňuje přístup ze skriptů.

> [!TIP]
> Uložené procedury jsou nejvhodnější pro operace, které jsou zápisu náročná na výkon. Při rozhodování, kam pouze pomocí uložených procedur, optimalizujte kolem zapouzdření maximální dobu, možná zápisy. Obecně řečeno uložené procedury nejsou nejúčinnější způsob plnit velkým počtem operací čtení, proto vrátí klientovi pomocí uložené procedury pro batch velký počet čtení nepřinese požadovanou výhodu.

## <a name="transactions"></a>Transakce

Transakce v databázi typické lze definovat jako posloupnost operací provedených jako jednu logickou jednotku práce. Obsahuje každou transakci **záruky modelu ACID vlastnost**. KYSELINY je známá zkratka, který zastupuje: **A**tomicity, **C**konzistence, **můžu**solation, a **D**urability. 

* Atomicitu zaručuje, že jsou všechny operace v rámci transakce jsou považovány za celek a buď všechny z nich jsou potvrzeny nebo žádná z nich. 

* Konzistence zajišťuje, že data jsou vždy v platném stavu napříč transakce. 

* Izolace zaručuje, že žádné dvě transakce konfliktu mezi sebou – řada komerčních systémů poskytují více úrovní izolace, se dají podle potřeby aplikace. 

* Odolnost zajišťuje, že každá změna, která je potvrzena v databázi budou vždy k dispozici.

Ve službě Azure Cosmos DB modulu runtime jazyka JavaScript je hostován v databázovém stroji. Proto spustit požadavky v rámci uložených procedur a aktivačních událostí ve stejném oboru jako databáze relace. Tato funkce umožňuje zajistit kyseliny vlastnosti pro všechny operace, které jsou součástí uloženou proceduru nebo aktivační událost Azure Cosmos DB. Příklady najdete v tématu [implementace transakce](how-to-write-stored-procedures-triggers-udfs.md#transactions) článku.

### <a name="scope-of-a-transaction"></a>Obor transakce

Pokud uložená procedura je přidružen ke kontejneru Azure Cosmos, uloženou proceduru spustit v oboru transakce klíč logického oddílu. Každé spuštění uložené procedury musí obsahovat hodnotu klíče logický oddíl, který odpovídá rozsahu transakce. Další informace najdete v tématu [služby Azure Cosmos DB dělení](partition-data.md) článku.

### <a name="commit-and-rollback"></a>Potvrdit změny a vrácení zpět

Transakce je nativně integrováno programovací model Azure Cosmos DB JavaScript. V rámci funkce JavaScriptu všechny operace jsou zabaleny automaticky v rámci jedné transakce. Pokud logiky JavaScript v uložené proceduře se dokončí bez jakékoli výjimky, všechny operace v rámci transakce se potvrdí do databáze. Příkazy, jako jsou `BEGIN TRANSACTION` a `COMMIT TRANSACTION` (známé do relační databáze) jsou implicitní ve službě Azure Cosmos DB. Pokud neexistují žádné výjimky ze skriptu, modul runtime Azure Cosmos DB JavaScript se vrátit zpět celou transakci. V důsledku toho je účinně ekvivalentní k vyvolání výjimky `ROLLBACK TRANSACTION` ve službě Azure Cosmos DB.

### <a name="data-consistency"></a>Konzistence dat

Uložené procedury a triggery jsou provedeny vždy na primární replice kontejneru Azure Cosmos. Tato funkce zajišťuje, který čte z nabídky uložené procedury [silnou konzistenci](consistency-levels-tradeoffs.md). Dotazy s využitím uživatelsky definovaných funkcí je možné provést v primární nebo sekundární repliku. Uložené procedury a triggery jsou určené pro podporu transakcí zápisu – mezitím logiky jen pro čtení je nejlepší implementovaný jako logiku na straně aplikace a dotazy s [sadami SDK služby Azure Cosmos DB SQL API](sql-api-dotnet-samples.md), vám pomůže saturate propustnost databáze. 

## <a name="bounded-execution"></a>Ohraničené spuštění

Všechny operace služby Azure Cosmos DB musí dokončit během zadaného časového limitu doby trvání. Toto omezení se vztahuje na funkce jazyka JavaScript - uložené procedury, triggery a uživatelem definované funkce. Pokud operace nedokončí během tohoto časového limitu, transakce je vrácena zpět.

Buď můžete zajistit, že vaše funkce jazyka JavaScript dokončit během časového limitu nebo implementovat jako model založený na pokračování pro spuštění dávky a obnovování. Aby bylo možné zjednodušit vývoj uložených procedur a aktivačních událostí ke zpracování časové limity, všechny funkce v kontejneru Azure Cosmos (například vytvoření, čtení, aktualizace a odstranění položek) vrátí logickou hodnotu, která udává, zda bude tuto operaci dokončení. Pokud je tato hodnota false, je znamením, že procedura musí shrnout spuštění, protože skript spotřebovává více času nebo zřízená propustnost než nakonfigurovaná hodnota. Operace zařazených do fronty před první operace nepřijatelného úložiště je zaručeno vyplnit, pokud uložená procedura dokončí včas a žádné další požadavky na frontu. Proto by operací ve frontě jeden po druhém pomocí konvence zpětného volání v jazyce JavaScript ke správě vašeho skriptu řízení toku. Protože skriptů jsou spouštěny v prostředí na straně serveru, jsou striktně řídit. Skripty porušující opakovaně hranice spuštění může být označen jako neaktivní a nelze ho provést, a se musí znovu vytvořit případném dalším sdílení dodržovat hranice spuštění.

Funkce jazyka JavaScript jsou také podléhá [zřízené kapacity propustnosti](request-units.md). Funkce jazyka JavaScript může potenciálně skončit pomocí velký počet jednotek žádostí za krátkou dobu a může být míra časově omezené při dosažení limitu kapacity zřízenou propustnost. Je důležité si uvědomit, že skripty využívají větší propustnost kromě propustnost trvání provádění databázových operací, i když tyto databázové operace jsou mírně levnější než provádění stejné operace z klienta.

## <a name="triggers"></a>Aktivační události

Tato část popisuje dva typy aktivačních událostí:

### <a name="pre-triggers"></a>Předběžné aktivační události

Azure Cosmos DB poskytuje triggery, které můžou vyvolat operace na položku služby Azure Cosmos DB. Můžete například zadat před aktivační událost při vytváření položky. V takovém případě před trigger se spustí předtím, než je tato položka vytvořena. Předběžné aktivační události nemůže mít žádné vstupní parametry. V případě potřeby objekt žádosti je použít k aktualizaci těla dokumentu z původního požadavku. Když aktivační události jsou registrovány, uživatelé mohou zadat operace, které můžete spustit s. Pokud se aktivační událost byla vytvořena pomocí `TriggerOperation.Create`, to znamená, že pomocí triggeru v operaci nahrazení není povoleno. Příklady najdete v tématu [jak psát triggery](how-to-write-stored-procedures-triggers-udfs.md#triggers) článku.

### <a name="post-triggers"></a>Po aktivační události

Podobně jako u předběžné aktivační události se po aktivační události jsou také přidružené operace na položku služby Azure Cosmos DB a nevyžadují žádné vstupní parametry. Mohou být spuštěny *po* operace byla dokončena a mají přístup k zprávy s odpovědí, která je odeslána do klienta. Příklady najdete v tématu [jak psát triggery](how-to-write-stored-procedures-triggers-udfs.md#triggers) článku.

## <a id="udfs"></a>Uživatelem definované funkce

Uživatelem definované funkce (UDF) slouží k rozšíření syntaxe jazyka dotaz SQL rozhraní API a snadno implementovat vlastní obchodní logiku. Může být volána pouze v rámci dotazů. Uživatelem definovanými funkcemi nemají přístup k objektu kontextu a jsou určené pro použití jako výpočetní pouze jazyka JavaScript. Proto uživatelem definovanými funkcemi, může běžet na sekundárních replikách. Příklady najdete v tématu [jak psát uživatelem definované funkce](how-to-write-stored-procedures-triggers-udfs.md#udfs) článku.

## <a id="jsqueryapi"></a>Dotaz integrovaný jazyk JavaScript API

Kromě vydávat dotazy pomocí rozhraní SQL API syntaxe dotazů, [SDK na straně serveru](https://azure.github.io/azure-cosmosdb-js-server) umožňuje provádět dotazy pomocí rozhraní JavaScript bez znalosti jazyka SQL. Dotaz jazyka JavaScript API můžete programově vytvářet dotazy předáním predikátu funkce sekvenci volání funkce. Dotazy jsou analyzovány pomocí modulu runtime jazyka JavaScript a jsou spouštěny efektivně ve službě Azure Cosmos DB. Další informace o podpoře dotazů rozhraní API jazyka JavaScript, najdete v článku [práce s jazykem JavaScript integrovaná rozhraní API pro dotazy](javascript-query-api.md) článku. Příklady najdete v tématu [jak psát uložených procedur a aktivačních událostí pomocí rozhraní API pro Javascript dotazy](how-to-write-javascript-query-api.md) článku.

## <a name="next-steps"></a>Další postup

Zjistěte, jak napsat a použít uložené procedury, triggery a uživatelem definovaných funkcí ve službě Azure Cosmos DB najdete v následujících článcích:

* [Jak napsat uložené procedury, triggery a uživatelem definovaných funkcí](how-to-write-stored-procedures-triggers-udfs.md)

* [Jak používat uložené procedury, triggery a uživatelem definovaných funkcí](how-to-use-stored-procedures-triggers-udfs.md)

* [Práce s jazykem JavaScript integrovaná rozhraní API pro dotazy](javascript-query-api.md)
