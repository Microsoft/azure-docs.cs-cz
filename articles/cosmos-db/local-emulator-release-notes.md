---
title: Azure Cosmos Emulátor stáhnout a poznámky k verzi
description: Získejte poznámky k verzi emulátoru Azure Cosmos pro různé verze a informace o stahování.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77168646"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Emulátor Azure Cosmos – poznámky k verzi a informace o stahování

Tento článek ukazuje poznámky k verzi emulátoru Azure Cosmos se seznamem aktualizací funkcí, které byly provedeny v každé verzi. Obsahuje také nejnovější verzi emulátoru ke stažení a použití.

## <a name="download"></a>Stáhnout

| | |
|---------|---------|
|**MSI ke stažení**|[Stažení softwaru společnosti Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Začínáme**|[Vývoj místně pomocí emulátoru Azure Cosmos](local-emulator.md)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="291"></a>2.9.1

- Tato verze opravuje několik problémů v podpoře rozhraní API dotazu a obnovuje kompatibilitu se staršími os, jako je například Windows Server 2012.

### <a name="290"></a>2.9.0

- Tato verze přidá možnost nastavit konzistenci na konzistentní předponu a zvýšit maximální limity pro uživatele a oprávnění.

### <a name="272"></a>2.7.2

- Tato verze přidává podporu serveru MongoDB verze 3.6 do emulátoru Cosmos. Chcete-li spustit koncový bod MongoDB, který cílí na verzi 3.6 služby, spusťte emulátor z příkazového řádku správce s možností "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Tato verze opravuje regresi, která uživatelům bránila ve spouštění dotazů proti účtu rozhraní SQL API z emulátoru při použití klientů založených na rozhraní .NET nebo x86 .NET.

### <a name="246"></a>2.4.6

- Tato verze poskytuje paritu s funkcemi služby Azure Cosmos od července 2019, s výjimkami známými v [develop místně s emulátorem Azure Cosmos](local-emulator.md). Opravuje také několik chyb souvisejících s vypnutím emulátoru při vyvolání pomocí příkazového řádku a přepsání interní chod IP adres pro klienty sady SDK pomocí připojení v přímém režimu.

### <a name="243"></a>2.4.3

- Zakázáno spuštění služby MongoDB ve výchozím nastavení. Jako výchozí je povolen pouze koncový bod SQL. Uživatel musí spustit koncový bod ručně pomocí možnosti příkazového řádku emulátoru "/EnableMongoDbEndpoint". Nyní je to jako všechny ostatní koncové body služby, jako je Gremlin, Cassandra a table.
- Opravena chyba v emulátoru při spuštění s "/AllowNetworkAccess", kde koncové body Gremlin, Cassandra a Tabulka nebyly správně zpracování požadavků z externích klientů.
- Přidejte porty přímého připojení do nastavení pravidel brány firewall.

### <a name="240"></a>2.4.0

- Byl opraven problém s nedaří spustit emulátor, když jsou v hostitelském počítači přítomny aplikace pro sledování sítě, například Pulse Client.
