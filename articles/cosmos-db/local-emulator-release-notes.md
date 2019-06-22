---
title: Azure Cosmos emulátor stahování a poznámky k verzi
description: Přečtěte si poznámky k verzi emulátor služby Azure Cosmos a stáhněte ho.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 5985d0d82341c76993ee91b8dff6927edd1ed8b4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332126"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Pro místní vývoj a testování používat emulátor služby Azure Cosmos

Tento článek ukazuje poznámky emulátor služby Azure Cosmos se seznamem aktualizací funkcí, které byly provedeny v jednotlivých verzích. Jsou zde uvedeny také nejnovější verze emulátoru stáhnout a použít.

## <a name="download"></a>Ke stažení

| | |
|---------|---------|
|**Stáhnout Instalační služby MSI**|[Stažení softwaru společnosti Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Začínáme**|[Místní vývoj s využitím emulátoru služby Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="243"></a>2.4.3

- Zakázané ve výchozím nastavení spouští službu MongoDB. Pouze SQL koncový bod je povolený jako výchozí. Uživatel musí spustit ručně pomocí emulátoru na koncový bod "/ EnableMongoDbEndpoint" možnost příkazového řádku. Nyní je stejně jako všechny ostatní koncové body služby, jako je například Gremlin, Cassandra a tabulky.
- Oprava chyby v emulátoru při spuštění pomocí "/ AllowNetworkAccess" kde koncové body Gremlin, Cassandra a tabulku nebyly zpracování správně žádosti od externích klientů.
- Přidání portů přímé připojení k nastavení pravidla brány Firewall.

### <a name="240"></a>2.4.0

- Opravili jsme problém s emulátorem selhání spuštění při monitorování sítě aplikací, jako je klient Pulse, jsou k dispozici na hostitelském počítači.
