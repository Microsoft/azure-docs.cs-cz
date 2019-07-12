---
title: Více hlavních výhod služby Azure Cosmos DB
description: Pochopíte výhody sady více hlavních databází ve službě Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789290"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Vysvětlení multimasterovou výhody ve službě Azure Cosmos DB

Operátory účtu cosmos DB by měl vybrat konfiguraci příslušné globální distribuce latence, dostupnosti a RTO požadavky pro své aplikace. Účty Azure Cosmos, nakonfigurovaný s využitím více umístění zápisu nabízejí významné výhody přes účty s včetně umístění jeden zápis, zápis 99,999 % dostupnosti smlouvy SLA, < 10 ms latence smlouvu SLA na úrovni 99. percentilu a RTO zápisu = 0 v regionálního.

## <a name="comparison-of-features"></a>Porovnání funkcí

|Požadavek na aplikaci|Více míst zápisu|Jeden zápis umístění|Poznámka|
|---|---|---|---|
|Zapsat ze smlouvy SLA latence < 10 MS při P99|**Ano**|Ne|Účty s jeden zápis umístění se vám účtovat další síti mezi různými oblastmi latence při každém zápisu.|
|Čtení ze smlouvy SLA latenci < 10 MS při P99|**Ano**|Ano| |
|Zápis SLA zajišťující 99,999 %|**Ano**|Ne|Účty s jeden zápis umístění zaručit smlouva SLA o 99,99 %|
|RTO = 0|**Ano**|Ne|Nula dolů čas pro zápis regionální havárie. Účty s umístěním jeden zápis mají RTO 15 minut.|

## <a name="next-steps"></a>Další kroky

Pokud stále chcete zakázat EnableMultipleWriteLocations ve vašem účtu Azure Cosmos, [vytvořit lístek podpory](https://azure.microsoft.com/support/create-ticket/).
