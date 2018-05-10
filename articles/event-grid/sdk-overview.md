---
title: Sady SDK služby Azure Event mřížky
description: Popisuje sady SDK pro Azure Event mřížky. Tyto sady SDK zajištění správy, publikování a využívání.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: d9bb4b3b161060f20fca34760872a24cbfcabf30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Událost mřížky sady SDK pro správu a publikování

Událost mřížky poskytuje sady SDK, které vám umožní programově spravovat prostředky a odesílat události.

## <a name="management-sdks"></a>Management SDK

Správa sad SDK umožňují vytvářet, aktualizovat a odstraňovat události mřížky témat a odběrů. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Roviny data sady SDK

Roviny data sady SDK umožňují odesílají události do témata podle postará o ověřování, které tvoří události a asynchronně publikování na zadaný koncový bod. Umožňují také zpracovávat první strany události. V současné době jsou k dispozici následující sady SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [co je mřížky událostí?](overview.md)
* Příkazy událostí mřížky v Azure CLI, najdete v části [rozhraní příkazového řádku Azure](/cli/azure/eventgrid).
* Událost mřížky příkazy v prostředí PowerShell, najdete v části [prostředí PowerShell](/powershell/module/azurerm.eventgrid).