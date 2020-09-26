---
title: Transakce databáze a kontrola optimistického řízení souběžnosti v Azure Cosmos DB
description: Tento článek popisuje transakce databáze a kontrolu optimistického řízení souběžnosti v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 9d8bd72b6a03164a41e0b7c0ff00ac728cecf7f5
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91355377"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Řízení optimistické souběžnosti a transakce

Transakce databáze poskytují bezpečný a předvídatelný programovací model, který umožňuje zabývat se souběžnými změnami dat. Tradiční relační databáze, jako je SQL Server, umožňují napsat obchodní logiku pomocí uložených procedur a/nebo triggerů a odeslat je na server, aby je bylo možné spouštět přímo v databázovém stroji. V případě tradičních relačních databází je nutné se zabývat dvěma různými programovacími jazyky (netransakční) programovací jazyk aplikací (non-transactioned), jako je JavaScript, Python, C#, Java atd., a transakční programovací jazyk (například T-SQL), který je nativně spuštěn databází.

Databázový stroj v Azure Cosmos DB podporuje úplnou transakci (kyselost, konzistenci, izolaci, odolnost) vyhovující požadavkům na izolaci snímků. Všechny databázové operace v oboru [logického oddílu](partition-data.md) kontejneru jsou v rámci databázového stroje, který je hostitelem repliky oddílu, revykonávány. Tyto operace zahrnují zápis (aktualizace jedné nebo více položek v rámci logického oddílu) a operace čtení. Následující tabulka ilustruje různé operace a typy transakcí:

| **Operace**  | **Typ operace** | **Transakce jedné nebo více položek** |
|---------|---------|---------|
| Vložit (bez aktivační události před/po) | Zápis | Transakce jedné položky |
| Vložit (s aktivační událostí před/po) | Zápis a čtení | Transakce s více položkami |
| Nahradit (bez aktivační události před/po) | Zápis | Transakce jedné položky |
| Nahradit (s aktivační událostí před/po) | Zápis a čtení | Transakce s více položkami |
| Upsert (bez aktivační události před/po) | Zápis | Transakce jedné položky |
| Upsert (s aktivační událostí před/po) | Zápis a čtení | Transakce s více položkami |
| Odstranit (bez aktivační události před/po) | Zápis | Transakce jedné položky |
| Odstranit (s aktivační událostí před/po) | Zápis a čtení | Transakce s více položkami |
| Spustit uloženou proceduru | Zápis a čtení | Transakce s více položkami |
| Systém inicioval provádění procesu sloučení | Zápis | Transakce s více položkami |
| Systém inicioval provádění odstraňování položek na základě vypršení platnosti položky (TTL) | Zápis | Transakce s více položkami |
| Čtení | Čtení | Transakce s jednou položkou |
| Kanál změn | Číst | Transakce s více položkami |
| Stránkované čtení | Číst | Transakce s více položkami |
| Stránkované dotaz | Číst | Transakce s více položkami |
| Spustit systém souborů UDF jako součást stránkovaného dotazu | Číst | Transakce s více položkami |

## <a name="multi-item-transactions"></a>Transakce s více položkami

Azure Cosmos DB umožňuje psát [uložené procedury, aktivační události před/po, uživatelsky definované funkce (UDF)](stored-procedures-triggers-udfs.md) a slučovací procedury v JavaScriptu. Azure Cosmos DB nativně podporují spouštění JavaScriptu uvnitř svého databázového stroje. Uložené procedury, aktivační události před/po, uživatelsky definované funkce (UDF) a slučovací procedury můžete registrovat v kontejneru a později je provádět v rámci databázového stroje Azure Cosmos. Zápis aplikační logiky v JavaScriptu umožňuje přirozený výraz toku řízení, určení proměnné, přiřazení a integraci primitivních operací zpracování výjimek v rámci transakcí databáze přímo v jazyce JavaScript.

Uložené procedury, triggery, UDF a slučovací procedury založené na jazyce JavaScript jsou zabaleny do transakce s KYSELINou v okolí s izolací snímku napříč všemi položkami v rámci logického oddílu. V průběhu provádění, pokud program JavaScript vyvolá výjimku, je celá transakce přerušena a vrácena zpět. Výsledný programovací model je jednoduchý, ale výkonný. Vývojáři JavaScriptu získají trvalý programovací model a stále používají své známé jazykové konstrukce a primitivní prvky knihovny.

Schopnost spustit JavaScript přímo v rámci databázového stroje poskytuje výkon a transakční provádění operací databáze proti položkám kontejneru. Vzhledem k tomu, že Azure Cosmos Database Engine nativně podporuje JSON a JavaScript, nedochází k neshodě mezi systémy typů aplikace a databáze.

## <a name="optimistic-concurrency-control"></a>Řízení optimistické souběžnosti

Optimistické řízení souběžnosti umožňuje zabránit ztrátě aktualizací a odstraňování. Souběžné a konfliktní operace se vztahují na běžné pesimistické zamykání databázového stroje hostovaného logickým oddílem, který tuto položku vlastní. Když se dvě souběžné operace pokusí aktualizovat nejnovější verzi položky v rámci logického oddílu, jedna z nich se podaří a druhá se nezdaří. Pokud však jedna nebo dvě operace, které se pokoušejí současně aktualizovat stejnou položku, dříve přečetly starší hodnotu položky, databáze neví, zda byla dříve přečtena buď konfliktní operace, nebo jak v obou konfliktních operacích byla skutečně aktuální hodnota položky. Naštěstí tuto situaci lze zjistit pomocí **optimistického řízení souběžnosti (OCC)** předtím, než umožníte dvěma operacím zadat hranici transakce uvnitř databázového stroje. OCC chrání vaše data před náhodným přepsáním změn provedených ostatními. Zabrání taky ostatním v neúmyslném přepsání vašich změn.

Souběžné aktualizace položky podléhají OCC vrstvě komunikačního protokolu Azure Cosmos DB. Azure Cosmos Database zajišťuje, že verze položky na straně klienta, kterou aktualizujete (nebo odstraňujete), je stejná jako verze položky v kontejneru Azure Cosmos. To zaručuje, že vaše zápisy jsou před náhodným zápisem přepsány zápisy ostatních a naopak. V prostředí s více uživateli vám optimistické řízení souběžnosti chrání před náhodným odstraněním nebo aktualizací nesprávné verze položky. V takovém případě jsou položky chráněny proti problémům s inFamous "ztráty aktualizace" nebo "ztráty odstranění".

Každá položka uložená v kontejneru Azure Cosmos má vlastnost definovanou systémem `_etag` . Hodnota `_etag` je automaticky generována a aktualizována serverem při každém aktualizaci položky. `_etag` dá se použít spolu s `if-match` hlavičkou žádosti, která je součástí klienta, aby mohl server rozhodnout, jestli může být položka podmíněně aktualizována. Hodnota `if-match` hlavičky odpovídá hodnotě na `_etag` serveru, položka se pak aktualizuje. Pokud hodnota `if-match` hlavičky požadavku již není aktuální, server odmítne operaci se zprávou odpovědi "selhání předběžné podmínky HTTP 412". Klient pak může znovu načíst položku k získání aktuální verze položky na serveru nebo přepsat verzi položky na serveru vlastní `_etag` hodnotou položky. Kromě toho `_etag` lze použít s `if-none-match` hlavičkou k určení, zda je nutné znovu načíst prostředek.

Hodnota položky se `_etag` změní pokaždé, když je položka aktualizována. V případě operací nahradit položku `if-match` musí být explicitně vyjádřena jako součást možností žádosti. Příklad najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772). `_etag` hodnoty jsou implicitně kontrolovány u všech zapsaných položek, které jsou v rámci uložené procedury změněny. Pokud je zjištěn nějaký konflikt, uložená procedura vrátí transakci zpět a vyvolá výjimku. Pomocí této metody se v rámci uložené procedury nepoužívají buď všechny, nebo žádné zápisy. Toto je signál k aplikaci pro opětovné použití aktualizací a původní požadavek klienta.

## <a name="next-steps"></a>Další kroky

Další informace o transakcích databáze a kontrole optimistického řízení souběžnosti najdete v následujících článcích:

- [Práce s databázemi, kontejnery a položkami Azure Cosmos](databases-containers-items.md)
- [Úrovně konzistence](consistency-levels.md)
- [Zásady řešení a typy konfliktů](conflict-resolution-policies.md)
- [Uložené procedury, triggery a uživatelsky definované funkce](stored-procedures-triggers-udfs.md)
