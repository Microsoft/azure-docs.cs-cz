---
title: 'Azure VPN Gateway: Konfigurace upozornění na události protokolu diagnostických prostředků'
description: Naučte se, jak nastavit výstrahy na základě událostí protokolu prostředků z Azure VPN Gateway pomocí Azure Monitor Log Analytics.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656610"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Nastavení upozornění na události protokolu prostředků z VPN Gateway

Tento článek vám pomůže nastavit výstrahy na základě událostí protokolu prostředků z Azure VPN Gateway pomocí Azure Monitor Log Analytics. 

V Azure jsou k dispozici následující protokoly prostředků:

|***Název** _ | _*_Popis_*_ |
|--- | --- |
|GatewayDiagnosticLog | Obsahuje protokoly prostředků pro události konfigurace brány, primární změny a události údržby. |
|TunnelDiagnosticLog | Obsahuje události změny stavu tunelu. Události připojení a odpojení tunelu mají souhrnný důvod změny stavu, pokud je to možné. |
|RouteDiagnosticLog | Zaznamená změny do statických tras a událostí protokolu BGP, ke kterým dojde v bráně. |
|IKEDiagnosticLog | Protokoluje zprávy řízení IKE a události v bráně. |
|P2SDiagnosticLog | Zaznamená v bráně zprávy řízení Point-to-site a události. K dispozici jsou informace o zdroji připojení jenom pro připojení IKEv2 a OpenVPN. |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Nastavení výstrah v Azure Portal

Následující příklad kroků vytvoří výstrahu pro událost odpojení, která zahrnuje tunel VPN typu Site-to-site:


1. V Azure Portal vyhledejte _ *Log Analytics** v části **všechny služby** a vyberte **Log Analytics pracovní prostory**.

   ![Výběry pro přechodu na Log Analytics pracovní prostory](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Vytvořit")

2. Na stránce **Log Analytics** vyberte **vytvořit** .

   ![Stránka Log Analytics s tlačítkem vytvořit](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Vyberte")

3. Vyberte **vytvořit novou** a vyplňte podrobnosti.

   ![Podrobnosti o vytvoření pracovního prostoru Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Vyberte")

4. V okně **monitorovat**  >  **nastavení diagnostiky** Najděte bránu VPN.

   ![Výběry pro vyhledání brány VPN v nastavení diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Vyberte")

5. Pokud chcete zapnout diagnostiku, poklikejte na bránu a pak vyberte **zapnout diagnostiku**.

   ![Výběry pro zapnutí diagnostiky](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Vyberte")

6. Vyplňte podrobnosti a ujistěte se, že je vybraná možnost **Odeslat do Log Analytics** a **TunnelDiagnosticLog** . Vyberte pracovní prostor Log Analytics, který jste vytvořili v kroku 3.

   ![Vybraná zaškrtávací políčka](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Vyberte")

   > [!NOTE]
   > Počáteční zobrazení dat může trvat několik hodin.

7. Přejít na přehled prostředku brány virtuální sítě a vybrat **výstrahy** na kartě **monitorování** . Pak vytvořte nové pravidlo výstrahy nebo upravte existující pravidlo výstrahy.

   ![Výběry pro vytvoření nového pravidla výstrahy](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Vyberte")

   ![Point-to-site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Vyberte")
8. Vyberte pracovní prostor Log Analytics a prostředek.

   ![Výběry pro pracovní prostor a prostředek](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Vyberte")

9. Jako logiku signálu v části **Přidat podmínku** vyberte **vlastní prohledávání protokolu** .

   ![Výběry pro vlastní prohledávání protokolu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Vyberte")

10. Do textového pole **Vyhledávací dotaz** zadejte následující dotaz. Nahraďte hodnoty v <> a TimeGenerated podle potřeby.

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

    Nastavte mezní hodnotu na 0 a vyberte **Hotovo**.

    ![Zadání dotazu a výběr prahové hodnoty](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Vyberte")

11. Na stránce **vytvořit pravidlo** vyberte v části **skupiny akcí** položku **vytvořit nový** . Vyplňte podrobnosti a vyberte **OK**.

    ![Podrobnosti nové skupiny akcí](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Vyberte")

12. Na stránce **vytvořit pravidlo** vyplňte podrobnosti pro **vlastní akce** a ujistěte se, že se v části **název skupiny akcí** zobrazuje správný název. Vyberte **vytvořit pravidlo upozornění** a vytvořte pravidlo.

    ![Výběry pro vytvoření pravidla](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Vyberte")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Nastavení výstrah pomocí prostředí PowerShell

Následující příklad postupu vytvoří výstrahu pro událost odpojení, která zahrnuje tunel VPN typu Site-to-site.

1. Vytvořit Log Analytics pracovní prostor:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Zapnout diagnostiku pro bránu sítě VPN:

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

   Tento kód vytvoří skupinu akcí, která při aktivaci výstrahy pošle e-mailové oznámení:

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

4. Vytvoření pravidla výstrahy na základě vlastního prohledávání protokolu:

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

Pokud chcete konfigurovat výstrahy pro metriky tunelu, přečtěte si téma [Nastavení výstrah pro VPN Gateway metriky](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
