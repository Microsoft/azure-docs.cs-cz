---
title: Azure Cosmos DB výhod s více hlavními servery
description: Seznamte se s výhodami více hlavních míst v Azure Cosmos DB, porovnáním požadavků na latenci a smlouvu SLA v jednom a několika umístěních pro zápis.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872005"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Pochopení výhod s více hlavními servery v Azure Cosmos DB

Operátoři Cosmos DB účtu by si měli zvolit odpovídající globální konfiguraci distribuce, aby se zajistila latence, dostupnost a RTO požadavků na jejich aplikace. Účty Azure Cosmos nakonfigurované s více umístěními pro zápis nabízí významné výhody pro účty s jedním umístěním pro zápis, včetně 99,999% dostupností smlouvy SLA pro zápis, < 10 99 pro latenci zápisu na percentil a RTO = 0 v oblasti regionální havárie.

## <a name="comparison-of-features"></a>Porovnání funkcí

|Požadavek na aplikaci|Více umístění pro zápis|Jedno umístění pro zápis|Poznámka|
|---|---|---|---|
|Smlouva SLA o latenci zápisu < 10ms na p99|**Ano**|Ne|Účty s jedním umístěním pro zápis mají u každého zápisu za následek další latenci sítě mezi oblastmi.|
|SLA pro čtení < 10ms na p99 na latenci|**Ano**|Ano| |
|Zápis SLA pro 99,999%|**Ano**|Ne|Účty s zaručenou smlouvou SLA o úrovni jednoho zápisu 99,99%|
|RTO = 0|**Ano**|Ne|Nulová doba pro zápisy v případě regionálních katastrof. Účty s jedním umístěním pro zápis mají RTO 15 minut.|

## <a name="next-steps"></a>Další kroky

Pokud chcete v účtu Azure Cosmos dál zakázat EnableMultipleWriteLocations, [otevřete prosím lístek podpory](https://azure.microsoft.com/support/create-ticket/).
