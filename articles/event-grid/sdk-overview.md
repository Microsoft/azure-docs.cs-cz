---
title: Sady SDK sítě událostí Azure
description: Popisuje sady SDK pro Azure Event Grid. Tyto sady SDK poskytují správu, publikování a spotřebu.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60822848"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Sady SDK sítě událostí pro správu a publikování

Event Grid poskytuje sady SDK, které umožňují programově spravovat prostředky a účtovat události.

## <a name="management-sdks"></a>Sady SDK pro správu

Sady SDK pro správu umožňují vytvářet, aktualizovat a odstraňovat témata a odběry mřížky událostí. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Přejít](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Sady SDK datové roviny

Sady SDK roviny dat umožňují zaúčtovat události na témata tím, že se postaráte o ověřování, formování události a asynchronně zaúčtování do zadaného koncového bodu. Umožňují také využívat události první strany. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Přejít](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Další kroky

* Například aplikace naleznete v [tématu ukázky kódu mřížky událostí](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Úvod do mřížky událostí najdete v tématu [Co je event grid?](overview.md)
* Příkazy Grid událostí v azure cli, najdete v [tématu Azure CLI](/cli/azure/eventgrid).
* Příkazy event gridu v PowerShellu najdete v [tématu PowerShell](/powershell/module/az.eventgrid).
