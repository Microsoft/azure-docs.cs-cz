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
ms.date: 07/19/2018
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: 7e4756fb241500a2f153b452c6dc5a4e332bdd6b
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54881592"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Spravovat pravidla inteligentního zjišťování služby Application Insights pomocí šablon Azure Resource Manageru

Pravidla inteligentního zjišťování ve službě Application Insights je možné spravovat a konfigurovat pomocí [šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).
Tuto metodu lze použít při nasazování nové prostředky Application Insights s Azure Resource Manageru automation, nebo pro úpravu nastavení existujících prostředků.

## <a name="smart-detection-rule-configuration"></a>Konfigurace pravidla inteligentního zjišťování

Můžete nakonfigurovat následující nastavení pro pravidlo inteligentní zjišťování:
- Pokud je pravidlo povolené (výchozí hodnota je **true**.)
- Pokud mají být e-maily zasílány vlastníkům předplatného, přispěvatelé a čtenáři při detekce nenajde (výchozí hodnota je **true**.)
- Žádné další e-mailové příjemce, kteří by měl dostat oznámení při zjištění nebyl nalezen.

Pokud chcete povolit, konfigurace nastavení pravidla prostřednictvím Azure Resource Manageru, konfigurace pravidla inteligentního zjišťování je teď dostupná jako vnitřní prostředků v rámci prostředku Application Insights s názvem **ProactiveDetectionConfigs**.
Pro maximální flexibilitu a každé pravidlo inteligentní zjišťování můžete použít nastavení jedinečné oznámení.

## <a name="examples"></a>Příklady

Níže jsou příklady ukazující, jak nakonfigurovat nastavení pravidla inteligentního zjišťování pomocí šablon Azure Resource Manageru.
Všechny ukázky odkazovat na prostředek Application Insights s názvem _"myApplication"_, a "dlouhý závislost doba trvání inteligentního zjišťování pravidla", který interně názvem _"longdependencyduration"_.
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

## <a name="smart-detection-rule-names"></a>Názvy pravidel inteligentního zjišťování

Níže je tabulka názvů pravidla inteligentního zjišťování, jak se objeví na portálu společně s jejich interní názvy, které byste měli použít ve šablony Azure Resource Manageru.

> [!NOTE]
> Pravidla inteligentního zjišťování označený jako ve verzi preview nepodporují e-mailová oznámení. Proto lze nastavit pouze vlastnost enabled pro tato pravidla. 

| Název pravidla Azure portal | Interní název
|:---|:---|
| Pomalé načítání stránky | slowpageloadtime |
| Dlouhá doba odezvy serveru | slowserverresponsetime |
| Dlouhá doba trvání závislosti | longdependencyduration |
| Prodloužení doby odezvy serveru | degradationinserverresponsetime |
| Pokles v trvání závislosti | degradationindependencyduration |
| Snížení výkonnosti v poměru závažnost trasování (preview) | extension_traceseveritydetector |
| Neobvyklý nárůst počtu výjimek (preview) | extension_exceptionchangeextension |
| Zjištěna potenciální nevrácená paměť (preview) | extension_memoryleakextension |
| Zjistil potenciální problém zabezpečení (preview) | extension_securityextensionspackage |
| Zjistil se problém využití prostředků (preview) | extension_resourceutilizationextensionspackage |

## <a name="who-receives-the-classic-alert-notifications"></a>Kdo přijímá oznámení výstrah (klasické)?

Tato část pouze platí pro klasických upozornění inteligentního zjišťování a pomůže vám optimalizovat vaše oznámení o výstrahách Ujistěte se, že pouze požadované příjemci dostávat oznámení. Bližší informace o rozdílech mezi [klasických upozornění](../platform/alerts-classic.overview.md) a předložit nové prostředí upozornění [výstrahy přehledovém článku](../platform/alerts-overview.md). Inteligentní zjišťování aktuálně upozorní potřebné klasických upozornění prostředí podpory. Jedinou výjimkou je [inteligentního zjišťování výstrah v Azure cloud services](./proactive-cloud-services.md). K řízení oznámení oznámení pro výstrahy funkce Inteligentní zjišťování v cloudu Azure services pomocí [skupiny akcí](../platform/action-groups.md).

* Doporučujeme vám používat konkrétní příjemců oznámení výstrah inteligentní zjišťování/classic.

* Upozornění inteligentního zjišťování **hromadné/skupiny** zaškrtávací políčko, pokud je povoleno, odešle uživatelům s rolí vlastník, Přispěvatel nebo Čtenář v rámci předplatného. V důsledku toho _všechny_ uživatelé s přístupem k předplatnému prostředku Application Insights jsou v rozsahu a budou dostávat oznámení. 

> [!NOTE]
> Pokud aktuálně používáte službu **hromadné/skupiny** zaškrtávací políčko a zakázat, nebude možné vrátit zpět změny.

## <a name="next-steps"></a>Další kroky

Další informace o automatické zjišťování:

- [Anomálie selhání](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Nevracení paměti](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomálie výkonu](../../azure-monitor/app/proactive-performance-diagnostics.md)