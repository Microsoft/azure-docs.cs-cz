---
title: Vypršení platnosti dat v db Služby Azure Cosmos s časem na život
description: S TTL, Microsoft Azure Cosmos DB poskytuje možnost mít dokumenty automaticky vymazány ze systému po určité době.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188719"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Hodnota TTL (Time to Live) ve službě Azure Cosmos DB 

S **Time to Live** nebo TTL, Azure Cosmos DB poskytuje možnost odstranit položky automaticky z kontejneru po určitém časovém období. Ve výchozím nastavení můžete nastavit čas žít na úrovni kontejneru a přepsat hodnotu na základě pro položce. Po nastavení TTL na kontejneru nebo na úrovni položky Azure Cosmos DB automaticky odebere tyto položky po časovém období od doby, kdy byly naposledy změněny. Čas na živou hodnotu je konfigurován v sekundách. Při konfiguraci Protokolu TTL systém automaticky odstraní položky, jejichž platnost vypršela na základě hodnoty TTL, aniž by bylo nutné odstranit operaci, která je explicitně vydána klientskou aplikací.

Odstranění položek, jejichž platnost vypršela, je úloha na pozadí, která spotřebovává zbývající [jednotky požadavků](request-units.md), tedy jednotky požadavků, které nebyly spotřebovány požadavky uživatelů. I po vypršení platnosti TTL, pokud je kontejner přetížen požadavky a pokud není k dispozici dostatek RU, odstranění dat je zpožděno. Data jsou odstraněna, jakmile je k dispozici dostatek ru pro provedení operace odstranění. Přestože odstranění dat je zpožděno, data nejsou vrácena žádné dotazy (podle libovolného rozhraní API) po vypršení platnosti TTL.

## <a name="time-to-live-for-containers-and-items"></a>Čas žít pro kontejnery a předměty

Čas na live hodnota je nastavena v sekundách a je interpretován jako delta od okamžiku, kdy byla položka naposledy změněna. Můžete nastavit čas žít na kontejneru nebo položky v kontejneru:

1. **Čas žít na kontejneru** `DefaultTimeToLive`(sada pomocí ):

   - Pokud chybí (nebo je nastavena na hodnotu null), položky nejsou vypršela automaticky.

   - Pokud je k dispozici a hodnota je nastavena na "-1", je rovna nekonečno a položky nevyprší ve výchozím nastavení.

   - Pokud je k dispozici a hodnota je nastavena na nějaké číslo *"n"* – položky vyprší *"n"* sekund po jejich poslední změněný čas.

2. **Čas žít na položku** `ttl`(sada pomocí ):

   - Tato vlastnost je `DefaultTimeToLive` použitelná pouze v případě, že je k dispozici a není nastavena na hodnotu null pro nadřazený kontejner.

   - Pokud je k dispozici, přepíše hodnotu `DefaultTimeToLive` nadřazeného kontejneru.

## <a name="time-to-live-configurations"></a>Čas na živé konfigurace

* Pokud TTL je nastavena na *"n"* na kontejneru, pak položky v tomto kontejneru vyprší po *n* sekund.  Pokud jsou položky ve stejném kontejneru, které mají svůj vlastní čas žít, nastavte -1 (označující, že jejich platnost nevyprší) nebo pokud některé položky mají přepsána doba live nastavení s jiným číslem, tyto položky vyprší na základě jejich vlastní nakonfigurované hodnoty TTL. 

* Pokud TTL není nastavena na kontejneru, pak čas žít na položku v tomto kontejneru nemá žádný vliv. 

* Pokud TTL na kontejneru je nastavena na -1, položka v tomto kontejneru, který má čas žít nastavena na n, vyprší po n sekund a zbývající položky nevyprší.

## <a name="examples"></a>Příklady

Tato část ukazuje některé příklady s různými časovými hodnotami přiřazenými kontejneru a položkám:

### <a name="example-1"></a>Příklad 1

TTL na kontejneru je nastavena na hodnotu null (DefaultTimeToLive = null)

|TTL na položce| Výsledek|
|---|---|
|ttl = null|    TTL je zakázáno. Platnost položky nikdy nevyprší (výchozí).|
|ttl = -1   |TTL je zakázáno. Platnost položky nikdy nevyprší.|
|ttl = 2000 |TTL je zakázáno. Platnost položky nikdy nevyprší.|


### <a name="example-2"></a>Příklad 2

TTL na kontejneru je nastavena na -1 (DefaultTimeToLive = -1)

|TTL na položce| Výsledek|
|---|---|
|ttl = null |TTL je povolena. Platnost položky nikdy nevyprší (výchozí).|
|ttl = -1   |TTL je povolena. Platnost položky nikdy nevyprší.|
|ttl = 2000 |TTL je povolena. Platnost položky vyprší po 2000 sekundách.|


### <a name="example-3"></a>Příklad 3

TTL na kontejneru je nastavena na 1000 (DefaultTimeToLive = 1000)

|TTL na položce| Výsledek|
|---|---|
|ttl = null|    TTL je povolena. Platnost položky vyprší po 1000 sekundách (výchozí).|
|ttl = -1   |TTL je povolena. Platnost položky nikdy nevyprší.|
|ttl = 2000 |TTL je povolena. Platnost položky vyprší po 2000 sekundách.|

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nakonfigurovat čas žít v následujících článcích:

* [Jak nakonfigurovat čas žít](how-to-time-to-live.md)
