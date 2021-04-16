---
title: Přehled značek služby Azure Firewall
description: Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529544"
---
# <a name="azure-firewall-service-tags"></a>Značky služby Azure Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služby Azure Firewall lze použít v poli cíl síťových pravidel. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služby

Seznam značek služeb, které jsou k dispozici pro použití v sítích brány firewall Azure, najdete v tématu [značky služby virtuální sítě](../virtual-network/service-tags-overview.md#available-service-tags) .

## <a name="configuration"></a>Konfigurace

Azure Firewall podporuje konfiguraci značek služeb prostřednictvím PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal.

### <a name="configure-via-azure-powershell"></a>Konfigurace přes Azure PowerShell

V tomto příkladu je nutné nejprve získat kontext na naši dříve vytvořenou instanci Azure Firewall.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Dál je potřeba vytvořit nové pravidlo.  Pro zdroj nebo cíl můžete zadat textovou hodnotu značky služby, kterou chcete využít, jak je uvedeno výše v tomto článku.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Dál je potřeba aktualizovat proměnnou obsahující naši definici Azure Firewall novými vytvořenými pravidly sítě.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Nakonec je nutné potvrdit změny pravidla sítě do spuštěné instance Azure Firewall.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Další kroky

Další informace o pravidlech Azure Firewall najdete v tématu [logika zpracování pravidel Azure firewall](rule-processing.md).
