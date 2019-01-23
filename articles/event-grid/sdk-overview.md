---
title: Sady SDK pro Azure Event Grid
description: Popisuje sady SDK pro Azure Event Grid. Tyto sady SDK poskytují spotřeby, publikování a správy.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 53a1f50647e05b71afe6accb85f8a90cd8b7e253
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470393"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid sady SDK pro správu a publikování

Event gridu poskytuje sady SDK, které umožňují programově spravovat vaše prostředky a odesílat události.

## <a name="management-sdks"></a>Management SDK

Sady SDK pro správu umožňují vytvářet, aktualizovat a odstraňovat témata event gridu a předplatných. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Sady SDK roviny dat

Rovina dat sady SDK umožňují odesílat události na témata podle postará o ověřování, které tvoří události a asynchronně účtování v určeném koncovém bodě. Umožňují také zpracovávat události první strany. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Další postup

* Příklad naleznete v tématu [ukázky kódu služby Event Grid](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* Úvod do služby Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Event Grid příkazy v rozhraní příkazového řádku Azure, najdete v části [rozhraní příkazového řádku Azure](/cli/azure/eventgrid).
* Event Grid příkazy v prostředí PowerShell najdete v [Powershellu](/powershell/module/azurerm.eventgrid).
