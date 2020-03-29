---
title: Aktualizace schématu analýzy provozu Azure – březen 2020 | Dokumenty společnosti Microsoft
description: Ukázkové dotazy s novými poli ve schématu Traffic Analytics.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969065"
---
# <a name="sample-queries-with-new-fields-in-traffic-analytics-schema-august-2019-schema-update"></a>Ukázkové dotazy s novými poli ve schématu Analýzy provozu (aktualizace schématu ze srpna 2019)

[Schéma protokolu analýzy provozu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) bylo aktualizováno tak, aby zahrnovalo následující nová pole: **SrcPublicIPs_s** , **DestPublicIPs_s**, **NSGRule_s**. V příštích několika měsících budou zastaralá následující starší pole: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s VM_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.
Nová pole poskytují informace o zdrojových a cílových IP adresy a zjednodušují dotazy.

Níže jsou uvedeny tři příklady, které ukazují, jak nahradit stará pole novými.

## <a name="example-1---vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-publicips_s"></a>Příklad 1 - VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s, PublicIPs_s

Není potřeba odvodit zdroj a cíl případy pro Azure a externí veřejné toky z FlowDirection_s pole pro AzurePublic a ExternalPublic toky konkrétně. V případě síťového virtuálního zařízení (Network Virtual Appliance) může být FlowDirection_s pole také nevhodné.

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


## <a name="example-2---nsgrules_s"></a>Příklad 2 - NSGRules_s

Dřívější pole bylo formátované: <hodnota indexu 0)>| NSG_RULENAME<>|<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

Dříve jsme agregovat data mezi NSG a NSGRules. Nyní se neagregujeme. Takže NSGList_s obsahuje pouze jeden skupiny nsg a NSGRules_s také obsahuje pouze jedno pravidlo. Takže jsme odstranili složité formátování zde a totéž lze nalézt v jiných oblastech, jak je uvedeno níže:

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

## <a name="example-3---flowcount_d"></a>Příklad 3 - FlowCount_d

Vzhledem k tomu, že nechceme klubová data v rámci NSG, je FlowCount_d jednoduše AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d.
Pouze 1 z výše uvedených 4 bude nenulová a zbytek tři bude 0. A to by znamenat stav a počet v nic, kde byl zachycen tok.

Pokud tok byl povolen, bude vyplněno jedno z polí s předponou "Povoleno". Else jedno pole s předponou "Odepřeno" budou naplněny.
Pokud tok byl příchozí, jedno z polí suffixed s "\_d " jako "InFlows_d" suffixed pole bude naplněna. Jinak bude naplněn "OutFlows_d".

V závislosti na výše uvedených 2 podmínkách víme, který ze 4 bude obydlený.


## <a name="next-steps"></a>Další kroky
Odpovědi na nejčastější dotazy najdete v [tématu Nejčastější dotazy k analýze provozu,](traffic-analytics-faq.md) kde najdete podrobnosti o [funkcích,](traffic-analytics.md) v dokumentaci k analýze provozu.
