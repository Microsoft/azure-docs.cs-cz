---
title: Stažení a poznámky k verzi emulátoru Azure Cosmos
description: Přečtěte si poznámky k verzi emulátoru Azure Cosmos a Stáhněte si je.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 587c730dfa436760d42e614c2dabee117f3b61d3
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018821"
---
# <a name="use-the-azure-cosmos-emulator-for-local-development-and-testing"></a>Použití emulátoru Azure Cosmos pro místní vývoj a testování

Tento článek obsahuje poznámky k verzi emulátoru Azure Cosmos se seznamem aktualizací funkcí, které byly provedeny v jednotlivých verzích. Obsahuje také nejnovější verzi emulátoru, která se má stáhnout a použít.

## <a name="download"></a>Ke stažení

| | |
|---------|---------|
|**Stažení MSI**|[Stažení softwaru společnosti Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Začínáme**|[Místní vývoj pomocí emulátoru Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="246"></a>2.4.6

- Tato verze poskytuje paritu s funkcemi ve službě Azure Cosmos od července 2019 s výjimkami popsanými v části [vývoj místně pomocí emulátoru Azure Cosmos](local-emulator.md). Opravuje také několik chyb souvisejících s vypnutím emulátoru při vyvolání prostřednictvím příkazového řádku a přepsání interních IP adres pro klienty SDK pomocí přímého připojení k režimu.

### <a name="243"></a>2.4.3

- Ve výchozím nastavení zakázáno spouštění služby MongoDB. Jako výchozí je povolený jenom koncový bod SQL. Uživatel musí spustit koncový bod ručně pomocí možnosti příkazového řádku emulátoru "/EnableMongoDbEndpoint". Teď to vypadá stejně jako všechny ostatní koncové body služby, například Gremlin, Cassandra a Table.
- Opravili jsme chybu v emulátoru, když začínáte na "/AllowNetworkAccess", kde koncové body Gremlin, Cassandra a Table nesprávně zpracovávají požadavky od externích klientů.
- Přidejte přímé spojovací porty do nastavení pravidel brány firewall.

### <a name="240"></a>2.4.0

- Opravili jsme problém s tím, že se nepodaří spustit emulátor, když se v hostitelském počítači nacházejí aplikace pro monitorování sítě, třeba Pulse Client.
