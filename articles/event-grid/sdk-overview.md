---
title: Sady Azure Event Grid SDK
description: Popisuje sady SDK pro Azure Event Grid. Tyto sady SDK poskytují správu, publikování a spotřebu.
ms.topic: reference
ms.date: 07/07/2019
ms.openlocfilehash: 2b21d8bf2c393b096e9046765e6cfadc76baffbf
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119102"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid sady SDK pro správu a publikování

Event Grid poskytuje sady SDK, které vám umožní programově spravovat prostředky a odesílat události.

## <a name="management-sdks"></a>Sady Management SDK

Sady Management SDK umožňují vytvářet, aktualizovat a odstraňovat témata a odběry Event gridu. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Přejít](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Sady SDK pro rovinu dat

Sady SDK roviny dat umožňují publikovat události do témat tím, že se postará o ověřování, vytváření událostí a asynchronní odeslání do určeného koncového bodu. Umožňují také využívat události první strany. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Přejít](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Další kroky

* Například aplikace naleznete v tématu [Event Grid Samples Code](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Úvod do Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Příkazy Event Grid v rozhraní příkazového řádku Azure CLI najdete v tématu [Azure CLI](/cli/azure/eventgrid).
* Příkazy Event Grid v PowerShellu najdete v tématu [PowerShell](/powershell/module/az.eventgrid).
