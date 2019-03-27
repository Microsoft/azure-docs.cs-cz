---
title: Ukázkový skript Azure PowerShellu – Otevření portu aplikace v nástroji pro vyrovnávání zatížení | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Otevření portu pro aplikaci Service Fabric v nástroji pro vyrovnávání zatížení Azure
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 72badb5ca2e8973fd64f5339eaff55ceb79a5ce3
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498260"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Otevření portu aplikace v nástroji pro vyrovnávání zatížení Azure

Aplikace Service Fabric spuštěná v Azure se nachází za nástrojem pro vyrovnávání zatížení Azure. Tento ukázkový skript otevře v nástroji pro vyrovnávání zatížení port a tím umožní aplikaci Service Fabric komunikovat s externími klienty. Podle potřeby upravte parametry. Pokud je váš cluster ve skupině zabezpečení sítě, [přidejte také příchozí pravidlo skupiny zabezpečení sítě](service-fabric-powershell-add-nsg-rule.md), které povolí příchozí provoz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

V případě potřeby nainstalujte modul PowerShellu pro Service Fabric se sadou [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Získá prostředek Azure.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Získá nástroj pro vyrovnávání zatížení Azure. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Přidá do nástroje pro vyrovnávání zatížení konfiguraci sondy.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Získá konfiguraci sondy pro nástroj pro vyrovnávání zatížení. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Přidá do nástroje pro vyrovnávání zatížení konfiguraci pravidla. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Nastaví cílový stav pro nástroj pro vyrovnávání zatížení. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
