---
title: Databázové transakce a optimistické řízení souběžnosti v Azure Cosmos DB
description: Tento článek popisuje databázové transakce a optimistické řízení souběžnosti v Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: d453bb4071c4a6972e01b8f7e90375181caf6d01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806520"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Řízení optimistické souběžnosti a transakce

Databázové transakce poskytují bezpečný a předvídatelný programovací model pro zpracování souběžných změn dat. Tradiční relační databáze, jako je SQL Server, umožňují psát obchodní logiku pomocí uložených procedur a/nebo aktivačních událostí, odesílat je na server k provedení přímo v databázovém stroji. U tradičních relačních databází je nutné se zabývat dvěma různými programovacími jazyky (netransakčním) programovacím jazykem aplikace, jako je JavaScript, Python, C#, Java atd. a transakčním programovacím jazykem (například T-SQL), který je nativně spuštěn databází.

Databázový stroj v Azure Cosmos DB podporuje úplné ACID (atomicity, konzistence, izolace, trvanlivost) kompatibilní transakce s snímek izolace. Všechny databázové operace v rámci [logického oddílu](partition-data.md) kontejneru jsou transactionálně provedeny v rámci databázového stroje, který je hostován replikou oddílu. Tyto operace zahrnují jak zápis (aktualizace jedné nebo více položek v rámci logického oddílu), tak operace čtení. Následující tabulka znázorňuje různé operace a typy transakcí:

| **Operace**  | **Typ operace** | **Transakce s jednou nebo více položkami** |
|---------|---------|---------|
| Vložit (bez spouště před/zastím) | Zápis | Transakce s jednou položkou |
| Vložit (s aktivační událostí před/poštou) | Psaní a čtení | Transakce s více položkami |
| Nahradit (bez spouště před/post) | Zápis | Transakce s jednou položkou |
| Nahradit (s aktivační událostí před/poštou) | Psaní a čtení | Transakce s více položkami |
| Upsert (bez spouště před/poštou) | Zápis | Transakce s jednou položkou |
| Upsert (s aktivační událostí před/poštou) | Psaní a čtení | Transakce s více položkami |
| Odstranit (bez aktivační události před/po) | Zápis | Transakce s jednou položkou |
| Odstranit (s aktivační událostí před/příspěvkem) | Psaní a čtení | Transakce s více položkami |
| Spustit uloženou proceduru | Psaní a čtení | Transakce s více položkami |
| Spuštění hromadného postupu iniciovalo systém | Zápis | Transakce s více položkami |
| Spuštění odstraněných položek iniciované systémem na základě vypršení platnosti (TTL) položky | Zápis | Transakce s více položkami |
| Čtení | Čtení | Transakce s jednou položkou |
| Kanál změn | Čtení | Transakce s více položkami |
| Stránkované čtení | Čtení | Transakce s více položkami |
| Stránkovaný dotaz | Čtení | Transakce s více položkami |
| Spuštění udf jako součást stránkovaného dotazu | Čtení | Transakce s více položkami |

## <a name="multi-item-transactions"></a>Transakce s více položkami

Azure Cosmos DB umožňuje psát [uložené procedury, aktivační události před/po, uživatelem definované funkce (UOF)](stored-procedures-triggers-udfs.md) a slučovací procedury v Jazyce JavaScript. Azure Cosmos DB nativně podporuje spuštění JavaScriptu uvnitř svého databázového stroje. Můžete zaregistrovat uložené procedury, aktivační události před/zaúčtováním, uživatelem definované funkce (UDF) a sloučit procedury v kontejneru a později je provést v rámci databázového stroje Azure Cosmos. Psaní aplikační logiky v JavaScriptu umožňuje přirozený výraz toku řízení, oboru proměnných, přiřazení a integraci primitiv zpracování výjimek v rámci databázových transakcí přímo v jazyce JavaScript.

Uložené procedury založené na jazyce JavaScript, aktivační události, usp a slučovací procedury jsou zabaleny v rámci transakce okolí ACID s izolací snímek u všech položek v logickém oddílu. V průběhu jeho provádění, pokud program Jazyka JavaScript vyvolá výjimku, celá transakce je přerušena a vrácena zpět. Výsledný programovací model je jednoduchý, ale výkonný. Vývojáři JavaScriptu získají trvalý programovací model, zatímco stále používají své známé jazykové konstrukce a základní prvky knihovny.

Možnost spouštět JavaScript přímo v databázovém stroji poskytuje výkon a transakční provádění databázových operací s položkami kontejneru. Navíc vzhledem k tomu, že databázový stroj Azure Cosmos nativně podporuje JSON a JavaScript, neexistuje žádný nesoulad impedance mezi typové systémy aplikace a databáze.

## <a name="optimistic-concurrency-control"></a>Optimistické řízení souběžnosti

Optimistické řízení souběžnosti umožňuje zabránit ztrátám aktualizací a odstranění. Souběžné konfliktní operace jsou vystaveny pravidelné pesimistické uzamčení databázového stroje hostovanélogický oddíl, který vlastní položku. Pokud se dvě souběžné operace pokusí aktualizovat nejnovější verzi položky v rámci logického oddílu, jeden z nich vyhraje a druhý se nezdaří. Pokud však jedna nebo dvě operace, které se pokoušejí souběžně aktualizovat stejnou položku, dříve četly starší hodnotu položky, databáze neví, zda dříve přečtená hodnota jedné nebo obou konfliktních operací byla skutečně nejnovější hodnotou položky. Naštěstí tato situace může být **zjištěna s optimistické souběžnosti řízení (OCC)** před zahájením dvě operace zadat hranice transakce uvnitř databázového stroje. OCC chrání vaše data před náhodným přepsáním změn, které byly provedeny jinými uživateli. Zabraňuje také ostatním v náhodném přepsání vlastních změn.

Souběžné aktualizace položky jsou podrobeny occ vrstvou komunikačního protokolu Azure Cosmos DB. Databáze Azure Cosmos zajišťuje, že verze položky na straně klienta, kterou aktualizujete (nebo odstranění) je stejná jako verze položky v kontejneru Azure Cosmos. To zaručuje, že vaše zápisy jsou chráněny před přepsána náhodně zápisy ostatních a naopak. V prostředí pro více uživatelů optimistický ovládací prvek souběžnosti chrání před náhodným odstraněním nebo aktualizací nesprávné verze položky. Jako takové jsou položky chráněny před nechvalně známými problémy "ztracené aktualizace" nebo "lost delete".

Každá položka uložená v kontejneru Azure `_etag` Cosmos má vlastnost definovanou systémem. Hodnota `_etag` je automaticky generována a aktualizována serverem při každé aktualizaci položky. `_etag`lze použít s hlavičkou požadavku zadané `if-match` klientem, aby server mohl rozhodnout, zda lze položku podmíněně aktualizovat. Hodnota `if-match` záhlaví odpovídá hodnotě `_etag` na serveru, položka je pak aktualizována. Pokud hodnota hlavičky `if-match` požadavku již není aktuální, server odmítne operaci se zprávou odpovědi http 412 precondition failure. Klient pak může znovu načíst položku získat aktuální verzi položky na serveru nebo přepsat verzi `_etag` položky na serveru s vlastní hodnotou pro položku. Kromě toho `_etag` lze použít `if-none-match` s hlavičkou k určení, zda je potřeba znovu načíst prostředek.

Hodnota položky `_etag` se změní při každé aktualizaci položky. Pro operace nahrazení `if-match` položky musí být explicitně vyjádřena jako součást možností požadavku. Příklad najdete v ukázkovém kódu na [GitHubu](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L578-L674). `_etag`hodnoty jsou implicitně kontrolovány pro všechny písemné položky, kterých se uložená procedura dotýká. Pokud je zjištěn konflikt, uložená procedura vrátí transakci zpět a vyvolá výjimku. S touto metodou jsou atomově použity všechny nebo žádné zápisy v rámci uložené procedury. Toto je signál pro aplikaci znovu použít aktualizace a opakovat původní požadavek klienta.

## <a name="next-steps"></a>Další kroky

Další informace o databázových transakcích a optimistickém řízení souběžnosti naleznete v následujících článcích:

- [Práce s databázemi, kontejnery a položkami Azure Cosmos](databases-containers-items.md)
- [Úrovně konzistence](consistency-levels.md)
- [Zásady řešení a typy konfliktů](conflict-resolution-policies.md)
- [Uložené procedury, aktivační události a uživatelem definované funkce](stored-procedures-triggers-udfs.md)
