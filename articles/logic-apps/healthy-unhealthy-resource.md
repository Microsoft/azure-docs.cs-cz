---
title: Nastavení protokolování pro monitorování aplikací logiky v Azure Security Center
description: Nastavením diagnostického protokolování Sledujte stav svých Logic Apps prostředků v Azure Security Center.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 42b33a5b96de7334f8310b040052c633342f5e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712381"
---
# <a name="set-up-logging-to-monitor-logic-apps-in-azure-security-center"></a>Nastavení protokolování pro monitorování aplikací logiky v Azure Security Center

Když monitorete Logic Apps prostředky v [Microsoft Azure Security Center](../security-center/security-center-introduction.md), můžete zkontrolovat, [jestli aplikace logiky sledují následující výchozí zásady](#view-logic-apps-health-status). Když povolíte protokolování a správně nastavíte umístění protokolů, Azure zobrazí stav prostředku Logic Apps. V tomto článku se dozvíte, jak nakonfigurovat diagnostické protokolování, a ujistěte se, že jsou všechny vaše aplikace logiky v dobrém stavu.

> [!TIP]
> Chcete-li zjistit aktuální stav služby Logic Apps, přečtěte si [stránku stavu Azure](https://status.azure.com/), která obsahuje seznam stavů různých produktů a služeb v každé oblasti, kterou máte k dispozici.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud předplatné nemáte, [Vytvořte si bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Stávající aplikace logiky s [povolenou diagnostikou protokolování](#enable-diagnostic-logging)
* Log Analytics pracovní prostor, který je nutný k povolení protokolování pro vaši aplikaci logiky. Pokud nemáte pracovní prostor, [vytvořte nejprve pracovní prostor](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-diagnostic-logging"></a>Povolení protokolování diagnostiky

Než budete moct zobrazit stav prostředků pro aplikace logiky, musíte nejdřív [nastavit diagnostické protokolování](monitor-logic-apps-log-analytics.md). Pokud již máte pracovní prostor Log Analytics, můžete protokolování povolit buď při vytváření aplikace logiky, nebo na stávajících aplikacích logiky.

> [!TIP]
> Výchozí doporučení je povolení diagnostických protokolů pro Logic Apps. Toto nastavení však můžete nastavit pro aplikace logiky. Když povolíte diagnostické protokoly pro aplikace logiky, můžete použít informace k analýze incidentů zabezpečení.

### <a name="check-diagnostic-logging-setting"></a>Ověřte nastavení diagnostického protokolování

Pokud si nejste jistí, jestli vaše aplikace logiky mají povolené diagnostické protokolování, můžete vrátit se změnami Security Center:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu hledání zadejte a vyberte **Security Center**.
1. V nabídce řídicího panelu Security Center v části **Obecné** vyberte **doporučení**.
1. V tabulce návrhů zabezpečení Najděte a zaškrtněte políčko **Povolit auditování a** &gt; **protokoly diagnostiky v Logic Apps musí být povolené** v tabulce ovládacích prvků zabezpečení.
1. Na stránce doporučení rozbalte oddíl **opravný postup** a zkontrolujte možnosti. Diagnostiku Logic Apps můžete povolit výběrem možnosti **Rychlá oprava!** nebo podle pokynů k ruční nápravě.

## <a name="view-logic-apps-health-status"></a>Zobrazení stavu aplikace Logic Apps

Po [Povolení protokolování diagnostiky](#enable-diagnostic-logging)uvidíte stav aplikací logiky v Security Center.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu hledání zadejte a vyberte **Security Center**.
1. V nabídce řídicího panelu Security Center v části **Obecné** vyberte **inventarizace**.
1. Na stránce inventarizace vyfiltrujte seznam assets (prostředky), abyste zobrazili pouze Logic Apps prostředky. V nabídce Stránka vyberte **typy prostředků** &gt; **Logic Apps**.

   Čítač **špatných prostředků** zobrazuje počet aplikací logiky, které Security Center považují za chybné.
1.  V seznamu prostředků Logic Apps zkontrolujte sloupec **doporučení** . Pokud chcete zkontrolovat podrobnosti o stavu konkrétní aplikace logiky, vyberte název prostředku nebo vyberte tlačítko se třemi tečkami (**...**) &gt; . **Zobrazit prostředek**
1.  Chcete-li opravit případné problémy se stavem prostředků, postupujte podle kroků uvedených v seznamu pro aplikace logiky.

Pokud je protokolování diagnostiky již povolené, může se jednat o problém s cílem pro vaše protokoly. Přečtěte si [, jak opravit problémy s různými cíli diagnostického protokolování](#fix-diagnostic-logging-for-logic-apps).

## <a name="fix-diagnostic-logging-for-logic-apps"></a>Oprava protokolování diagnostiky pro Logic Apps

Pokud [jsou vaše aplikace logiky uvedené v Security Center v nesprávném stavu](#view-logic-apps-health-status), otevřete aplikaci logiky v zobrazení kódu v Azure Portal nebo prostřednictvím rozhraní příkazového řádku Azure CLI. Pak zkontrolujte cílovou konfiguraci pro diagnostické protokoly: [azure Log Analytics](#log-analytics-and-event-hubs-destinations), [Azure Event Hubs](#log-analytics-and-event-hubs-destinations)nebo [účet Azure Storage](#storage-account-destination).

### <a name="log-analytics-and-event-hubs-destinations"></a>Log Analytics a Event Hubs cíle

Pokud jako cíl pro diagnostické protokoly Logic Apps používáte Log Analytics nebo Event Hubs, ověřte následující nastavení. 

1. Chcete-li ověřit, zda jste povolili diagnostické protokoly, zkontrolujte, zda je pole nastavení diagnostiky `logs.enabled` nastaveno na hodnotu `true` . 
1. Pokud chcete potvrdit, že jste místo toho nastavili účet úložiště jako cíl, zkontrolujte, že `storageAccountId` je pole nastavené na `false` .

Například:

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
                "notEquals": "true"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/storageAccountId",
                "exists": false
            }
        ]
    }
] 
```

### <a name="storage-account-destination"></a>Cíl účtu úložiště

Pokud jako cíl pro diagnostické protokoly Logic Apps použijete účet úložiště, ověřte následující nastavení.

1. Chcete-li ověřit, zda jste povolili diagnostické protokoly, zkontrolujte, zda je pole nastavení diagnostiky `logs.enabled` nastaveno na hodnotu `true` .
1. Chcete-li ověřit, že jste povolili zásady uchovávání informací pro diagnostické protokoly, zkontrolujte, zda `retentionPolicy.enabled` je pole nastaveno na hodnotu `true` .
1. Pokud chcete potvrdit, že jste nastavili dobu uchovávání 0-365 dní, zaškrtněte políčko `retentionPolicy.days` u pole nastaveno na číslo (včetně) mezi 0 a 365.

```json
"allOf": [
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.enabled",
        "equals": "true"
    },
    {
        "anyOf": [
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "0"
            },
            {
                "field": "Microsoft.Insights/diagnosticSettings/logs[*].retentionPolicy.days",
                "equals": "[parameters('requiredRetentionDays')]"
            }
          ]
    },
    {
        "field": "Microsoft.Insights/diagnosticSettings/logs.enabled",
        "equals": "true"
    }
]
```