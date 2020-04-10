---
title: Práce s uloženými procedurami, aktivačními událostmi a uflvy v Azure Cosmos DB
description: Tento článek představuje koncepty, jako jsou uložené procedury, aktivační události a uživatelem definované funkce v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 13256377b8a8aaebf59196df57eef67d3b960cb8
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010541"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Uložené procedury, aktivační události a uživatelem definované funkce

Azure Cosmos DB poskytuje transakční spouštění JavaScriptu integrované do jazyka. Při použití rozhraní SQL API v Azure Cosmos DB můžete zapisovat **uložené procedury**, **aktivační události**a **uživatelem definované funkce (UOF)** v jazyce JavaScript. Vlastní logiku můžete psát v JavaScriptu, který se spouští v databázovém stroji. Aktivační události, uložené procedury a ufls můžete vytvářet a spouštět pomocí [portálu Azure Portal](https://portal.azure.com/), [rozhraní API integrovaného dotazu v jazyce JavaScript v azure cosmos DB](javascript-query-api.md) nebo [sadách SDK klienta Cosmos DB SQL API](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Výhody použití programování na straně serveru

Zápis uložených procedur, aktivačních událostí a uživatelem definovaných funkcí (UDF) v Jazyce JavaScript umožňuje vytvářet bohaté aplikace a mají následující výhody:

* **Procedurální logika:** JavaScript jako programovací jazyk na vysoké úrovni, který poskytuje bohaté a známé rozhraní pro vyjádření obchodní logiky. Můžete provést posloupnost složitých operací na data.

* **Atomové transakce:** Azure Cosmos DB zaručuje, že databázové operace, které se provádějí v rámci jedné uložené procedury nebo aktivační události jsou atomické. Tato atomická funkce umožňuje aplikaci kombinovat související operace do jedné dávky, takže všechny operace úspěšné nebo žádná z nich úspěšné.

* **Výkon:** Data JSON jsou vnitřně mapována na systém typu jazyka JavaScript. Toto mapování umožňuje řadu optimalizací, jako je opožděné materializace dokumentů JSON ve fondu vyrovnávací paměti a jejich zpřístupnění na vyžádání pro vykonávající kód. Existují další výhody výkonu spojené s odesílání obchodní logiky do databáze, která zahrnuje:

   * *Dávkování:* Operace můžete seskupit, jako jsou vložení, a odeslat je hromadně. Náklady na latenci síťového provozu a režie úložiště pro vytvoření samostatných transakcí se výrazně sníží.

   * *Před kompilací:* Uložené procedury, aktivační události a UDf sazby jsou implicitně předem kompilovány do formátu bajtového kódu, aby se zabránilo nákladům na kompilaci v době každého vyvolání skriptu. Vzhledem k pre-kompilace, vyvolání uložené procedury je rychlý a má nízkou stopu.

   * *Sekvencování:* Někdy operace potřebují spouštěcí mechanismus, který může provádět jednu nebo další aktualizace dat. Kromě atomicity, existují také výhody výkonu při provádění na straně serveru.

* **Zapouzdření:** Uložené procedury lze použít k seskupení logiky na jednom místě. Zapouzdření přidá vrstvu abstrakce nad data, která umožňuje vyvíjet aplikace nezávisle na datech. Tato vrstva abstrakce je užitečná, když data jsou bez schématu a není třeba spravovat přidání další logiky přímo do aplikace. Abstrakce umožňuje udržovat data v bezpečí zefektivněním přístupu ze skriptů.

> [!TIP]
> Uložené procedury jsou nejvhodnější pro operace, které jsou náročné na zápis a vyžadují transakci přes hodnotu klíče oddílu. Při rozhodování, zda použít uložené procedury, optimalizovat kolem zapouzdření maximální možné množství zápisů. Obecně řečeno, uložené procedury nejsou nejúčinnější prostředky pro provádění velkého počtu operací čtení nebo dotazu, takže použití uložené procedury k dávkové velké množství čtení vrátit klientovi nepřinese požadovanou výhodu. Pro nejlepší výkon tyto operace náročné na čtení by mělo být provedeno na straně klienta pomocí Cosmos SDK. 

## <a name="transactions"></a>Transakce

Transakce v typické databázi může být definována jako posloupnost operací prováděných jako jedna logická jednotka práce. Každá transakce poskytuje **záruky vlastností ACID**. ACID je dobře-známý zkratka, která znamená: **tomicity,** **C**onsistency, **I**solation, a **D**urability. 

* Nedělitelnost zaručuje, že všechny operace provedené uvnitř transakce jsou považovány za jednu jednotku a buď všechny z nich jsou potvrzeny nebo žádné z nich. 

* Konzistence zajišťuje, že data jsou vždy v platném stavu napříč transakcemi. 

* Izolace zaručuje, že žádné dvě transakce vzájemně zasahovat – mnoho komerčních systémů poskytují více úrovní izolace, které lze použít na základě potřeb aplikace. 

* Trvanlivost zajišťuje, že všechny změny, které je potvrzena v databázi bude vždy k dispozici.

V Azure Cosmos DB je modul runtime JavaScript hostovaný uvnitř databázového stroje. Proto požadavky provedené v rámci uložené procedury a aktivační události spustit ve stejném oboru jako relace databáze. Tato funkce umožňuje Azure Cosmos DB zaručit acid vlastnosti pro všechny operace, které jsou součástí uložené procedury nebo aktivační události. Příklady naleznete v [tématu, jak implementovat transakce](how-to-write-stored-procedures-triggers-udfs.md#transactions) článku.

### <a name="scope-of-a-transaction"></a>Rozsah transakce

Uložené procedury jsou přidruženy ke kontejneru Azure Cosmos a provádění uložené procedury je vymezeno na klíč logického oddílu. Uložené procedury musí obsahovat hodnotu klíče logického oddílu během provádění, která definuje logický oddíl pro rozsah transakce. Další informace najdete v článku [dělení Azure Cosmos DB.](partition-data.md)

### <a name="commit-and-rollback"></a>Potvrzení a vrácení zpět

Transakce se nativně integrují do programovacího modelu JavaScriptu Azure Cosmos DB. V rámci funkce JavaScript jsou všechny operace automaticky zabaleny do jedné transakce. Pokud logika Jazyka JavaScript v uložené proceduře dokončí bez výjimky, všechny operace v rámci transakce jsou potvrzeny do databáze. Příkazy `BEGIN TRANSACTION` `COMMIT TRANSACTION` jako a (známé relační databáze) jsou implicitní v Azure Cosmos DB. Pokud existují nějaké výjimky ze skriptu, runtime JavaScript Azure Cosmos DB vrátí zpět celou transakci. Jako takové vyvolání výjimky je efektivně `ROLLBACK TRANSACTION` ekvivalentní v Azure Cosmos DB.

### <a name="data-consistency"></a>Konzistence dat

Uložené procedury a aktivační události jsou vždy spouštěny na primární repliku kontejneru Azure Cosmos. Tato funkce zajišťuje, že čtení z uložených procedur nabízí [silnou konzistenci](consistency-levels-tradeoffs.md). Dotazy pomocí uživatelem definovaných funkcí lze provést na primární nebo sekundární repliky. Uložené procedury a aktivační události jsou určeny pro podporu transakční zápisy – mezitím logika jen pro čtení je nejlépe implementována jako logika na straně aplikace a dotazy pomocí [Azure Cosmos DB SQL API SDK](sql-api-dotnet-samples.md), vám pomůže nasytit propustnost databáze. 

## <a name="bounded-execution"></a>Omezené spouštění

Všechny operace Azure Cosmos DB musí být dokončeny v rámci zadané doby trvání časového roku. Toto omezení platí pro funkce Jazyka JavaScript – uložené procedury, aktivační události a uživatelem definované funkce. Pokud operace není dokončena v tomto časovém limitu, transakce je vrácena zpět.

Můžete buď zajistit, že vaše funkce JavaScriptdokončit v časovém limitu nebo implementovat model založený na pokračování dávkové/obnovení spuštění. Aby se zjednodušil vývoj uložených procedur a aktivačních událostí pro zpracování časových limitů, všechny funkce v rámci kontejneru Azure Cosmos (například vytvořit, číst, aktualizovat a odstranit položky) vrátí logickou hodnotu, která představuje, zda bude tato operace dokončena. Pokud je tato hodnota false, je označení, že postup musí zabalit spuštění, protože skript spotřebovává více času nebo zřízená propustnost než nakonfigurovaná hodnota. Operace zařazené do fronty před první nepřijatou operací úložiště jsou zaručeně dokončeny, pokud se uložená procedura dokončí včas a nezařadí do fronty žádné další požadavky. Operace by proto měly být zařazeny do fronty po jednom pomocí konvence zpětného volání javascriptu ke správě toku řízení skriptu. Vzhledem k tomu, že skripty jsou spouštěny v prostředí na straně serveru, jsou přísně řízeny. Skripty, které opakovaně porušují hranice provádění, mohou být označeny jako neaktivní a nelze je spustit a měly by být znovu vytvořeny, aby byly doznány hranice provádění.

Funkce JavaScriptu také podléhají [zřízené kapacitě propustnosti](request-units.md). Funkce JavaScriptu by mohly potenciálně skončit pomocí velkého počtu jednotek požadavku v krátkém čase a může být omezena rychlost, pokud je dosaženo limitu kapacity zřízeného propustnosti. Je důležité si uvědomit, že skripty spotřebovávají další propustnost kromě propustnosti strávené provádění databázových operací, i když tyto databázové operace jsou o něco levnější než provádění stejných operací z klienta.

## <a name="triggers"></a>Aktivační události

Azure Cosmos DB podporuje dva typy triggerů:

### <a name="pre-triggers"></a>Triggery před akcí

Azure Cosmos DB poskytuje triggery, které je možné vyvolat provedením operace s položkou Azure Cosmos. Můžete například určit trigger před akcí vytvoření položky. V tomto případě se trigger před akcí spustí před vytvořením položky. Triggery před akcí nesmí mít žádné vstupní parametry. V případě potřeby je možné k aktualizaci těla dokumentu z původního požadavku použít objekt požadavku. Po zaregistrování triggerů můžou uživatelé určit operace, se kterými se můžou spouštět. Pokud aktivační událost `TriggerOperation.Create`byla vytvořena s , to znamená, že použití aktivační události v operaci nahrazení nebude povoleno. Příklady najdete v tématu [Jak psát aktivační události](how-to-write-stored-procedures-triggers-udfs.md#triggers) článku.

### <a name="post-triggers"></a>Triggery po akci

Podobně jako před aktivační události, post-triggers, jsou také přidruženy k operaci na položku Azure Cosmos a nevyžadují žádné vstupní parametry. Jejich spuštění *po* dokončení operace a mají přístup ke zprávě odpovědi, která je odeslána klientovi. Příklady najdete v tématu [Jak psát aktivační události](how-to-write-stored-procedures-triggers-udfs.md#triggers) článku.

> [!NOTE]
> Registrované aktivační události se nespustí automaticky, když se jejich odpovídající operace (vytvořit / odstranit / nahradit / aktualizovat) stalo. Při provádění těchto operací se musí explicitně zavolat. Další informace najdete v článku [o spuštění aktivačních událostí.](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers)

## <a name="user-defined-functions"></a><a id="udfs"></a>Uživatelsky definované funkce

[Uživatelem definované funkce](sql-query-udfs.md) (UD) se používají k rozšíření syntaxe dotazovacího jazyka ROZHRANÍ SQL API a snadné implementaci vlastní obchodní logiky. Mohou být volány pouze v rámci dotazů. UDFnemají přístup k objektu kontextu a jsou určeny k použití jako výpočetní pouze JavaScript. Proto udfs lze spustit na sekundární repliky. Příklady naleznete v tématu [Jak psát uživatelem definované funkce](how-to-write-stored-procedures-triggers-udfs.md#udfs) článku.

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>Rozhraní API dotazů integrovaných jazykem JavaScriptu

Kromě vydávání dotazů pomocí syntaxe dotazu rozhraní SQL API umožňuje sada [SDK](https://azure.github.io/azure-cosmosdb-js-server) na straně serveru provádět dotazy pomocí rozhraní JavaScript bez znalosti jazyka SQL. JavaScript dotaz API umožňuje programově vytvářet dotazy předáním predikátu funkce do posloupnosti volání funkce. Dotazy jsou analyzovány v době runtime JavaScript a jsou spouštěny efektivně v rámci Azure Cosmos DB. Další informace o podpoře rozhraní API dotazů javascriptu najdete [v článku Práce s jazykem JavaScript integrovaným rozhraním API dotazu.](javascript-query-api.md) Příklady najdete v tématu Jak psát uložené procedury a aktivační události pomocí článku [javascriptového rozhraní API dotazu.](how-to-write-javascript-query-api.md)

## <a name="next-steps"></a>Další kroky

Zjistěte, jak psát a používat uložené procedury, aktivační události a uživatelem definované funkce v Azure Cosmos DB s následujícími články:

* [Jak psát uložené procedury, aktivační události a uživatelem definované funkce](how-to-write-stored-procedures-triggers-udfs.md)

* [Použití uložených procedur, aktivačních událostí a uživatelem definovaných funkcí](how-to-use-stored-procedures-triggers-udfs.md)

* [Práce s rozhraním API pro integrované dotazy v jazyce JavaScript](javascript-query-api.md)
