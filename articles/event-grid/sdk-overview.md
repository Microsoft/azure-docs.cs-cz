---
title: Sady SDK služby Azure Event mřížky
description: Popisuje sady SDK pro Azure Event mřížky. Tyto sady SDK zajištění správy, publikování a využívání.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 82f08341f8c96695a4ceddb19d1b610d70b89f88
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
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