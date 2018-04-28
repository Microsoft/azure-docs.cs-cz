---
title: Migrace Azure výstrahy na události správy do protokolu upozornění | Microsoft Docs
description: Výstrahy na události správy budou odebrány říjen 1. Připravte migraci existujícího výstrahy.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: johnkem
ms.openlocfilehash: 1cc02664a345da49c276a6876d5cdfaf5bdb8477
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="migrate-azure-alerts-on-management-events-to-activity-log-alerts"></a>Migrace Azure výstrahy na události správy do protokolu činnosti výstrahy


> [!WARNING]
> Výstrahy na události správy se vypne na nebo po říjen 1. Následující pokyny slouží k pochopení, pokud máte tyto výstrahy a migraci, je-li tomu tak.
>
> 

## <a name="what-is-changing"></a>Co je změna

Azure monitorování (dříve Statistika Azure) nabízí možnost vytvořit výstrahu, která aktivuje z událostí správy a vygeneruje oznámení na webhooku adresu URL nebo e-mailové adresy. Jste vytvořili jednu z těchto výstrah některý z těchto způsobů:
* Na portálu Azure pro určité typy prostředků v části monitorování -> Výstrahy -> Přidat výstrahy, kde "Výstrah na" je nastaven na "Události"
* Spuštěním rutiny Add-AzureRmLogAlertRule prostředí PowerShell
* Přímo pomocí [výstrahy REST API](http://docs.microsoft.com/rest/api/monitor/alertrules) s odata.type = "ManagementEventRuleCondition" a dataSource.odata.type = "RuleManagementEventDataSource"
 
Následující skript prostředí PowerShell vrátí seznam všech výstrah v události správy, které máte v vašeho předplatného, jakož i podmínky nastavit pro jednotlivé výstrahy.

```powershell
Connect-AzureRmAccount
$alerts = $null
foreach ($rg in Get-AzureRmResourceGroup ) {
  $alerts += Get-AzureRmAlertRule -ResourceGroup $rg.ResourceGroupName
}
foreach ($alert in $alerts) {
  if($alert.Properties.Condition.DataSource.GetType().Name.Equals("RuleManagementEventDataSource")) {
    "Alert Name: " + $alert.Name
    "Alert Resource ID: " + $alert.Id
    "Alert conditions:"
    $alert.Properties.Condition.DataSource
    "---------------------------------"
  }
} 
```

Pokud máte žádné výstrahy týkající se správy událostí, výše uvedené rutiny prostředí PowerShell výstup řadu zprávy upozornění podobné následujícímu:

`WARNING: The output of this cmdlet will be flattened, i.e. elimination of the properties field, in a future release to improve the user experience.`

Tyto zprávy upozornění můžete ignorovat. Pokud máte výstrahy na události správy, bude výstup této rutiny prostředí PowerShell vypadat takto:

```
Alert Name: webhookEvent1
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/webhookEvent1
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : microsoft.web/sites/start/action
ResourceGroupName    : 
ResourceProviderName : 
Status               : succeeded
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Web/sites/samplealertapp

---------------------------------
Alert Name: someclilogalert
Alert Resource ID: /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/microsoft.insights/alertrules/someclilogalert
Alert conditions:

EventName            : 
EventSource          : 
Level                : 
OperationName        : Start
ResourceGroupName    : 
ResourceProviderName : 
Status               : 
SubStatus            : 
Claims               : Microsoft.Azure.Management.Monitor.Management.Models.RuleManagementEventClaimsDataSource
ResourceUri          : /subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>/providers/Microsoft.Compute/virtualMachines/Seaofclouds

---------------------------------
```

Každá výstraha je oddělená na přerušovanou čáru a podrobnosti zahrnují ID prostředku výstraha a konkrétní pravidlo, které jsou monitorovány.

Tato funkce přešla do [Azure monitorování aktivity protokolu výstrah](monitoring-activity-log-alerts.md). Tyto nové výstrahy umožňují nastavit stav aktivity protokolu událostí a přijímat oznámení, když o novou událost odpovídá podmínku. Také nabízí několik vylepšení z výstrahy na události správy:
* Můžete opakovaně použít skupině příjemců oznámení ("akce") napříč více výstrah pomocí [skupiny akcí](monitoring-action-groups.md), snižuje složitost změny příjemce výstrahu.
* Můžete obdržet oznámení přímo na váš telefon SMS pomocí akce skupiny.
* Můžete [vytvářet výstrahy, aktivity protokolu pomocí šablony Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md).
* Podmínky lze vytvořit s větší flexibilitu a složitost tak, aby vyhovovala vašim konkrétním potřebám.
* Oznámení se doručují rychleji.
 
## <a name="how-to-migrate"></a>Postup migrace
 
Pokud chcete vytvořit oznámení nové aktivity na protokolu, můžete buď:
* Postupujte podle [našem návodu o tom, jak vytvořit upozornění na portálu Azure](monitoring-activity-log-alerts.md)
* Zjistěte, jak [vytvořena výstraha pomocí šablony Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
 
Výstrahy na události správy, které jste předtím vytvořili, nebude se migrovat automaticky aktivity protokolu výstrah. Budete muset použít předchozí skript prostředí PowerShell k zobrazení seznamu výstrahy na události správy jste nakonfigurovali a ručně je znovu vytvořit jako aktivity protokolu výstrahy. To je třeba provést před říjen 1, po jejímž uplynutí výstrahy na události správy se už nebude zobrazovat ve vašem předplatném Azure. Jiné typy výstrah, Azure, včetně monitorování Azure metriky výstrah, Application Insights výstrahy a upozornění analýzy protokolů jsou tato změna nemá vliv. Pokud máte nějaké otázky, post v komentářích níže.


## <a name="next-steps"></a>Další postup

* Další informace o [protokol aktivit](monitoring-overview-activity-logs.md)
* Konfigurace [aktivity protokolu výstrahy prostřednictvím portálu Azure](monitoring-activity-log-alerts.md)
* Konfigurace [aktivity protokolu výstrahy prostřednictvím Resource Manageru](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Zkontrolujte [schématu výstrahy webhooku protokolu aktivit](monitoring-activity-log-alerts-webhook.md)
* Další informace o [oznámení o službách](monitoring-service-notifications.md)
* Další informace o [skupiny akcí](monitoring-action-groups.md)
