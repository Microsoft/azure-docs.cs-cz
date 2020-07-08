---
title: Jak integrovat společné schéma výstrah s Logic Apps
description: Naučte se, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah k obsluze všech výstrah.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77668226"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Jak integrovat společné schéma výstrah s Logic Apps

V tomto článku se dozvíte, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah ke zpracování všech výstrah.

## <a name="overview"></a>Přehled

[Běžné schéma výstrah](https://aka.ms/commonAlertSchemaDocs) poskytuje standardizované a rozšiřitelné schéma JSON napříč všemi různými typy výstrah. Běžné schéma výstrah je nejužitečnější, když se využívají programově – prostřednictvím webhooků, runbooků a Logic Apps. V tomto článku ukážeme, jak může být jediná aplikace logiky vytvořená pro zpracování všech výstrah. Stejné zásady je možné použít i pro další programové metody. Aplikace logiky popsané v tomto článku vytvoří dobře definované proměnné pro [pole "Essential"](alerts-common-schema-definitions.md#essentials)a také popisuje, jak můžete zpracovat logiku specifickou pro [Typ výstrahy](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Požadavky 

V tomto článku se předpokládá, že čtenář je známý 
* Nastavení pravidel upozornění ([metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [protokol](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [Protokol aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Nastavení [skupin akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Povolení [společného schématu výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) v rámci skupin akcí

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Vytvoření aplikace logiky s využitím společného schématu výstrah

1. Postupujte podle kroků popsaných v části [Vytvoření aplikace logiky](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Vyberte aktivační událost: **když se přijme požadavek HTTP**.

    ![Triggery aplikace logiky](media/action-groups-logic-app/logic-app-triggers.png "Triggery aplikace logiky")

1.  Výběrem **Upravit** změňte aktivační událost požadavku HTTP.

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

1. Vyberte **+** **Nový krok** a pak zvolte **přidat akci**.

    ![Přidání akce](media/action-groups-logic-app/add-action.png "Přidání akce")

1. V této fázi můžete přidat nejrůznější konektory (Microsoft teams, časová rezerva, Salesforce atd.) na základě konkrétních podnikových požadavků. Klíčové slovo "základní" můžete použít předem. 

    ![Základní pole](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Základní pole")
    
    Alternativně můžete vytvořit podmíněný logiku na základě typu výstrahy pomocí možnosti Expression.

    ![Výraz aplikace logiky](media/alerts-common-schema-integrations/logic-app-expressions.png "Výraz aplikace logiky")
    
     [Pole ' monitoringService '](alerts-common-schema-definitions.md#alert-context) umožňuje jednoznačně identifikovat typ výstrahy na základě toho, ve kterém můžete vytvořit podmíněný Logic.

    
    Například následující fragment kódu kontroluje, zda se jedná o výstrahu protokolu na základě Application Insights, a pokud ano, budou vytištěny výsledky hledání. V opačném případě vytiskne ' NEDEF '.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Přečtěte si další informace o [psaní výrazů aplikace logiky](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o skupinách akcí](../../azure-monitor/platform/action-groups.md).
* [Přečtěte si další informace o běžném schématu výstrah](https://aka.ms/commonAlertSchemaDocs).

