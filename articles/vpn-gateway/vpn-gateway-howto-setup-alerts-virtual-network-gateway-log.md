---
title: 'Brána Azure VPN: Konfigurace výstrah na události diagnostického protokolu'
description: Postup konfigurace výstrah při událostech diagnostického protokolu brány VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878897"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Nastavení výstrah na události diagnostického protokolu z brány VPN Gateway

Tento článek vám pomůže nastavit výstrahy na základě událostí diagnostického protokolu z Azure VPN Gateway pomocí Azure Log Analytics. 

V Azure jsou k dispozici následující protokoly:

|***Název*** | ***Popis*** |
|---        | ---               |
|GatewayDiagnosticLog | Obsahuje diagnostické protokoly pro události konfigurace brány, primární změny a události údržby. |
|TunnelDiagnosticLog | Obsahuje události změny stavu tunelového propojení. Události připojení/odpojení tunelového propojení mají souhrnný důvod změny stavu, pokud je to možné |
|RouteDiagnosticLog | Protokoluje změny statických tras a událostí protokolu BGP, ke kterým dochází v bráně. |
|Protokol IKEDiagnosticLog | Protokoly IKE řídí zprávy a události na bráně |
|P2SDiagnosticLog | Protokoluje zprávy a události ovládacího prvku point-to-site v bráně. Informace o zdroji připojení jsou k dispozici pouze pro připojení IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Nastavení výstrah na webu Azure Portal

Následující příklad kroky vytvoří výstrahu pro událost odpojení, která zahrnuje tunel VPN site-to-site:


1. Na webu Azure Portal vyhledejte **analýzu protokolů** v části **Všechny služby** a vyberte **pracovní prostory Log Analytics**.

   ![Výběry pro práci do pracovních prostorů Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Vytvořit")

2. Na stránce **Log Analytics** vyberte **Vytvořit.**

   ![Stránka Log Analytics s tlačítkem Vytvořit](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Vyberte")

3. Vyberte **Vytvořit nový** a vyplňte podrobnosti.

   ![Podrobnosti pro vytvoření pracovního prostoru Analýzy protokolů](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Vyberte")

4. Vyhledejte bránu VPN v okně**nastavení diagnostiky** **monitoru.** > 

   ![Výběrpro nalezení brány VPN v nastavení diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Vyberte")

5. Chcete-li diagnostiku zapnout, poklepejte na bránu a vyberte **možnost Zapnout diagnostiku**.

   ![Výběrpro zapnutí diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Vyberte")

6. Vyplňte podrobnosti a ujistěte se, že **odeslat do protokolu Analytics** a **TunnelDiagnosticLog** jsou vybrány. Zvolte pracovní prostor Analýzy protokolů, který jste vytvořili v kroku 3.

   ![Vybraná zaškrtávací políčka](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Vyberte")

   > [!NOTE]
   > Může trvat několik hodin, než se data zpočátku zobrazí.

7. Přejděte do přehledu prostředku brány virtuální sítě a na kartě **Monitorování** vyberte **Výstrahy.** Potom vytvořte nové pravidlo výstrahy nebo upravte existující pravidlo výstrahy.

   ![Výběr pro vytvoření nového pravidla výstrahy](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Vyberte")

   ![bod na místo](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Vyberte")
8. Vyberte pracovní prostor Analýzy protokolů a prostředek.

   ![Výběry pro pracovní prostor a zdroj](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Vyberte")

9. Vyberte **vlastní hledání protokolu** jako logiku signálu v části Přidat **podmínku**.

   ![Výběry pro vlastní hledání protokolu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Vyberte")

10. Do textového pole **Vyhledávací dotaz** zadejte následující dotaz. Nahradit hodnoty v <> a TimeGenerated podle potřeby.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Nastavte prahovou hodnotu na hodnotu 0 a vyberte **Hotovo**.

    ![Zadání dotazu a výběr prahové hodnoty](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Vyberte")

11. Na stránce **Vytvořit pravidlo** vyberte v části **SKUPINY AKCÍ** možnost Vytvořit **nový.** Vyplňte podrobnosti a vyberte **OK**.

    ![Podrobnosti o nové skupině akcí](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Vyberte")

12. Na stránce **Vytvořit pravidlo** vyplňte podrobnosti pro akce **A přizpůsobení** a ujistěte se, že se v části **NÁZEV SKUPINY AKCÍ** zobrazí správný název. Chcete-li vytvořit pravidlo, vyberte **vytvořit pravidlo výstrahy.**

    ![Výběry pro vytvoření pravidla](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Vyberte")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Nastavení výstrah pomocí PowerShellu

Následující příklad kroky vytvořit výstrahu pro událost odpojení, která zahrnuje tunelové propojení VPN site-to-site.

1. Vytvoření pracovního prostoru Analýzy protokolů:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Zapněte diagnostiku brány VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Vytvořte skupinu akcí.

   Tento kód vytvoří skupinu akcí, která odešle e-mailové oznámení při aktivaci výstrahy:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Vytvořte pravidlo výstrahy na základě vlastního vyhledávání v protokolu:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci výstrah na metriky tunelového propojení najdete v [tématu Nastavení výstrah na metrikách brány VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
