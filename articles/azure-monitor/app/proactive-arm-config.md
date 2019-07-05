---
title: Konfigurace nastavení pravidla inteligentního zjišťování Azure Application Insights pomocí šablon Azure Resource Manageru | Dokumentace Microsoftu
description: Automatizace správy a konfigurace pravidla inteligentního zjišťování Azure Application Insights pomocí šablon Azure Resource Manageru
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 6bb89eec0b4905e101bed87d3d3fc617dec589e0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477867"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Spravovat pravidla inteligentního zjišťování služby Application Insights pomocí šablon Azure Resource Manageru

Pravidla inteligentního zjišťování ve službě Application Insights je možné spravovat a konfigurovat pomocí [šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).
Tuto metodu lze použít při nasazování nové prostředky Application Insights s Azure Resource Manageru automation, nebo pro úpravu nastavení existujících prostředků.

## <a name="smart-detection-rule-configuration"></a>Konfigurace pravidla inteligentního zjišťování

Můžete nakonfigurovat následující nastavení pro pravidlo inteligentní zjišťování:
- Pokud je pravidlo povolené (výchozí hodnota je **true**.)
- Pokud mají být e-maily zasílány uživatelé přidružení k odběru [Čtenář monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) a [Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) role, když se najde detekce (výchozí hodnota je **true**.)
- Žádné další e-mailové příjemce, kteří by měl dostat oznámení při zjištění nebyl nalezen.
    -  E-mailové konfigurace není k dispozici pro pravidla inteligentního zjišťování označen jako _ve verzi preview_.

Pokud chcete povolit, konfigurace nastavení pravidla prostřednictvím Azure Resource Manageru, konfigurace pravidla inteligentního zjišťování je teď dostupná jako vnitřní prostředků v rámci prostředku Application Insights s názvem **ProactiveDetectionConfigs**.
Pro maximální flexibilitu a každé pravidlo inteligentní zjišťování můžete použít nastavení jedinečné oznámení.

## 

## <a name="examples"></a>Příklady

Níže jsou příklady ukazující, jak nakonfigurovat nastavení pravidla inteligentního zjišťování pomocí šablon Azure Resource Manageru.
Všechny ukázky odkazovat na prostředek Application Insights s názvem _"myApplication"_ , a "dlouhý závislost doba trvání inteligentního zjišťování pravidla", který interně názvem _"longdependencyduration"_ .
Ujistěte se, že chcete nahradit název prostředku Application Insights a k určení příslušné inteligentního zjišťování interní název pravidla. Zkontrolujte v následující tabulce seznamu odpovídající vnitřní názvů Azure Resource Manageru pro každé pravidlo inteligentní zjišťování.

### <a name="disable-a-smart-detection-rule"></a>Zakázat pravidlo inteligentního zjišťování

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Zakázat odesílání e-mailová oznámení pro pravidlo inteligentního zjišťování

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Přidat další e-mailové příjemce pravidla inteligentního zjišťování

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

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Pravidlo výstrahy v2 (který není klasický) anomálie pro selhání

Tuto šablonu Azure Resource Manageru ukazuje konfiguraci pravidla upozornění anomálie selhání v2 s závažnost 2. Tato nová verze, pravidlo upozornění na anomálie selhání je součástí nové výstrahy platformy Azure a nahradí klasickou verzi, která se vyřazuje jako součást [classic výstrahy procesu vyřazení](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
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
> Tuto šablonu Azure Resource Manageru je jedinečné pro pravidla upozornění anomálie selhání v2 a se liší od jiných classic pravidla inteligentního zjišťování popsaných v tomto článku.   

## <a name="smart-detection-rule-names"></a>Názvy pravidel inteligentního zjišťování

Níže je tabulka názvů pravidla inteligentního zjišťování, jak se objeví na portálu společně s jejich interní názvy, které byste měli použít ve šablony Azure Resource Manageru.

> [!NOTE]
> Pravidla inteligentního zjišťování označen jako _ve verzi preview_ nepodporují e-mailová oznámení. Proto lze nastavit pouze _povolené_ vlastnost pro tato pravidla. 

| Název pravidla Azure portal | Interní název
|:---|:---|
| Čas načítání stránky pomalé | slowpageloadtime |
| Dlouhá doba odezvy serveru | slowserverresponsetime |
| Dlouhá doba trvání závislosti | longdependencyduration |
| Snížení doby odezvy serveru | degradationinserverresponsetime |
| Pokles v trvání závislosti | degradationindependencyduration |
| Snížení výkonnosti v poměru závažnost trasování (preview) | extension_traceseveritydetector |
| Neobvyklý nárůst počtu výjimek (preview) | extension_exceptionchangeextension |
| Zjištěna potenciální nevrácená paměť (preview) | extension_memoryleakextension |
| Zjistil potenciální problém zabezpečení (preview) | extension_securityextensionspackage |
| Neobvyklý nárůst míry denní objem dat (preview) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Další kroky

Další informace o automatické zjišťování:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)
