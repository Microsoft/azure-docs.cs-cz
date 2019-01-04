---
title: Databázové transakce a optimistického řízení souběžnosti ve službě Azure Cosmos DB
description: Tento článek popisuje databázové transakce a optimistického řízení souběžnosti ve službě Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ddd3b7a859e48e3212d8d51d627eea2e69c7c1ff
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54024701"
---
# <a name="database-transactions-and-optimistic-concurrency-control"></a>Databázové transakce a optimistického řízení souběžnosti

Databázové transakce poskytují bezpečné a předvídatelné programovací model řešit souběžných změny v datech. Tradičních relačních databází, jako je SQL Server umožňuje psát obchodní logiku pomocí uložené procedury a triggery, odeslat ho na server pro spuštění přímo uvnitř databázového stroje. Pomocí tradičních relačních databází, je nutné řešit dvě různé programovací jazyky - (netransakční) aplikačního programovacího jazyka, jako je JavaScript, Python, C#, Java, atd. a transakční programovací jazyk (T-SQL), která je nativně proveden v databázi.

Databázový stroj ve službě Azure Cosmos DB podporuje úplnou kyseliny (atomicitu, konzistence, izolaci, odolnosti) kompatibilní s transakcí s izolací snímku. Všechny databázové operace v rámci oboru kontejneru logického oddílu jsou transakčně spouštět přímo z databázového stroje, který je hostitelem repliky oddílu. Obě tyto operace zahrnují operace čtení a zápisu (aktualizuje jednu nebo více položek v rámci logického oddílu). Následující tabulka ilustruje různé operace a transakce typech:

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

Azure Cosmos DB umožňuje zápis uložené procedury, triggery předzálohovacího nebo pozálohovacího, uživatel definované – funkce (UDF) a sloučení procedury v jazyce JavaScript. Azure Cosmos DB nativně podporuje spouštění JavaScriptu uvnitř jeho databázový stroj. Můžete zaregistrovat uložené procedury, předzálohovacího nebo pozálohovacího aktivační události, uživatelem definované – funkce (UDF) a postupy sloučení na kontejner a později je spouštět transakčně přímo z databázového stroje Azure Cosmos. Psaní aplikační logiky v jazyce JavaScript umožňuje přirozené vyjádření tok řízení, proměnné rozsahu, přiřazení a integrace primitivních elementů v rámci databázové transakce přímo v jazyce JavaScript zpracování výjimek.

Bázi jazyka JavaScript uložené procedury, aktivační události, uživatelem definovanými funkcemi a sloučení postupy jsou zabaleny v rámci ambientní transakci ACID s izolací snímku přes všechny položky v rámci logického oddílu. V průběhu jeho spuštění Pokud program jazyka JavaScript vyvolá výjimku, celá transakce byla přerušena a vrácena zpět. Výsledný programovací model je jednoduchý výkonné. Vývojáře v JavaScriptu získejte odolné programovací model, zatímco stále pomocí jejich dobře známého jazyka vytvoří a knihovny primitiv.

Spouštění jazyka JavaScript přímo uvnitř databázového stroje zajišťuje výkon a transakční provádění databázových operací u položek kontejneru. Navíc vzhledem modul databáze Azure Cosmos nativně podporuje JSON a JavaScript, není žádný vzniklé vzájemné napětí Neshoda mezi systémy typ aplikace a databáze.

## <a name="optimistic-concurrency-control"></a>Optimistického řízení souběžnosti 

Optimistického řízení souběžnosti umožňuje zabránit ztrátou určitých aktualizací a odstraní. Souběžné, konfliktní operace podléhají regulární pesimistické zamykání databázového stroje hostuje logický oddíl, který vlastní položku. Při pokusu o aktualizaci nejnovější verzi položky v rámci logického oddílu, jeden z těchto dvou souběžných operací vyhraje a druhý se nezdaří. Nicméně pokud jedna nebo dvě operace pokus o aktualizaci souběžně jedné položce bylo dříve přečtené starší hodnota položky, databáze nebude vědět, pokud dříve čtení podle jedné nebo obou konfliktní operace byla zadána hodnota skutečně nejnovější hodnotu položky. Naštěstí tuto situaci lze zjistit pomocí optimistického řízení souběžnosti řízení přístupu (OCC) před umožníte tím dvě operace zadejte hranici transakce v databázovém stroji. OCC chrání vaše data před náhodnému přepsání změny provedené jinými uživateli. Je také zabrání ostatním náhodnému přepsání vlastní změny.

Souběžná aktualizace položky podléhají OCC vrstvou komunikace protokolu služby Azure Cosmos DB. Databáze Azure Cosmos se zajistí, že klientské verze položky, která jsou aktualizace (nebo odstranění) je stejná jako verze položky v kontejneru Azure Cosmos. Zaručí se tak, že zápisů jsou chráněny před přepsáním omylem podle zápisy ostatních a naopak. V prostředí s více uživateli optimistického řízení souběžnosti ovládacím prvku chrání proti náhodnému odstranění nebo aktualizaci chybná verze položky. V důsledku toho jsou položky chráněné proti nechvalně známý "ztracené aktualizace" nebo "ztráty delete" problémy.

Systém definované měla každá položka uložená v kontejneru Azure Cosmos `_etag` vlastnost. Hodnota `_etag` automaticky vygeneruje a aktualizovat server pokaždé, když je položka aktualizována. `_etag` je možné pomocí hlavičky žádosti if-match zadaná klientem umožňující server se rozhodnout, zda lze položky aktualizovat podmíněně. Hodnota hlavičky if-match odpovídá hodnotě `_etag` na serveru, se pak aktualizuje položku. Pokud už je aktuální hodnota hlavičky žádosti if-match, server zamítne operaci s "HTTP 412 selhání předběžné podmínky" zprávy s odpovědí. Klient pak může znovu načíst položky k získání aktuální verze položky na serveru nebo přepsání verze položky na serveru s vlastním `_etag` hodnotu pro položku. Kromě toho `_etag` je možné určit, jestli je potřeba znovu načíst prostředku s hlavičku if-none-match. 

Položky _etag hodnota změní pokaždé, když je položka aktualizována. Pro operace nahrazení položky musí být if-match explicitně vyjádřena jako součást možnosti žádosti. Příklad najdete v tématu ukázkový kód v [Githubu](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). `_etag` hodnoty jsou implicitně kontroluje všechny položky písemné přistupovala uloženou proceduru. Pokud se zjistí jakéhokoli konfliktu uloženou proceduru se navrátit transakci a vyvolá výjimku. Pomocí této metody se používají atomicky all nebo žádný zápisy v rámci uložené procedury. Toto je signál, který se aplikace znovu použít aktualizace a zkuste to znovu původní požadavek klienta.

## <a name="next-steps"></a>Další postup

Další informace o databázové transakce a optimistického řízení souběžnosti v následujících článcích:

- [Práce s databází Azure Cosmos, kontejnery a položek](databases-containers-items.md)
- [Úrovně konzistence](consistency-levels.md)
- [Zásady rozlišení a typy konfliktů](conflict-resolution-policies.md)
