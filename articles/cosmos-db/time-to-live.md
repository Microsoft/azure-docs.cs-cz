---
title: Vypršení platnosti dat v Azure Cosmos DB s časem až Live
description: Pomocí TTL Microsoft Azure Cosmos DB poskytuje možnost mít po určitou dobu automaticky vyčištěné dokumenty ze systému.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 7a29e9446a8c3b703c2ec3140711f44f3c81535f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467587"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Hodnota TTL (Time to Live) v Azure Cosmos DB 

S hodnotou **Time to Live** nebo TTL Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určitém časovém období. Ve výchozím nastavení můžete nastavit možnost čas na živý na úrovni kontejneru a přepsat hodnotu na základě jednotlivých položek. Jakmile nastavíte hodnotu TTL v kontejneru nebo na úrovni položky, Azure Cosmos DB tyto položky po uplynutí časového období automaticky odebrat od doby, kdy byla naposledy změněna. Hodnota TTL (Time to Live) se nakonfiguruje během několika sekund. Když nastavíte hodnotu TTL, systém automaticky odstraní položky s vypršenou platností na základě hodnoty TTL bez nutnosti operace odstranění, která je explicitně vystavena klientskou aplikací.

## <a name="time-to-live-for-containers-and-items"></a>Doba provozu pro kontejnery a položky

Hodnota TTL (Time to Live) je nastavena v sekundách a je interpretována jako rozdíl od doby poslední změny položky. Můžete nastavit hodnotu TTL (Time to Live) pro kontejner nebo položku v rámci kontejneru:

1. **Doba do provozu v kontejneru** (nastavit pomocí `DefaultTimeToLive`):

   - Pokud chybí (nebo je nastavené na hodnotu null), položky se automaticky nevyprší.

   - Je-li k dispozici a hodnota nastavená na "-1", bude rovna hodnotě nekonečno a položky ve výchozím nastavení nevyprší.

   - Pokud je tato hodnota nastavená na nějaké číslo *"n"* , vyprší *"n"* sekund po datu poslední změny.

2. **Čas do živého pro položku** (nastavit pomocí `ttl`):

   - Tato vlastnost je platná pouze v `DefaultTimeToLive` případě, že je k dispozici a není pro nadřazený kontejner nastavena na hodnotu null.

   - Je-li k dispozici `DefaultTimeToLive` , Přepisuje hodnotu nadřazeného kontejneru.

## <a name="time-to-live-configurations"></a>Doba do živých konfigurací

* Pokud je hodnota TTL nastavena na *"n"* na kontejneru, pak položky v tomto kontejneru vyprší po *n* sekundách.  Pokud jsou ve stejném kontejneru nějaké položky, které mají svůj vlastní čas v reálném čase, nastavte na hodnotu-1 (což znamená, že nevyprší platnost), nebo pokud některé položky přepsaly nastavení čas na živé s jiným číslem, vyprší platnost těchto položek na základě vlastní nakonfigurované hodnoty TTL. 

* Pokud hodnota TTL není nastavena u kontejneru, nemá čas na živé položky v tomto kontejneru žádný vliv. 

* Pokud je hodnota TTL u kontejneru nastavená na hodnotu-1, vyprší platnost položky v tomto kontejneru, která má hodnotu TTL nastavenou na n. po dobu n sekund vyprší platnost a zbývající položky nebudou vypršet. 

Odstraňování položek na základě hodnoty TTL je zdarma. Neúčtují se žádné další náklady (to znamená, že se nespotřebovává žádné další ru), když se položka odstraní v důsledku vypršení hodnoty TTL.

## <a name="examples"></a>Příklady

V této části jsou uvedeny některé příklady s různými časy pro živé hodnoty přiřazené k kontejneru a položkám:

### <a name="example-1"></a>Příklad 1

Hodnota TTL u kontejneru je nastavená na hodnotu null (DefaultTimeToLive = null).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|    Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet (výchozí nastavení).|
|TTL =-1   |Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet.|
|TTL = 2000 |Hodnota TTL je zakázána. Platnost položky nebude nikdy vypršet.|


### <a name="example-2"></a>Příklad 2

Hodnota TTL u kontejneru je nastavená na-1 (DefaultTimeToLive =-1).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null |Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet (výchozí nastavení).|
|TTL =-1   |Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet.|
|TTL = 2000 |Hodnota TTL je povolena. Platnost položky vyprší po 2000 sekundách.|


### <a name="example-3"></a>Příklad 3

Hodnota TTL u kontejneru je nastavená na 1000 (DefaultTimeToLive = 1000).

|Hodnota TTL u položky| Výsledek|
|---|---|
|TTL = null|    Hodnota TTL je povolena. Platnost položky vyprší po 1000 sekundách (výchozí nastavení).|
|TTL =-1   |Hodnota TTL je povolena. Platnost položky nebude nikdy vypršet.|
|TTL = 2000 |Hodnota TTL je povolena. Platnost položky vyprší po 2000 sekundách.|

## <a name="next-steps"></a>Další kroky

Naučte se konfigurovat dobu v provozu v následujících článcích:

* [Jak nakonfigurovat dobu provozu](how-to-time-to-live.md)
