---
title: Vypršení platnosti dat v Azure Cosmos DB s časem až Live
description: Pomocí TTL Microsoft Azure Cosmos DB poskytuje možnost mít po určitou dobu automaticky vyčištěné dokumenty ze systému.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: cf9d0aea9ab9e79a5f184a42e1bb785b6fb870a7
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360084"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Hodnota TTL (Time to Live) ve službě Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

S hodnotou **Time to Live** nebo TTL Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určitém časovém období. Ve výchozím nastavení můžete nastavit možnost čas na živý na úrovni kontejneru a přepsat hodnotu na základě jednotlivých položek. Jakmile nastavíte hodnotu TTL v kontejneru nebo na úrovni položky, Azure Cosmos DB tyto položky po uplynutí časového období automaticky odebrat od doby, kdy byla naposledy změněna. Hodnota TTL (Time to Live) se nakonfiguruje během několika sekund. Když nastavíte hodnotu TTL, systém automaticky odstraní položky s vypršenou platností na základě hodnoty TTL bez nutnosti operace odstranění, která je explicitně vystavena klientskou aplikací. Maximální hodnota TTL je 2147483647.

Odstraněním položek, jejichž platnost vypršela, je úloha na pozadí, která využívá [jednotky žádostí o](request-units.md)převzetí služeb při selhání. Jedná se o jednotky požadavků, které nevyužily požadavky uživatelů. I po vypršení platnosti TTL dojde v případě přetížení kontejneru v případě požadavků a v případě, že není k dispozici dostatek RU, odstranění dat je zpožděno. Data se odstraní, jakmile je k dispozici dostatek ru k provedení operace odstranění. I když je odstranění dat zpožděno, data se po vypršení hodnoty TTL nevrátí žádným dotazům (pomocí rozhraní API).

> Tento obsah se týká Azure Cosmos DB hodnoty TTL transakčního úložiště. Pokud hledáte hodnotu TTL analytického úložiště, která umožňuje scénáře NoETL HTAP prostřednictvím [odkazu Azure synapse](./synapse-link.md), klikněte [sem](./analytical-store-introduction.md#analytical-ttl).

## <a name="time-to-live-for-containers-and-items"></a>Doba provozu pro kontejnery a položky

Hodnota TTL (Time to Live) je nastavena v sekundách a je interpretována jako rozdíl od doby poslední změny položky. Můžete nastavit hodnotu TTL (Time to Live) pro kontejner nebo položku v rámci kontejneru:

1. **Doba do provozu v kontejneru** (nastavení pomocí `DefaultTimeToLive` ):

   - Pokud chybí (nebo je nastavené na hodnotu null), položky se automaticky nevyprší.

   - Je-li k dispozici a hodnota nastavená na "-1", bude rovna hodnotě nekonečno a položky ve výchozím nastavení nevyprší.

   - Je-li k dispozici a hodnota je nastavena na *nenulové* číslo *"n"* – položky vyprší za *"n"* sekund po datu poslední změny.

2. **Doba, po kterou se má položka** (nastavit pomocí) na živé `ttl` :

   - Tato vlastnost je platná pouze v případě, že `DefaultTimeToLive` je k dispozici a není pro nadřazený kontejner nastavena na hodnotu null.

   - Je-li k dispozici, Přepisuje `DefaultTimeToLive` hodnotu nadřazeného kontejneru.

## <a name="time-to-live-configurations"></a>Doba do živých konfigurací

- Pokud je hodnota TTL nastavena na *"n"* na kontejneru, pak položky v tomto kontejneru vyprší po *n* sekundách.  Pokud jsou ve stejném kontejneru nějaké položky, které mají svůj vlastní čas v reálném čase, nastavte na hodnotu-1 (což znamená, že nevyprší platnost), nebo pokud některé položky přepsaly nastavení čas na živé s jiným číslem, vyprší platnost těchto položek na základě vlastní nakonfigurované hodnoty TTL.

- Pokud hodnota TTL není nastavena u kontejneru, nemá čas na živé položky v tomto kontejneru žádný vliv.

- Pokud je hodnota TTL u kontejneru nastavená na hodnotu-1, vyprší platnost položky v tomto kontejneru, která má hodnotu TTL nastavenou na n. po dobu n sekund vyprší platnost a zbývající položky nebudou vypršet.

## <a name="examples"></a>Příklady

V této části jsou uvedeny některé příklady s různými časy pro živé hodnoty přiřazené k kontejneru a položkám:

### <a name="example-1"></a>Příklad 1

Hodnota TTL u kontejneru je nastavená na hodnotu null (DefaultTimeToLive = null).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet (výchozí nastavení).|
|TTL =-1|Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet.|
|TTL = 2000|Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet.|

### <a name="example-2"></a>Příklad 2

Hodnota TTL u kontejneru je nastavená na-1 (DefaultTimeToLive =-1).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet (výchozí nastavení).|
|TTL =-1|Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet.|
|TTL = 2000|Hodnota TTL je povolena. Platnost položky vyprší po 2000 sekundách.|

### <a name="example-3"></a>Příklad 3

Hodnota TTL u kontejneru je nastavená na 1000 (DefaultTimeToLive = 1000).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|Hodnota TTL je povolena. Platnost položky vyprší po 1000 sekundách (výchozí nastavení).|
|TTL =-1|Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet.|
|TTL = 2000|Hodnota TTL je povolena. Platnost položky vyprší po 2000 sekundách.|

## <a name="next-steps"></a>Další kroky

Naučte se konfigurovat dobu v provozu v následujících článcích:

- [Jak nakonfigurovat dobu provozu](how-to-time-to-live.md)