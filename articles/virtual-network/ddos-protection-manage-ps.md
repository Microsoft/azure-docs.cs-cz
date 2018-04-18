---
title: "Správa Azure DDoS ochrany standardní pomocí Azure PowerShell | Microsoft Docs"
description: "Naučte se spravovat Azure DDoS ochrany standardní pomocí Azure PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 33ff6cfcacd1632dc49b448e70361e1cb2ce1176
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Správa Azure DDoS ochrany standardní pomocí Azure PowerShell

Zjistěte, jak povolit a zakázat distribuovaná útok na dostupnost služby (Denial) ochrany, a zmírnit útoku DDoS s Azure DDoS ochrany standardní pomocí telemetrie. DDoS ochrany standardní chrání prostředky Azure, jako jsou virtuální počítače, nástroje pro vyrovnávání zatížení a aplikačních bran, které mají Azure [veřejnou IP adresu](virtual-network-public-ip-address.md) přiřazen. Další informace o DDoS ochrany standardní a jeho funkce, najdete v části [DDoS ochrany standardní přehled](ddos-protection-overview.md). 

>[!IMPORTANT]
>Azure DDoS ochrany Standard (Ochrana proti útoku DDoS) se aktuálně ve verzi preview. Ochrana proti útoku DDoS podporovat omezený počet prostředků Azure a je k dispozici pouze v vyberte počet oblastí. Seznam dostupných oblastí najdete v tématu [DDoS ochrany standardní přehled](ddos-protection-overview.md). Budete muset [zaregistrovat pro službu](http://aka.ms/ddosprotection) během omezené preview získat ochrana proti útoku DDoS pro vaše předplatné povolený. Po registraci, vás kontaktovat tým Azure DDoS, který vás provede procesem povolování.


## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Login-AzureRmAccount` a postupujte podle pokynů na obrazovce. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. Pokud je potřeba nainstalovat nebo upgradovat prostředí Azure PowerShell najdete v tématu [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Povolit DDoS ochrany Standard - nové virtuální sítě

Chcete-li vytvořit virtuální síť s povolenou ochranou proti DDoS, spusťte v následujícím příkladu:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

Tento příklad vytvoří virtuální síť se dvěma podsítěmi a dva servery DNS. Účinek zadání serverů DNS ve virtuální síti je, že síťové adaptéry nebo virtuální počítače, které jsou nasazeny do této virtuální síti dědit tyto servery DNS jako výchozí. Ochrana proti útoku DDoS je povoleno pro všechny chráněné prostředky ve virtuální síti.

> [!WARNING]
> Když vyberete oblast, zvolte ze seznamu v podporované oblasti [Azure DDoS ochrany standardní přehled](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Povolit ochrana proti útoku DDoS na existující virtuální síť

Chcete-li povolit ochrana proti útoku DDoS na existující virtuální síť, spusťte v následujícím příkladu:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> Virtuální síť musí existovat v podporované oblasti. Seznam podporovaných oblastí najdete v tématu [Azure DDoS ochrany standardní přehled](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Zakažte ochranu DDoS ve virtuální síti

Chcete-li zakázat ochrana proti útoku DDoS ve virtuální síti, spusťte v následujícím příkladu:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>Zkontrolujte stav ochrany DDoS virtuální sítě 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Pomocí telemetrie ochrana proti útoku DDoS

Telemetrie na útok je zajišťováno prostřednictvím Azure monitorování v reálném čase. Telemetrie je k dispozici pouze po dobu, která veřejnou IP adresu je pod zmírnění dopadů. Neuvidíte telemetrie před nebo po zmírnit útok.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Nakonfigurujte upozornění na metriky ochrana proti útoku DDoS

Využití konfiguraci výstrah monitorování Azure, můžete vybrat všechny dostupné metriky ochrana proti útoku DDoS pro upozornění, když dojde active zmírnění při útoku.

#### <a name="configure-email-alert-rules-via-azure"></a>Konfigurace pravidla výstrah e-mailu prostřednictvím Azure

1. Získání seznamu odběry, které máte k dispozici. Ověřte, že pracujete s správné předplatné. Pokud ne, nastavte ji na ten správný pomocí výstup Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. K zobrazení seznamu existujících pravidel ve skupině prostředků, použijte následující příkaz: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. K vytvoření pravidla, musíte nejdříve k dispozici následující informace: 

    - ID prostředku pro prostředek, kterou chcete nastavit výstrahy pro.
    - Metriky definice je k dispozici pro tento prostředek. Jeden způsob, jak získat ID prostředku je použití portálu Azure. Za předpokladu, že byl již vytvořený, vyberte ho v portálu Azure. Na další stránce vyberte *vlastnosti* pod *nastavení* části. **ID prostředku** je pole na další stránce. Dalším způsobem je použít [Průzkumníka prostředků Azure](https://resources.azure.com/). Je například ID prostředku pro veřejnou IP adresu:`/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    Následující příklad vytvoří výstrahu pro veřejné IP adresy přidružené k prostředku ve virtuální síti. Metrika na vytvoření výstrahy **útoku DDoS pod nebo není**. Toto je logická hodnota, 1 nebo 0. A **1** znamená, můžete v útoku. A **0** znamená nejsou napadené. Výstraha se vytvoří při spuštění útoku během posledních 5 minut.

    K vytvoření webhook, jehož nebo odeslat e-mail, když se vytvoří výstrahu, je nutné nejprve vytvořte e-mailu nebo webhooku. Po vytvoření e-mailu nebo webhooku, hned vytvořit pravidlo s `-Actions` značka, jak je znázorněno v následujícím příkladu. Nelze přidružit webhooku nebo e-mailů s existující pravidla pomocí prostředí PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Zkontrolujte, zda upozornění správně vytvořen prohlížením pravidlo:

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

Můžete si také přečíst informace o [konfigurace webhooky](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [aplikace logiky](../logic-apps/logic-apps-overview.md) pro vytvoření výstrahy.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Konfigurovat protokolování na metriky ochrana proti útoku DDoS

Odkazovat [prostředí PowerShell rychlý start ukázky](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vám pomohou přístup a konfiguraci služby Azure pomocí prostředí PowerShell protokolování diagnostiky.

## <a name="next-steps"></a>Další postup

- [Další informace o diagnostických protokolů Azure.](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analýza protokolů z úložiště Azure s analýzy protokolů](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Začínáme s Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)