---
title: Otevření portu aplikace v nástroji pro vyrovnávání zatížení v PowerShellu
description: Ukázkový skript Azure PowerShellu – Otevření portu pro aplikaci Service Fabric v nástroji pro vyrovnávání zatížení Azure
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 4be9944a53aaf0c697d55ff567dee7f2fbb3ed85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038082"
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

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázky PowerShellu pro Azure Service Fabric najdete v [ukázkách Azure PowerShellu](../service-fabric-powershell-samples.md).
