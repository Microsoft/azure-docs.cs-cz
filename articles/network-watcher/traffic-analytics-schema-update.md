---
title: Aktualizace schématu Azure Traffic Analytics – březen 2020 | Microsoft Docs
description: Ukázkové dotazy s novými poli ve schématu Analýza provozu.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 0e9d37e3a89473e59b94168f8f8c80e7a6621107
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969065"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Ukázkové dotazy s novými poli ve schématu Analýza provozu (aktualizace schématu srpna 2019)

[Schéma protokolu Analýza provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) se aktualizovalo tak, aby obsahovalo následující nová pole **: SrcPublicIPs_s** , **DestPublicIPs_s** **NSGRule_s**. V následujících několika měsících budou tato starší pole zastaralá: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.
Nová pole poskytují informace o zdrojových a cílových IP adresách a zjednodušují dotazy.

Níže jsou tři příklady, které ukazují, jak nahradit stará pole novými.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Příklad 1 VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s, PublicIPs_s

Pro Azure a externí veřejné toky z FlowDirection_sho pole pro toky AzurePublic a ExternalPublic nemusíme odvodit zdrojový a cílový případ. V případě síťové virtuální zařízení (síťové virtuální zařízení) může být pole FlowDirection_s nevhodné, aby bylo možné ho použít taky.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```


## <a name="example-2---nsgrules_s"></a>Příklad 2 – NSGRules_s

Předchozí pole bylo formátu: < hodnota indexu 0) > | < NSG_RULENAME > |<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Dříve jsme použili pro agregaci dat napříč NSG a NSGRules. Teď neagregujeme. Takže NSGList_s obsahuje jenom jeden NSG a NSGRules_s se taky používá k tomu, aby obsahovalo jenom jedno pravidlo. Proto jsme odebrali komplikované formátování, které najdete v dalších polích, jak je uvedeno níže:

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3---flowcount_d"></a>Příklad 3 – FlowCount_d

Vzhledem k tomu, že data nepoužíváme napříč NSG, je FlowCount_d jednoduše AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d.
Pouze jedna z výše uvedených 4 bude nenulová a zbývající tři budou 0. A to by znamenalo, že v síťovém adaptéru, ve kterém byl tok zachycen, byl zaznamenán stav a počet.

Pokud byl tok povolený, naplní se jedno z polí s předponou "povoleno". Naplní se některá pole s předponou "Odepřít".
Pokud tok byl příchozí, naplní se jedno z polí s příponou "\_d" jako "InFlows_d". Naplní se jinak "OutFlows_d".

V závislosti na předchozích 2 podmínkách ví, že se naplní jedna ze 4.


## <a name="next-steps"></a>Další kroky
Odpovědi na nejčastější dotazy najdete v tématu [Nejčastější dotazy k analýze provozu](traffic-analytics-faq.md) a zobrazení podrobností o funkcích najdete v tématu [dokumentace k analýze provozu](traffic-analytics.md) .
