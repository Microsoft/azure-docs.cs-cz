---
title: Stream protokolů aktivit Azure do služby Event Hubs
description: Naučíte se Streamovat protokol aktivit Azure do služby Event Hubs.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 41965c4a498cfa4c1f0dfefaf7615e43083eb94c
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011829"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Stream protokolů aktivit Azure do služby Event Hubs
Můžete Streamovat [protokolu aktivit Azure](monitoring-overview-activity-logs.md) téměř v reálném čase pro libovolné aplikace buď:

* Použití předdefinované **exportovat** možnosti na portálu
* ID pravidla služby Azure Service Bus v profilu protokolu prostřednictvím rutin prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Co můžete dělat s protokolu aktivit a Event Hubs
Tady jsou dva způsoby, jak můžete použít funkci streamování pro protokol aktivit:

* **Stream protokolování a telemetrie systémům třetích stran**: v průběhu času streamování služby Azure Event Hubs se stanou mechanismus kanálem váš protokol aktivit do sady Siem třetích stran a protokolu analytická řešení.
* **Vytvořit vlastní telemetrii a protokolování platformy**: Pokud už máte platformu vlastními silami sestavených telemetrická data nebo přemýšlí o vytvoříte, vysoce škálovatelné publikování a odběru povaha služby Event Hubs umožňuje flexibilně ingestování protokolu aktivit. Další informace najdete v tématu [Dan Rosanova video o používání služby Event Hubs v globálním měřítku telemetrie platformě](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Povolení streamování protokolu aktivit
Můžete povolit streamování protokolu aktivit programově nebo prostřednictvím portálu. V obou případech můžete vybrat obor názvů služby Event Hubs a zásadu sdíleného přístup pro tento obor názvů. Centrum událostí s názvem insights protokoly operationallogs se vytvoří v tomto oboru názvů při výskytu první nové události protokolu aktivit. 

Pokud nemáte k dispozici obor názvů služby Event Hubs, musíte nejdřív vytvořit. Pokud jste předtím streamovali události protokolu aktivit do tohoto oboru názvů služby Event Hubs, bude znovu použita centra událostí, který byl dříve vytvořen. 

Zásady sdíleného přístupu definuje oprávnění, která má mechanismu datových proudů. Streamování do Event Hubs v současné době vyžaduje **spravovat**, **odeslat**, a **naslouchání** oprávnění. Můžete vytvářet nebo upravovat zásady sdíleného přístupu pro službu Event Hubs oboru názvů na webu Azure Portal v části **konfigurovat** kartu pro váš obor názvů služby Event Hubs. 

K aktualizaci profilu protokolu aktivit protokolu zahrnout streamování, musí mít uživatel, který je provádění změny ListKey oprávnění na tento autorizační pravidlo Event Hubs. Obor názvů Event Hubs, nemusí být ve stejném předplatném jako předplatné, které je vysílá protokoly, jako má uživatel, který konfiguruje nastavení odpovídající roli RBAC přístup k oběma předplatným a obě předplatná jsou ve stejném tenantovi AAD.

### <a name="via-the-azure-portal"></a>Prostřednictvím portálu Azure portal
1. Přejděte **protokolu aktivit** části pomocí **všechny služby** hledání na levé straně na portálu.
   
   ![Výběr protokolu aktivit ze seznamu služeb na portálu](./media/monitoring-stream-activity-logs-event-hubs/activity-logs-portal-navigate-v2.png)
2. Vyberte **exportovat do centra událostí** tlačítko v horní části v protokolu.
   
   ![Tlačítko pro export na portálu](./media/monitoring-stream-activity-logs-event-hubs/activity-logs-portal-export-v2.png)

   Všimněte si, že nastavení filtru, který jste použili při zobrazení protokolu aktivit do předchozího zobrazení mít vliv na Exportovat nastavení. Ty jsou určeny pouze pro filtrování, co se zobrazí při procházení váš protokol aktivit na portálu.
3. V části, která se zobrazí, vyberte **všech oblastech**. Nesmí být zvolen konkrétní oblasti.
   
   ![Export části](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Pokud něco jiného než vyberete **všech oblastech**, budete si ujít klíčových událostí, které očekáváte, že pro příjem. Protokol aktivit je globální protokolu (– neregionální), tak nejvíce událostem nemají k nim má přiřazené oblasti. 
   >

4. Klikněte na tlačítko **Azure Event Hubs** možnost a vyberte obor názvů služby event hubs do protokolů, které mají být odeslány, pak klikněte na **OK**.
5. Vyberte **Uložit** nastavení uložte. Nastavení se okamžitě použijí k vašemu předplatnému.
6. Pokud máte několik předplatných, opakujte tuto akci a odeslat veškerá data do centra událostí, stejné.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Pokud profil protokolu již existuje, musíte nejprve odstranit stávající profilu protokolu a potom vytvořit nový profil protokolu.

1. Použití `Get-AzureRmLogProfile` a zjistěte, jestli existuje profil protokolu.  Pokud profil protokolu neexistuje, vyhledejte *název* vlastnost.
2. Použití `Remove-AzureRmLogProfile` odebrání profilu protokolu z hodnoty *název* vlastnost.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. Použití `Add-AzureRmLogProfile` k vytvoření nového profilu protokolu:

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Prostřednictvím rozhraní příkazového řádku Azure
Pokud profil protokolu již existuje, musíte nejprve odstranit stávající profilu protokolu a potom vytvořit nový profil protokolu.

1. Použití `az monitor log-profiles list` a zjistěte, jestli existuje profil protokolu.
2. Použití `az monitor log-profiles delete --name "<log profile name>` odebrání profilu protokolu z hodnoty *název* vlastnost.
3. Použití `az monitor log-profiles create` k vytvoření nového profilu protokolu:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Zpracovat data protokolů ze služby Event Hubs
Schéma pro protokol aktivit je k dispozici v [monitorovat aktivitu předplatného se protokol aktivit Azure](monitoring-overview-activity-logs.md). Každá událost je v poli objektů BLOB JSON s názvem *záznamy*.

## <a name="next-steps"></a>Další postup
* [Archivace protokolu aktivit do účtu úložiště](monitoring-archive-activity-log.md)
* [Přečtěte si přehled protokolu aktivit Azure](monitoring-overview-activity-logs.md)
* [Nastavte si výstrahy na základě události protokolu aktivit](monitor-alerts-unified-log-webhook.md)

