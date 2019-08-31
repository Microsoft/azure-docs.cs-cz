---
title: Konfigurace nastavení pravidla inteligentního zjišťování pro Azure Application Insights pomocí šablon Azure Resource Manager | Microsoft Docs
description: Automatizace správy a konfigurace pravidel inteligentního zjišťování Application Insights Azure pomocí šablon Azure Resource Manager
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
ms.openlocfilehash: e7a54c2e207a27f3519375df09d0c930a92d52d6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193720"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Správa pravidel inteligentního vyhledávání Application Insights pomocí šablon Azure Resource Manager

Pravidla inteligentního zjišťování v Application Insights můžou být spravovaná a nakonfigurovaná pomocí [šablon Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Tuto metodu lze použít při nasazování nových Application Insightsch prostředků pomocí Automatizace Azure Resource Manager nebo pro úpravu nastavení existujících prostředků.

## <a name="smart-detection-rule-configuration"></a>Konfigurace pravidla inteligentního zjišťování

Můžete konfigurovat následující nastavení pravidel inteligentního zjišťování:
- Pokud je pravidlo povolené (výchozí hodnota je **true**)
- Pokud se mají e-maily posílat uživatelům přidruženým ke [čtenářům monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) předplatného a k [monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) rolí přispěvatele, když se najde detekce (výchozí hodnota je **true**)
- Všichni další příjemci e-mailu, kteří by měli dostávat oznámení, když zjistí detekci.
    -  Konfigurace e-mailu není k dispozici pro pravidla inteligentního zjišťování označená jako _Náhled_.

Pokud chcete pomocí Azure Resource Manager nakonfigurovat nastavení pravidla, konfigurace pravidla inteligentního zjišťování je teď k dispozici jako vnitřní prostředek v prostředku Application Insights s názvem **ProactiveDetectionConfigs**.
Pro zajištění maximální flexibility je možné nakonfigurovat každé pravidlo inteligentního zjišťování s jedinečným nastavením oznámení.

## 

## <a name="examples"></a>Příklady

Níže jsou uvedeny některé příklady, jak nakonfigurovat nastavení pravidel inteligentního vyhledávání pomocí šablon Azure Resource Manager.
Všechny ukázky odkazují na prostředek Application Insights s názvem _"MyApplication"_ a na pravidlo inteligentního zjišťování trvání celé závislosti, které je interně pojmenované _"longdependencyduration"_ .
Nezapomeňte nahradit název Application Insights prostředku a zadat odpovídající interní název pravidla inteligentního zjišťování. V následující tabulce najdete seznam odpovídajících vnitřních Azure Resource Manager názvů pro každé pravidlo inteligentního zjišťování.

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Zakázat odesílání e-mailových oznámení pro pravidlo inteligentního zjišťování

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

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Pravidlo upozornění na anomálii v2 (neklasické)

Tato šablona Azure Resource Manager demonstruje konfiguraci pravidla upozornění na anomálii v2 s závažností 2. Tato nová verze pravidla výstrahy anomálií při selhání je součástí nové platformy pro upozorňování Azure a nahrazuje klasickou verzi, která je vyřazena jako součást procesu odchodu [klasických výstrah](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

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
> Tato šablona Azure Resource Manager je jedinečná pro pravidlo upozornění nepravidelnosti selhání v2 a liší se od ostatních pravidel inteligentního zjišťování, která jsou popsaná v tomto článku.   

## <a name="smart-detection-rule-names"></a>Názvy pravidel inteligentního zjišťování

Níže je tabulka s názvy pravidel inteligentního zjišťování, které se zobrazují na portálu, spolu s jejich interními názvy, které by se měly používat v Azure Resource Manager šabloně.

> [!NOTE]
> Pravidla inteligentního zjišťování označená jako _Preview_ nepodporují e-mailová oznámení. Proto můžete pro tato pravidla nastavit pouze vlastnost _Enabled_ . 

| Název pravidla Azure Portal | Interní název
|:---|:---|
| Pomalé načítání stránky | slowpageloadtime |
| Dlouhá doba odezvy serveru | slowserverresponsetime |
| Dlouhá doba trvání závislosti | longdependencyduration |
| Prodloužení doby odezvy serveru | degradationinserverresponsetime |
| Pokles v trvání závislosti | degradationindependencyduration |
| Snížení výkonu v poměru závažnosti trasování (Preview) | extension_traceseveritydetector |
| Neobvyklé zvýšení objemu výjimek (Preview) | extension_exceptionchangeextension |
| Zjištěna potenciální nevracení paměti (Preview) | extension_memoryleakextension |
| Zjištěn potenciální problém se zabezpečením (Preview) | extension_securityextensionspackage |
| Neobvyklé zvýšení denních objemů dat (Preview) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Další kroky

Další informace o automatickém zjišťování:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)
