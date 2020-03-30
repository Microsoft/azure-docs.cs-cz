---
title: Jak integrovat společné schéma výstrah s Logic Apps
description: Zjistěte, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah ke zpracování všech vašich výstrah.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668226"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Jak integrovat společné schéma výstrah s Logic Apps

Tento článek ukazuje, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah ke zpracování všech vašich výstrah.

## <a name="overview"></a>Přehled

[Společné schéma výstrah](https://aka.ms/commonAlertSchemaDocs) poskytuje standardizované a rozšiřitelné schéma JSON ve všech typech výstrah. Společné schéma výstrah je nejužitečnější při programovém využití – prostřednictvím webhooků, runbooků a aplikací logiky. V tomto článku ukážeme, jak může být vytvořena jedna aplikace logiky pro zpracování všech vašich výstrah. Stejné principy lze použít i na jiné programové metody. Aplikace logiky popsané v tomto článku vytvoří dobře definované proměnné pro ["základní" pole](alerts-common-schema-definitions.md#essentials)a také popisuje, jak můžete zpracovat logiku specifické [pro typ výstrahy.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Požadavky 

Tento článek předpokládá, že čtenář je obeznámen s 
* Nastavení pravidel výstrah[(metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [protokol](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [protokol aktivit)](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)
* Nastavení [skupin akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Povolení [schématu běžných výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) z řad skupin akcí

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Vytvoření aplikace logiky využívající společné schéma výstrah

1. Podle [pokynů uvedených vytvořte aplikaci logiky](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Vyberte aktivační událost: **Při přijetí požadavku HTTP**.

    ![Triggery aplikace logiky](media/action-groups-logic-app/logic-app-triggers.png "Triggery aplikace logiky")

1.  Výběrem **možnosti Upravit** změníte aktivační událost požadavku HTTP.

    ![Aktivační události požadavku HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Aktivační události požadavku HTTP")


1.  Zkopírujte a vložte následující schéma:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Vyberte **+** **Nový krok** a pak zvolte Přidat **akci**.

    ![Přidání akce](media/action-groups-logic-app/add-action.png "Přidání akce")

1. V této fázi můžete přidat různé konektory (Microsoft Teams, Slack, Salesforce atd.) na základě vašich konkrétních obchodních požadavků. Můžete použít 'základní pole' out-of-the-box. 

    ![Základní pole](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Základní pole")
    
    Případně můžete vytvářet podmíněnou logiku na základě typu výstrahy pomocí možnosti Výraz.

    ![Výraz aplikace logiky](media/alerts-common-schema-integrations/logic-app-expressions.png "Výraz aplikace logiky")
    
     [Pole "monitoringService"](alerts-common-schema-definitions.md#alert-context) umožňuje jednoznačně identifikovat typ výstrahy, na základě kterého můžete vytvořit podmíněnou logiku.

    
    Například níže uvedený výstřižek zkontroluje, zda je výstraha výstraha založená na protokolu application insights, a pokud ano, vytiskne výsledky hledání. Jinak to vytiskne "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Další informace o [psaní výrazů aplikace logiky](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o skupinách akcí](../../azure-monitor/platform/action-groups.md).
* [Další informace o společném schématu výstrah](https://aka.ms/commonAlertSchemaDocs).

