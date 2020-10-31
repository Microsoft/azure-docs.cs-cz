---
title: Práce s uloženými procedurami, triggery a UDF v Azure Cosmos DB
description: V tomto článku se seznámíte s koncepty, jako jsou uložené procedury, triggery a uživatelsky definované funkce v Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 7dc81581846f8abdae81fa3552d9fa4645f32a05
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101302"
---
# <a name="stored-procedures-triggers-and-user-defined-functions"></a>Uložené procedury, triggery a uživatelsky definované funkce
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB poskytuje transakční spouštění JavaScriptu integrované do jazyka. Při použití rozhraní SQL API v Azure Cosmos DB můžete v jazyce JavaScript zapisovat **uložené procedury** , **triggery** a **uživatelsky definované funkce (UDF)** . Vlastní logiku můžete psát v JavaScriptu, který se spouští v databázovém stroji. Můžete vytvářet a spouštět triggery, uložené procedury a UDF pomocí [Azure Portal](https://portal.azure.com/), [integrovaného rozhraní Query API jazyka JavaScript v Azure Cosmos DB](javascript-query-api.md) nebo klientských SADÁCH [SDK Cosmos DB SQL API](how-to-use-stored-procedures-triggers-udfs.md).

## <a name="benefits-of-using-server-side-programming"></a>Výhody používání programování na straně serveru

Zápis uložených procedur, triggerů a uživatelsky definovaných funkcí (UDF) v JavaScriptu vám umožní vytvářet bohatý aplikace a mají následující výhody:

* **Procesní logika:** JavaScript jako programovací jazyk vysoké úrovně, který poskytuje bohatou a známou rozhraní pro Express Business Logic. Můžete provést sekvenci komplexních operací s daty.

* **Atomické transakce:** Operace Azure Cosmos DB Database, které se provádějí v rámci jedné uložené procedury nebo triggeru, jsou atomické. Tato atomická funkce umožňuje aplikaci zkombinovat související operace do jedné dávky, aby všechny operace byly úspěšné nebo žádné z nich nebyly úspěšné.

* **Výkon:** Data JSON jsou vnitřně mapována na systém typů jazyka JavaScript. Toto mapování umožňuje několik optimalizací, jako je opožděné dematerializace dokumentů JSON ve fondu vyrovnávací paměti a jejich zpřístupnění na vyžádání spouštěcímu kódu. K odeslání obchodní logiky do databáze jsou k dispozici další výhody související s výkonem, které zahrnují:

   * *Dávkování:* Můžete seskupit operace, jako je vložení, a hromadně je odeslat. Náklady na latenci síťového provozu a nároky na úložiště pro vytvoření samostatných transakcí jsou výrazně omezené.

   * *Před kompilací:* Uložené procedury, triggery a UDF jsou implicitně kompilovány do formátu bajtového kódu, aby se předešlo nákladům na kompilaci v době vyvolání každého skriptu. Z důvodu předkompilace je vyvolání uložených procedur rychlé a má nízké nároky.

   * *Sekvence:* V některých případech potřebují operace aktivační mechanismus, který může provést jednu nebo více aktualizací dat. Kromě nedělitelnost existují i výhody výkonu při spouštění na straně serveru.

* **Zapouzdření:** Uložené procedury lze použít k seskupení logiky na jednom místě. Zapouzdření přidá vrstvu abstrakce nad daty, která umožňuje vyvíjet aplikace nezávisle na datech. Tato vrstva abstrakce je užitečná v případě, že jsou data méně schématu a není nutné spravovat další logiku přímo do vaší aplikace. Abstrakce umožňuje zajistit zabezpečení dat tím, že zjednodušuje přístup ze skriptů.

> [!TIP]
> Uložené procedury jsou nejvhodnější pro operace, které jsou náročné na zápis a vyžadují transakci napříč hodnotou klíče oddílu. Při rozhodování, zda chcete použít uložené procedury, optimalizujte z zapouzdření maximálního možného množství zápisů. Obecně řečeno, uložené procedury nejsou nejúčinnějším prostředkem pro provádění velkých objemů operací čtení nebo dotazování, takže při použití uložených procedur ke dávkovému zpracování velkého počtu čtení pro návrat do klienta nepřinese požadovanou výhodu. Pro dosažení co nejlepších výsledků je potřeba tyto operace čtení-těžkých operací provádět na straně klienta pomocí sady Cosmos SDK. 

## <a name="transactions"></a>Transakce

Transakce v typické databázi může být definována jako posloupnost operací prováděná jako jediná logická jednotka práce. Každá transakce poskytuje **záruky vlastností kyselosti** . KYSELost je známý akronym, **který představuje: tomicity,** **C** onsistency, **I** solation a **D** urability. 

* Nedělitelnost zaručuje, že všechny operace provedené uvnitř transakce jsou považovány za jednu jednotku a všechny z nich jsou potvrzeny nebo žádné z nich. 

* Konzistence zajistí, že data jsou vždy v platném stavu napříč transakcemi. 

* Izolace zaručuje, že žádné dvě transakce neovlivňují vzájemnou činnost – mnoho komerčních systémů poskytuje několik úrovní izolace, které se dají použít v závislosti na potřebách aplikace. 

* Odolnost zajišťuje, že všechny změny, které jsou potvrzené v databázi, budou vždy k dispozici.

V Azure Cosmos DB je JavaScript Runtime hostovaný v databázovém stroji. Proto požadavky vytvořené v rámci uložených procedur a triggerů se spustí ve stejném oboru jako relace databáze. Tato funkce umožňuje Azure Cosmos DB zaručit vlastnosti KYSELosti u všech operací, které jsou součástí uložené procedury nebo triggeru. Příklady naleznete v článku [How to Implementing Transactions](how-to-write-stored-procedures-triggers-udfs.md#transactions) .

### <a name="scope-of-a-transaction"></a>Rozsah transakce

Uložené procedury jsou přidružené k kontejneru Azure Cosmos a provádění uložených procedur je vymezeno na klíč logického oddílu. Uložené procedury musí zahrnovat hodnotu klíče logického oddílu během provádění, která definuje logický oddíl pro obor transakce. Další informace najdete v článku o [dělení Azure Cosmos DB](partitioning-overview.md) .

### <a name="commit-and-rollback"></a>Potvrdit změny a vrátit se zpátky

Transakce jsou nativně integrované do programovacího modelu Azure Cosmos DB JavaScript. V rámci funkce JavaScriptu jsou všechny operace automaticky zabaleny do jediné transakce. Pokud je logika JavaScriptu v uložené proceduře dokončena bez výjimek, všechny operace v rámci transakce jsou potvrzeny do databáze. Příkazy jako `BEGIN TRANSACTION` a `COMMIT TRANSACTION` (známé pro relační databáze) jsou implicitní v Azure Cosmos DB. Pokud ze skriptu existují výjimky, modul runtime Azure Cosmos DB JavaScript vrátí zpět celou transakci. V takovém případě je vyvolání výjimky účinně ekvivalentní s a `ROLLBACK TRANSACTION` v Azure Cosmos DB.

### <a name="data-consistency"></a>Konzistence dat

Uložené procedury a triggery se vždycky spouštějí na primární replice kontejneru Azure Cosmos. Tato funkce zajišťuje, že čtení z uložených procedur nabízí [silnou konzistenci](./consistency-levels.md). Dotazy využívající uživatelsky definované funkce lze provádět na primární nebo libovolné sekundární replice. Uložené procedury a triggery jsou určené k podpoře transakčních zápisů – zatímco logika jen pro čtení se nejlépe implementuje jako logika na straně aplikace a dotazy pomocí [Azure Cosmos DB SQL API SDK](sql-api-dotnet-samples.md), vám pomůže sytost propustnosti databáze. 

> [!TIP]
> Dotazy, které byly provedeny v uložené proceduře nebo triggeru, nemusí zobrazit změny v položkách provedených stejnou transakcí skriptu. Tento příkaz se vztahuje na dotazy SQL, jako je například, i na `getContent().getCollection.queryDocuments()` integrované jazykové dotazy, jako je `getContext().getCollection().filter()` .

## <a name="bounded-execution"></a>Omezené spouštění

Všechny operace Azure Cosmos DB musí být dokončeny v rámci zadaného časového limitu. Toto omezení se vztahuje na funkce jazyka JavaScript – uložené procedury, triggery a uživatelsky definované funkce. Pokud se operace v tomto časovém limitu nedokončí, transakce se vrátí zpět.

Můžete buď zajistit, aby se funkce JavaScriptu dokončily v rámci časového limitu, nebo implementovat model založený na pokračování pro dávku nebo pokračování v provádění. Aby bylo možné zjednodušit vývoj uložených procedur a triggerů za účelem zpracování časových omezení, všechny funkce v kontejneru Azure Cosmos (například vytváření, čtení, aktualizace a odstraňování položek) vrací logickou hodnotu, která představuje, zda bude tato operace dokončena. Pokud je tato hodnota false, znamená to, že procedura musí zabalit provádění, protože skript spotřebovává více času nebo zřídil propustnost, než je nakonfigurovaná hodnota. Operace zařazené do fronty před prvním nepřijatnou operací úložiště jsou zaručené k dokončení, pokud se uložená procedura dokončí v čase, a nezařazuje žádné další požadavky. Operace by tedy měly být zařazeny po jednom pomocí konvence zpětného volání JavaScriptu ke správě toku řízení skriptu. Vzhledem k tomu, že se skripty spouštějí v prostředí na straně serveru, jsou výhradně řízeny. Skripty, které opakovaně narušují hranice provádění, mohou být označeny jako neaktivní a nelze je spustit, aby bylo možné akceptovat hranice spouštění.

Funkce JavaScriptu také podléhají [zřízené kapacitě propustnosti](request-units.md). Funkce JavaScriptu můžou po krátkou dobu končit velkým počtem jednotek žádostí a můžou být omezené na frekvenci, pokud jste dosáhli limitu kapacity zajištěné propustnosti. Je důležité si uvědomit, že skripty využívají další propustnost kromě propustnosti strávené prováděním databázových operací, i když tyto databázové operace jsou mírně levnější než provádění stejných operací od klienta.

## <a name="triggers"></a>Aktivační události

Azure Cosmos DB podporuje dva typy triggerů:

### <a name="pre-triggers"></a>Triggery před akcí

Azure Cosmos DB poskytuje triggery, které je možné vyvolat provedením operace s položkou Azure Cosmos. Můžete například určit trigger před akcí vytvoření položky. V tomto případě se trigger před akcí spustí před vytvořením položky. Triggery před akcí nesmí mít žádné vstupní parametry. V případě potřeby je možné k aktualizaci těla dokumentu z původního požadavku použít objekt požadavku. Po zaregistrování triggerů můžou uživatelé určit operace, se kterými se můžou spouštět. Pokud se vytvoří trigger s operací `TriggerOperation.Create`, znamená to, že tento trigger nebude možné použít v operaci nahrazení. Příklady najdete v článku [Jak napsat triggery](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

### <a name="post-triggers"></a>Triggery po akci

Podobně jako předběžné triggery jsou po triggerech přidružené k operaci na položce Azure Cosmos a nevyžadují žádné vstupní parametry. Jsou spouštěny *po* dokončení operace a mají přístup ke zprávě odpovědi, která je odeslána klientovi. Příklady najdete v článku [Jak napsat triggery](how-to-write-stored-procedures-triggers-udfs.md#triggers) .

> [!NOTE]
> Zaregistrované triggery se nespustí automaticky, když dojde k odpovídajícím operacím (vytvořit/odstranit/nahradit/aktualizovat). Při provádění těchto operací se musí explicitně zavolat. Další informace najdete v článku [Jak spustit triggery](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) .

## <a name="user-defined-functions"></a><a id="udfs"></a>Uživatelsky definované funkce

[Uživatelsky definované funkce](sql-query-udfs.md) (UDF) slouží k rozšiřování syntaxe jazyka dotazů rozhraní SQL API a snadné implementaci vlastní obchodní logiky. Mohou být volány pouze v rámci dotazů. UDF nemají přístup k objektu kontextu a jsou určeny pro použití pouze COMPUTE JavaScript. Proto je možné spustit UDF na sekundárních replikách. Příklady najdete v článku [Postup zápisu uživatelem definovaných funkcí](how-to-write-stored-procedures-triggers-udfs.md#udfs) .

## <a name="javascript-language-integrated-query-api"></a><a id="jsqueryapi"></a>Rozhraní API pro integrované dotazy jazyka JavaScript

Kromě vydávání dotazů pomocí syntaxe dotazů rozhraní SQL API umožňuje [sada SDK na straně serveru](https://azure.github.io/azure-cosmosdb-js-server) provádět dotazy pomocí rozhraní JavaScript bez znalosti SQL. Rozhraní API pro dotazy jazyka JavaScript umožňuje programově vytvářet dotazy předáním funkcí predikátu do sekvence volání funkcí. Dotazy jsou analyzovány modulem runtime jazyka JavaScript a jsou prováděny efektivně v rámci Azure Cosmos DB. Další informace o podpoře rozhraní API pro JavaScript najdete v článku [práce s integrovaným dotazovým rozhraním API jazyka JavaScript](javascript-query-api.md) . Příklady najdete v článku [jak zapisovat uložené procedury a triggery pomocí rozhraní API dotazů jazyka JavaScript](how-to-write-javascript-query-api.md) .

## <a name="next-steps"></a>Další kroky

Naučte se psát a používat uložené procedury, triggery a uživatelsky definované funkce v Azure Cosmos DB s následujícími články:

* [Postup zápisu uložených procedur, triggerů a uživatelsky definovaných funkcí](how-to-write-stored-procedures-triggers-udfs.md)

* [Jak používat uložené procedury, triggery a uživatelsky definované funkce](how-to-use-stored-procedures-triggers-udfs.md)

* [Práce s integrovaným rozhraním API pro integrované dotazy jazyka JavaScript](javascript-query-api.md)