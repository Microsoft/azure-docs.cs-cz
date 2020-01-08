---
title: Stažení a poznámky k verzi emulátoru Azure Cosmos
description: Získejte poznámky k verzi emulátoru Azure Cosmos pro různé verze a informace ke stažení.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: e14ee617aca20fc18226651acfadab6ab2bdd939
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445292"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulátor Azure Cosmos – poznámky k verzi a informace ke stažení

Tento článek obsahuje poznámky k verzi emulátoru Azure Cosmos se seznamem aktualizací funkcí, které byly provedeny v jednotlivých verzích. Obsahuje také nejnovější verzi emulátoru, která se má stáhnout a použít.

## <a name="download"></a>Stáhnout

| | |
|---------|---------|
|**Stažení MSI**|[Stažení softwaru společnosti Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Začínáme**|[Místní vývoj pomocí emulátoru Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="272"></a>2.7.2

- Tato verze přidá podporu serveru MongoDB verze 3,6 do emulátoru Cosmos. Pokud chcete spustit koncový bod MongoDB, který cílí na verzi 3,6 služby, spusťte emulátor z příkazového řádku správce s možností/EnableMongoDBEndpoint = 3.6.

### <a name="270"></a>2.7.0

- Tato verze opravuje regresi, která brání uživatelům ve spouštění dotazů na účet rozhraní SQL API z emulátoru při použití klientů využívajících rozhraní .NET Core nebo x86 .NET.

### <a name="246"></a>2.4.6

- Tato verze poskytuje paritu s funkcemi ve službě Azure Cosmos od července 2019 s výjimkami popsanými v části [vývoj místně pomocí emulátoru Azure Cosmos](local-emulator.md). Opravuje také několik chyb souvisejících s vypnutím emulátoru při vyvolání prostřednictvím příkazového řádku a přepsání interních IP adres pro klienty SDK pomocí přímého připojení k režimu.

### <a name="243"></a>2.4.3

- Ve výchozím nastavení zakázáno spouštění služby MongoDB. Jako výchozí je povolený jenom koncový bod SQL. Uživatel musí spustit koncový bod ručně pomocí možnosti příkazového řádku emulátoru "/EnableMongoDbEndpoint". Teď to vypadá stejně jako všechny ostatní koncové body služby, například Gremlin, Cassandra a Table.
- Opravili jsme chybu v emulátoru, když začínáte na "/AllowNetworkAccess", kde koncové body Gremlin, Cassandra a Table nesprávně zpracovávají požadavky od externích klientů.
- Přidejte přímé spojovací porty do nastavení pravidel brány firewall.

### <a name="240"></a>2.4.0

- Opravili jsme problém s tím, že se nepodaří spustit emulátor, když se v hostitelském počítači nacházejí aplikace pro monitorování sítě, třeba Pulse Client.
