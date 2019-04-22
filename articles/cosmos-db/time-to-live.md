---
title: Vypršení platnosti dat v Azure Cosmos DB s využitím TTL
description: Díky TTL Microsoft Azure Cosmos DB poskytuje možnost používat dokumenty z systém automaticky vymazány po určitou dobu.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 27540c3dfce73788e01f0f8ab0892c733f153fdf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271267"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Hodnota Time to Live (TTL) ve službě Azure Cosmos DB 

S **TTL** nebo TTL, Azure Cosmos DB poskytuje možnost automaticky odstraňovat položky z kontejneru po určité časové období. Ve výchozím nastavení můžete nastavit dobu za provozu na úrovni kontejneru a přepište hodnotu na základě za položku. Po nastavení interval TTL, ZÍSKÁ v kontejneru nebo na úrovni položek služby Azure Cosmos DB tyto položky automaticky odebrat po období od chvíle, kdy bylo naposledy změněno. Hodnotu Time to live je nakonfigurovaný v řádu sekund. Při konfiguraci hodnoty TTL se systém automaticky odstraní vypršela platnost položky založené na hodnotu TTL, bez nutnosti explicitně vydaného klientskou operaci odstranění.

## <a name="time-to-live-for-containers-and-items"></a>Hodnota Time to live pro kontejnery a položek

Během několika sekund je nastavena hodnotu time to live a je interpretován jako rozdílový od doby poslední změny položky. Můžete nastavit TTL na kontejner nebo položku v rámci kontejneru:

1. **TTL na kontejner** (sady s použitím `DefaultTimeToLive`):

   - Pokud chybí (nebo nastavený na hodnotu null), nejsou automaticky platnost položky.

   - Pokud je k dispozici a hodnota je nastavena na "-1", se rovná hodnotě nekonečno a není ve výchozím nastavení vypršení platnosti položek.

   - Pokud je k dispozici a hodnota je nastavena na nějaké číslo *"n"* – vyprší platnost položky *"n"* sekund po čas jejich poslední změny.

2. **Čas TTL na položku** (sady s použitím `ttl`):

   - Tato vlastnost se vztahuje pouze tehdy, pokud `DefaultTimeToLive` je k dispozici a není nastaven na hodnotu null pro nadřazený kontejner.

   - Pokud existuje, přepíše `DefaultTimeToLive` hodnota jeho nadřazeného kontejneru.

## <a name="time-to-live-configurations"></a>Čas na živé konfigurace

* Pokud hodnota TTL je nastavená na *"n"* na kontejner, klikněte položky v tomto kontejneru platnost vyprší po *n* sekund.  Pokud je položek ve stejném kontejneru, které mají své vlastní čas live, nastavte na hodnotu -1 (což znamená, že nevyprší platnost), nebo pokud některé položky mají přepsat time to live nastavení s jiným číslem, platnost těchto položek podle jejich vlastní nakonfigurovanou hodnotu TTL. 

* Pokud hodnota TTL není nastavena na kontejner, time to live na položky v tomto kontejneru nemá žádný vliv. 

* Pokud hodnota TTL kontejneru je nastavena na hodnotu -1, položky v tomto kontejneru, který má time to live nastavenou na n, platnost vyprší po n sekund a zbývající položky nevyprší. 

Odstranění položek podle hodnoty TTL je zdarma. Se neúčtují žádné další poplatky (to znamená, že se spotřebuje žádné další RUs) při odstranění položky v důsledku vypršení hodnoty TTL.

## <a name="next-steps"></a>Další postup

Další informace o konfiguraci TTL v následujících článcích:

* [Jak nakonfigurovat TTL](how-to-time-to-live.md)
