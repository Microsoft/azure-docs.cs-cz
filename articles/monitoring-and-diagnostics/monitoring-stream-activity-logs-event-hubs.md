---
title: "Stream protokol činnosti Azure do centra událostí | Microsoft Docs"
description: "Zjistěte, jak k vysílání datového proudu protokol činnosti Azure do centra událostí."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Datový proud protokolu Azure činnosti do centra událostí
Dá Streamovat [protokol činnosti Azure](monitoring-overview-activity-logs.md) skoro v reálném čase pro všechny aplikace, a to buď:

* Pomocí integrovaných **exportovat** možnost na portálu
* Povolení služby Azure Service Bus pravidlo ID v profilu protokolu prostřednictvím rutin prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Co můžete dělat s protokol aktivit a Event Hubs
Můžete použít možnost streamování pro protokol činnosti těmito dvěma způsoby:

* **Datový proud na protokolování a telemetrie systémů jiných výrobců**: V čase, streamování Azure Event Hubs se stane mechanismus kanálem protokolu aktivit do jiných systémů Siem a řešení pro analýzu protokolu.
* **Vytvoření vlastní telemetrii a protokolování platformy**: Pokud už máte uživatelské telemetrie platformy nebo jsou přemýšlení o vytváření jeden, vysoce škálovatelné publikování a odběru povaha Event Hubs umožňuje flexibilně ingestování protokolu aktivit. Další informace najdete v tématu [Dana Rosanova video o používání služby Event Hubs v globálním měřítku telemetrie platformy](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Povolit vysílání datového proudu protokolu aktivit
Můžete povolit vysílání datového proudu protokolu aktivit buď programově, nebo prostřednictvím portálu. V obou případech vyberete na obor názvů služby Event Hubs a zásady sdíleného přístupu pro tento obor názvů. Centra událostí s název insights-logs-operationallogs se vytvoří v daném oboru názvů dojde k první nové aktivity protokolu události. 

Pokud nemáte na obor názvů služby Event Hubs, musíte nejprve vytvořit. Pokud jste dříve streamování aktivity protokolu události na tento obor názvů služby Event Hubs, bude znovu použita centra událostí, která byla dříve vytvořena. 

Zásada sdíleného přístupu definuje oprávnění, která má streamování mechanismus. V současné době vyžadují streamování Event Hubs **spravovat**, **odeslat**, a **naslouchání** oprávnění. Můžete vytvářet nebo upravovat zásady sdíleného přístupu pro obor názvů služby Event Hubs na portálu Azure v části **konfigurace** kartě pro obor názvů služby Event Hubs. 

Aktualizovat profil protokolu protokol aktivit zahrnout streamování, uživatele, který je provedení změny, musí mít oprávnění ListKey na tuto službu Event Hubs autorizační pravidlo. Obor názvů služby Event Hubs nemusí být ve stejném předplatném jako odběr, který je generování protokoly, tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

### <a name="via-the-azure-portal"></a>Prostřednictvím portálu Azure
1. Přejděte do **protokol aktivit** části pomocí **všechny služby** vyhledávání na levé straně na portálu.
   
   ![Výběr protokol aktivit ze seznamu služeb na portálu](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Vyberte **exportovat** tlačítka v horní části protokolu.
   
   ![Tlačítko Exportovat na portálu](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   Všimněte si, že nastavení filtru, který měl použité při zobrazování protokol aktivit v předchozí zobrazení nemají žádný vliv na nastavení exportu. Ty jsou pouze pro účely filtrování najdete při procházení prostřednictvím protokolu aktivit na portálu.
3. V oddílu, který se zobrazí, vyberte **všech oblastech**. Nevybírejte konkrétní oblasti.
   
   ![Export části](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > Pokud nic jiné než vyberete **všech oblastech**, budete neproběhly klíče události, které chcete dostávat. Protokol aktivit je globální protokolu (není místní), takže většina události nemají oblasti s nimi spojených. 
   > 

4. Vyberte **Uložit** uložit tato nastavení. Nastavení se použije okamžitě do vašeho předplatného.
5. Pokud máte několik předplatných, opakujte tuto akci a odeslat veškerá data do stejné centra událostí.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell
Pokud profil protokolu již existuje, musíte nejprve odebrat tento profil.

1. Použití `Get-AzureRmLogProfile` a zjistit, zda existuje profil protokolu.
2. Pokud ano, použít `Remove-AzureRmLogProfile` jeho odebrání.
3. Použití `Set-AzureRmLogProfile` k vytvoření profilu:

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

ID pravidla Service Bus je řetězec s Tento formát: `{service bus resource ID}/authorizationrules/{key name}`. 

### <a name="via-azure-cli"></a>Via Azure CLI
Pokud profil protokolu již existuje, musíte nejprve odebrat tento profil.

1. Použití `azure insights logprofile list` a zjistit, zda existuje profil protokolu.
2. Pokud ano, použít `azure insights logprofile delete` jeho odebrání.
3. Použití `azure insights logprofile add` k vytvoření profilu:

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

ID pravidla Service Bus je řetězec s Tento formát: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="consume-the-log-data-from-event-hubs"></a>Tato data protokolu ze služby Event Hubs
Schéma pro protokol aktivit je k dispozici v [sledovat činnost předplatné s protokol činnosti Azure](monitoring-overview-activity-logs.md). Každá událost je v pole objektů JSON BLOB názvem *záznamy*.

## <a name="next-steps"></a>Další postup
* [Archiv protokol aktivit na účet úložiště](monitoring-archive-activity-log.md)
* [Přečtěte si přehled protokol činnosti Azure](monitoring-overview-activity-logs.md)
* [Nastavit výstrahy na základě aktivity protokolu události](insights-auditlog-to-webhook-email.md)

