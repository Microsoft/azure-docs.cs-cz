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
ms.openlocfilehash: 2cd3e2e471135242b52459abc231a0f3545e05e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Datový proud protokolu Azure činnosti do centra událostí
[ **Protokol činnosti Azure** ](monitoring-overview-activity-logs.md) Streamovat skoro v reálném čase pro všechny aplikace pomocí předdefinované možnosti "Export" na portálu nebo povolením ID pravidla Service Bus v profilu protokolu pomocí prostředí Azure PowerShell Rutiny nebo Azure CLI.

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Co můžete dělat s protokol aktivit a Event Hubs
Můžete použít možnost streamování pro protokol činnosti několika způsoby:

* **Datový proud na protokolování a telemetrie systémů jiných výrobců** – v čase, streamování Event Hubs se stane mechanismus kanálem protokolu aktivit do jiných systémů Siem a řešení pro analýzu protokolu.
* **Vytvoření vlastní telemetrii a protokolování platformy** – Pokud už máte uživatelské telemetrie platformy nebo jsou jenom přemýšlíte o vytváření jeden vysoce škálovatelné, publikování a odběru na povaze služby Event Hubs umožňuje flexibilně ingestování protokolu aktivit. [V příručce Dana Rosanova pomocí služby Event Hubs telemetrie platformy globálním měřítku sem.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-the-activity-log"></a>Povolit vysílání datového proudu protokolu aktivit
Můžete povolit vysílání datového proudu protokolu aktivit buď programově, nebo prostřednictvím portálu. V obou případech můžete vybrat na obor názvů služby Event Hubs a zásady sdíleného přístupu pro tento obor názvů a centra událostí s název "insights protokoly operationallogs' je vytvořen v daném oboru názvů dojde k první nové aktivity protokolu události. Pokud jste na obor názvů služby Event Hubs, musíte nejprve vytvořit. Pokud jste dříve streamování aktivity protokolu události na tento obor názvů služby Event Hubs, bude znovu použita centra událostí, která byla dříve vytvořena. Zásada sdíleného přístupu definuje oprávnění, která má streamování mechanismus. V současné době vyžadují streamování do centra událostí **spravovat**, **odeslat**, a **naslouchání** oprávnění. Můžete vytvářet nebo upravovat Event Hubs obor názvů sdílených zásad přístupu na portálu Azure na kartě "Konfigurace" pro obor názvů. Aktualizovat profil protokolu protokol aktivit zahrnout streamování, uživatele, provedení změny, musí mít oprávnění ListKey na tuto událost rozbočovače autorizační pravidlo.

Obor názvů služby Event Hubs nemusí být ve stejném předplatném jako předplatné emitování protokoly tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

### <a name="via-azure-portal"></a>Prostřednictvím portálu Azure
1. Přejděte na **protokol aktivit** části Použití všechny služby search na levé straně na portálu.
   
    ![Přejděte na protokol aktivit v portálu](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. Klikněte **exportovat** tlačítka v horní části protokolu aktivit. Všimněte si, že nastavení filtru, který měl použité při zobrazování protokol aktivit v předchozí zobrazení nemají žádný vliv na nastavení exportu – těch, které jsou určeny pouze pro filtrování, co se zobrazí při prohlížení protokolu aktivit na portálu.
   
    ![Tlačítko Exportovat portálu](./media/monitoring-stream-activity-logs-event-hubs/export.png)
3. V oddílu, který se zobrazí, vyberte **všech oblastech**. Nevybírejte konkrétní oblasti.
   
    ![Exportovat protokol aktivit](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)
    
    > [!WARNING]
    > Vybrat pouze všech oblastech. V opačném případě bude neproběhly klíče události, které obsahují by jinak očekávané přijímat. Toto je skutečnost, že protokol aktivit je globální protokolu (není místní), takže většina události nemají oblasti s nimi spojených.
    >
    >
    
4. Klikněte na tlačítko **Uložit** uložit tato nastavení. Nastavení se použije okamžitě do vašeho předplatného.
5. Pokud máte několik předplatných, by měl opakujte tuto akci a odeslat veškerá data do stejné centra událostí.

### <a name="via-powershell-cmdlets"></a>Via PowerShell Cmdlets
Pokud profil protokolu již existuje, musíte nejprve odebrat tento profil.

1. Použití `Get-AzureRmLogProfile` a zjistit, zda existuje profil protokolu
2. Pokud ano, použít `Remove-AzureRmLogProfile` jeho odebrání.
3. Použití `Set-AzureRmLogProfile` k vytvoření profilu:

```powershell

Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

```

ID pravidla Service Bus je řetězec s Tento formát: {service bus ID prostředku} /authorizationrules/ {název klíče}, např. 

### <a name="via-azure-cli"></a>Via Azure CLI
Pokud profil protokolu již existuje, musíte nejprve odebrat tento profil.

1. Použití `azure insights logprofile list` a zjistit, zda existuje profil protokolu
2. Pokud ano, použít `azure insights logprofile delete` jeho odebrání.
3. Použití `azure insights logprofile add` k vytvoření profilu:

```azurecli-interactive
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

ID pravidla Service Bus je řetězec s Tento formát: `{service bus resource ID}/authorizationrules/{key name}`.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Způsob, jakým využívají data protokolu ze služby Event Hubs?
[Zde jsou k dispozici schéma pro protokol aktivit](monitoring-overview-activity-logs.md). Každá událost je v pole objektů JSON BLOB názvem "záznamů".

## <a name="next-steps"></a>Další kroky
* [Archiv protokol aktivit na účet úložiště](monitoring-archive-activity-log.md)
* [Přečtěte si přehled protokol činnosti Azure](monitoring-overview-activity-logs.md)
* [Nastavit výstrahy na základě aktivity protokolu události](insights-auditlog-to-webhook-email.md)

