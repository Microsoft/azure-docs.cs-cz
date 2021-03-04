---
title: Jak integrovat společné schéma výstrah s Logic Apps
description: Naučte se, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah k obsluze všech výstrah.
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 459ac43a6260e046befbe58d3531bac494903d51
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038335"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Jak integrovat společné schéma výstrah s Logic Apps

V tomto článku se dozvíte, jak vytvořit aplikaci logiky, která využívá společné schéma výstrah ke zpracování všech výstrah.

## <a name="overview"></a>Přehled

[Běžné schéma výstrah](./alerts-common-schema.md) poskytuje standardizované a rozšiřitelné schéma JSON napříč všemi různými typy výstrah. Běžné schéma výstrah je nejužitečnější, když se využívají programově – prostřednictvím webhooků, runbooků a Logic Apps. V tomto článku ukážeme, jak může být jediná aplikace logiky vytvořená pro zpracování všech výstrah. Stejné zásady je možné použít i pro další programové metody. Aplikace logiky popsané v tomto článku vytvoří dobře definované proměnné pro [pole "Essential"](alerts-common-schema-definitions.md#essentials)a také popisuje, jak můžete zpracovat logiku specifickou pro [Typ výstrahy](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Požadavky 

V tomto článku se předpokládá, že čtenář je známý 
* Nastavení pravidel upozornění ([metrika](../alerts/alerts-metric.md), [protokol](./alerts-log.md), [Protokol aktivit](./alerts-activity-log.md))
* Nastavení [skupin akcí](./action-groups.md)
* Povolení [společného schématu výstrah](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) v rámci skupin akcí

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Vytvoření aplikace logiky s využitím společného schématu výstrah

1. Postupujte podle kroků popsaných v části [Vytvoření aplikace logiky](./action-groups-logic-app.md). 

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
    
     Přečtěte si další informace o [psaní výrazů aplikace logiky](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions).

    


## <a name="next-steps"></a>Další kroky

* [Přečtěte si další informace o skupinách akcí](./action-groups.md).
* [Přečtěte si další informace o běžném schématu výstrah](./alerts-common-schema.md).