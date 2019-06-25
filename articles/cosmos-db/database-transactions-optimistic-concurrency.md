---
title: Databázové transakce a optimistického řízení souběžnosti ve službě Azure Cosmos DB
description: Tento článek popisuje databázové transakce a optimistického řízení souběžnosti ve službě Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 1da5dabad04d72c903072a33dfb7b0229f99c62d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978991"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Řízení optimistické souběžnosti a transakce

Databázové transakce poskytují bezpečné a předvídatelné programovací model řešit souběžných změny v datech. Tradičních relačních databází, jako je SQL Server, umožňuje psát obchodní logiku pomocí uložené procedury a triggery, odeslat ho na server pro spuštění přímo uvnitř databázového stroje. Pomocí tradičních relačních databází, je nutné řešit dva různé programovací jazyky (netransakční) aplikačního programovacího jazyka, jako je JavaScript, Python, C#, Java, atd. a transakční programovací jazyk ( například T-SQL), která nativně provádí databáze.

Databázový stroj ve službě Azure Cosmos DB podporuje úplnou kyseliny (atomicitu, konzistence, izolaci, odolnosti) kompatibilní s transakcí s izolací snímku. Všechny databázové operace v rámci oboru kontejneru [logický oddíl](partition-data.md) jsou transakčně spouštět přímo z databázového stroje, který je hostitelem repliky oddílu. Obě tyto operace zahrnují operace čtení a zápisu (aktualizuje jednu nebo více položek v rámci logického oddílu). Následující tabulka ilustruje různé operace a typy transakcí:

| **Operace**  | **Typ operace** | **Jeden nebo více položek transakce** |
|---------|---------|---------|
| Vložit (bez předzálohovacího nebo pozálohovacího aktivační události) | Zápis | Transakce jednu položku |
| Vložit (triggerem předzálohovacího nebo pozálohovacího) | Zápis a čtení | Transakce více položek |
| Nahradit (bez předzálohovacího nebo pozálohovacího aktivační události) | Zápis | Transakce jednu položku |
| Nahraďte (aktivační událost předzálohovacího nebo pozálohovacího) | Zápis a čtení | Transakce více položek |
| Upsert (bez předzálohovacího nebo pozálohovacího aktivační události) | Zápis | Transakce jednu položku |
| Upsert (triggerem předzálohovacího nebo pozálohovacího) | Zápis a čtení | Transakce více položek |
| Odstranit (bez předzálohovacího nebo pozálohovacího aktivační události) | Zápis | Transakce jednu položku |
| Odstranit (triggerem předzálohovacího nebo pozálohovacího) | Zápis a čtení | Transakce více položek |
| Spustit uloženou proceduru | Zápis a čtení | Transakce více položek |
| Provádění procedury sloučení zahájené systému | Zápis | Transakce více položek |
| Systém zahájené spuštění odstranění položky podle vypršení platnosti (TTL) položky | Zápis | Transakce více položek |
| Čtení | Čtení | Transakce jedné položky |
| Kanál změn | Čtení | Transakce více položek |
| Stránkovaná pro čtení | Čtení | Transakce více položek |
| Stránkovaných dotazů | Čtení | Transakce více položek |
| Spuštění UDF jako součást stránkovaných dotazů | Čtení | Transakce více položek |

## <a name="multi-item-transactions"></a>Transakce více položek

Azure Cosmos DB umožňuje psát [uložené procedury, triggery předzálohovacího nebo pozálohovacího uživatelem definované – funkce (UDF)](stored-procedures-triggers-udfs.md) a sloučit procedury v jazyce JavaScript. Azure Cosmos DB nativně podporuje spouštění JavaScriptu uvnitř jeho databázový stroj. Můžete zaregistrovat uložené procedury, předzálohovacího nebo pozálohovacího aktivační události, uživatelem definované – funkce (UDF) a postupy sloučení na kontejner a později je spouštět transakčně přímo z databázového stroje Azure Cosmos. Psaní aplikační logiky v jazyce JavaScript umožňuje přirozené vyjádření tok řízení, proměnné rozsahu, přiřazení a integrace primitivních elementů v rámci databázové transakce přímo v jazyce JavaScript zpracování výjimek.

Bázi jazyka JavaScript uložené procedury, aktivační události, uživatelem definovanými funkcemi a sloučení postupy jsou zabaleny v rámci ambientní transakci ACID s izolací snímku přes všechny položky v rámci logického oddílu. V průběhu jeho spuštění Pokud program jazyka JavaScript vyvolá výjimku, celá transakce byla přerušena a vrácena zpět. Výsledný programovací model je jednoduchý výkonné. Vývojáře v JavaScriptu získejte odolné programovací model, zatímco stále pomocí jejich dobře známého jazyka vytvoří a knihovny primitiv.

Spouštění jazyka JavaScript přímo uvnitř databázového stroje zajišťuje výkon a transakční provádění databázových operací u položek kontejneru. Navíc vzhledem modul databáze Azure Cosmos nativně podporuje JSON a JavaScript, není žádný vzniklé vzájemné napětí Neshoda mezi systémy typ aplikace a databáze.

## <a name="optimistic-concurrency-control"></a>Optimistického řízení souběžnosti 

Optimistického řízení souběžnosti umožňuje zabránit ztrátou určitých aktualizací a odstraní. Souběžné, konfliktní operace podléhají regulární pesimistické zamykání databázového stroje hostuje logický oddíl, který vlastní položku. Při pokusu o dvě souběžná operace aktualizovat nejnovější verzi položky v rámci logického oddílu, jeden z nich vyhraje a druhý se nezdaří. Nicméně pokud jedna nebo dvě operace pokus o aktualizaci souběžně jedné položce bylo dříve přečtené starší hodnota položky, databáze nebude vědět, pokud dříve čtení podle jedné nebo obou konfliktní operace byla zadána hodnota skutečně nejnovější hodnotu položky. Naštěstí můžete zjistit této situaci se **řízení přístupu (optimistického řízení souběžnosti OCC)** před s informacemi zadejte dvě operace transakce hranice v databázovém stroji. OCC chrání vaše data před náhodnému přepsání změny provedené jinými uživateli. Je také zabrání ostatním náhodnému přepsání vlastní změny.

Souběžná aktualizace položky podléhají OCC vrstvou komunikace protokolu služby Azure Cosmos DB. Databáze Azure Cosmos se zajistí, že klientské verze položky, která jsou aktualizace (nebo odstranění) je stejná jako verze položky v kontejneru Azure Cosmos. Zaručí se tak, že zápisů jsou chráněny před přepsáním omylem podle zápisy ostatních a naopak. V prostředí s více uživateli optimistického řízení souběžnosti ovládacím prvku chrání proti náhodnému odstranění nebo aktualizaci chybná verze položky. V důsledku toho jsou položky chráněné proti nechvalně známý "ztracené aktualizace" nebo "ztráty delete" problémy.

Systém definované měla každá položka uložená v kontejneru Azure Cosmos `_etag` vlastnost. Hodnota `_etag` automaticky vygeneruje a aktualizovat server pokaždé, když je položka aktualizována. `_etag` je možné s zadaná klientem `if-match` hlavičku požadavku umožňující server se rozhodnout, zda lze položky aktualizovat podmíněně. Hodnota `if-match` záhlaví shoduje s hodnotou `_etag` na serveru, se pak aktualizuje položku. Pokud hodnota `if-match` hlavičku požadavku už není aktuální, server zamítne operaci s "HTTP 412 selhání předběžné podmínky" zprávy s odpovědí. Klient pak může položku znovu načtěte k získání aktuální verze položky na serveru nebo přepsání verze položky na serveru s vlastním `_etag` hodnotu pro položku. Kromě toho `_etag` jde použít s `if-none-match` hlavičky k určení, jestli je potřeba znovu načíst prostředku. 

Položky `_etag` hodnota změny pokaždé, když je položka aktualizována. Pro operace položku nahradit `if-match` musí být explicitně vyjádřena jako součást možnosti žádosti. Příklad najdete v tématu ukázkový kód v [Githubu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` hodnoty jsou implicitně kontroluje všechny položky písemné přistupovala uloženou proceduru. V případě jakéhokoli konfliktu se detekuje, uložené procedury se vraťte transakci zpět a vyvolají výjimku. Pomocí této metody se používají atomicky all nebo žádný zápisy v rámci uložené procedury. Toto je signál, který se aplikace znovu použít aktualizace a zkuste to znovu původní požadavek klienta.

## <a name="next-steps"></a>Další postup

Další informace o databázové transakce a optimistického řízení souběžnosti v následujících článcích:

- [Práce s databází Azure Cosmos, kontejnery a položek](databases-containers-items.md)
- [Úrovně konzistence](consistency-levels.md)
- [Zásady rozlišení a typy konfliktů](conflict-resolution-policies.md)
- [Uložené procedury, triggery a uživatelem definovaných funkcí](stored-procedures-triggers-udfs.md)
