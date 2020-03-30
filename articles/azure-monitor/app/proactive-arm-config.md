---
title: Nastavení pravidel inteligentního zjišťování – Přehledy aplikací Azure
description: Automatizace správy a konfigurace pravidel inteligentního zjišťování Azure Application Insights pomocí šablon Azure Resource Manager
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294906"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Správa pravidel inteligentního zjišťování Application Insights pomocí šablon Azure Resource Manager

Pravidla inteligentního zjišťování v Application Insights lze spravovat a konfigurovat pomocí [šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
Tuto metodu lze použít při nasazování nových prostředků Application Insights s automatizací Azure Resource Manager nebo pro úpravu nastavení stávajících prostředků.

## <a name="smart-detection-rule-configuration"></a>Konfigurace pravidla inteligentního zjišťování

Můžete konfigurovat následující nastavení pravidel inteligentního zjišťování:
- Pokud je pravidlo povoleno (výchozí hodnota je **true**.)
- Pokud e-maily by měly být odesílány uživatelům přidruženým k [předplatnému monitorovací čtečky](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) a [monitorování přispěvatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role při zjištění je nalezen (výchozí hodnota je **true**.)
- Další příjemci e-mailu, kteří by měli dostat oznámení při zjištění.
    -  Konfigurace e-mailu není k dispozici pro pravidla inteligentní detekce označená jako _náhled_.

Chcete-li povolit konfiguraci nastavení pravidla prostřednictvím Správce prostředků Azure, konfigurace pravidla inteligentního zjišťování je teď k dispozici jako vnitřní prostředek v rámci prostředku Application Insights s názvem **ProactiveDetectionConfigs**.
Pro maximální flexibilitu lze každé pravidlo inteligentního zjišťování nakonfigurovat s jedinečným nastavením oznámení.

## <a name="examples"></a>Příklady

Níže je uvedeno několik příkladů, které ukazují, jak nakonfigurovat nastavení pravidel inteligentního zjišťování pomocí šablon Azure Resource Manager.
Všechny ukázky odkazují na prostředek Application Insights s názvem _"myApplication"_ a "dlouhé trvání závislostí inteligentní zjišťování pravidlo", který je interně s názvem _"longdependencyduration"_.
Nezapomeňte nahradit název prostředku Application Insights a určit příslušný interní název pravidla inteligentního zjišťování. V následující tabulce najdete seznam odpovídajících interních názvů Azure Resource Manageru pro každé pravidlo inteligentního zjišťování.

### <a name="disable-a-smart-detection-rule"></a>Zakázání pravidla inteligentního zjišťování

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Zakázání odesílání e-mailových oznámení pro pravidlo inteligentnídetekce

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Přidání dalších příjemců e-mailu pro pravidlo inteligentního zjišťování

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Názvy pravidel inteligentního zjišťování

Níže je tabulka inteligentní zjišťování názvy pravidel, jak se zobrazí na portálu, spolu s jejich vnitřní názvy, které by měly být použity v šabloně Azure Resource Manager.

> [!NOTE]
> Pravidla inteligentního zjišťování označená jako _náhled_ nepodporují e-mailová oznámení. Proto můžete nastavit pouze _povolenou_ vlastnost pro tato pravidla. 

| Název pravidla portálu Azure | Interní název
|:---|:---|
| Pomalý čas načítání stránky | slowpageloadtime |
| Pomalá doba odezvy serveru | slowserverresponsetime |
| Dlouhá doba trvání závislosti | doba trvání longdependency |
| Snížení doby odezvy serveru | degradaceinserverresponsetime |
| Degradace v době trvání závislosti | degradacezahrnujícídoba |
| Degradace v poměru závažnosti trasování (náhled) | extension_traceseveritydetector |
| Abnormální nárůst objemu výjimek (náhled) | extension_exceptionchangeextension |
| Potenciální nevracení paměti (náhled) | extension_memoryleakextension |
| Byl zjištěn potenciální problém se zabezpečením (náhled) | extension_securityextensionspackage |
| Abnormální nárůst denního objemu dat (náhled) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Pravidlo výstrahy anomálií selhání

Tato šablona Správce prostředků Azure ukazuje konfiguraci pravidla výstrahy anomálií selhání se závažností 2. Tato nová verze pravidla upozornění anomálií selhání je součástí nové platformy upozornění Azure a nahrazuje klasickou verzi, která je vyřazena jako součást [procesu klasické výstrahy odchodu do důchodu](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

> [!NOTE]
> Anomálie selhání je globální služba, proto je v globálním umístění vytvořeno umístění pravidla.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Tato šablona Správce prostředků Azure je jedinečná pro pravidlo výstrahy Anomálií selhání a liší se od ostatních klasických pravidel inteligentního zjišťování popsaných v tomto článku. Pokud chcete spravovat anomálie selhání ručně, to se provádí ve výstrahách monitorování Azure, zatímco všechna ostatní pravidla inteligentní detekce se spravují v podokně inteligentní detekce v ui.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o automatickém zjišťování:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)
