---
title: Postup pro integraci ve společném schématu produktu alert Logic Apps
description: Zjistěte, jak vytvořit aplikaci logiky, které využívá připojení ve společném schématu produktu výstrah pro zpracování všech výstrah.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: b51b9f08819a4c496e051d375f6d52aaa985c8e6
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394128"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Postup pro integraci ve společném schématu produktu alert Logic Apps

V tomto článku se dozvíte, jak vytvořit aplikaci logiky, které využívá připojení ve společném schématu produktu výstrah pro zpracování všech výstrah.

## <a name="overview"></a>Přehled

[Společné schéma produktu upozornění](https://aka.ms/commonAlertSchemaDocs) poskytuje standardizované a rozšiřitelné schématu JSON v rámci všech vašich různých typů výstrah. Ve společném schématu produktu výstrah je nejužitečnější, když využít prostřednictvím kódu programu – prostřednictvím webhooků, runbooků a logic apps. V tomto článku vám ukážeme, jak jeden prostředek se můžou vytvořit pro zpracování všech výstrah. Stejné zásady můžete použít pro jiné metody prostřednictvím kódu programu. Vytvoří jasně definované proměnné pro aplikaci logiky, které jsou popsané v tomto článku ["základní" pole](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields)a také popisuje, jak můžete zpracovávat [typ výstrahy]('https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields') určitou logiku.


## <a name="prerequisites"></a>Požadavky 

Tento článek předpokládá, že je čtečka znáte 
* Nastavení pravidla výstrah ([metrika](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [protokolu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Nastavení [skupiny akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Povolení [společné schéma produktu upozornění](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) z v rámci skupiny akcí

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Vytvoření aplikace logiky ve společném schématu produktu upozornění využití

1. Postupujte podle [kroky k vytvoření aplikace logiky](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Vyberte trigger: **Při přijetí požadavku HTTP**.

    ![Triggery aplikace logiky](media/action-groups-logic-app/logic-app-triggers.png "triggery aplikace logiky")

1.  Vyberte **upravit** změnit aktivační událost požadavek HTTP.

    ![Aktivační události HTTP požadavku](media/action-groups-logic-app/http-request-trigger-shape.png "triggerů požadavek HTTP")


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

1. Vyberte **+** **nový krok** a klikněte na tlačítko **přidat akci**.

    ![Přidání akce](media/action-groups-logic-app/add-action.png "přidat akci")

1. V této fázi můžete přidat širokou škálu konektorů (Microsoft Teams, Slack, Salesforce, atd.) podle konkrétních podnikových požadavků. Můžete použít "zásadní pole" out-of-the-box. 

    ![Základní pole](media/alerts-common-schema-integrations/logic-app-essential-fields.png "zásadní pole")
    
    Alternativně můžete vytvářet podmíněnou logiku podle typu výstrahy pomocí možnosti "Výraz".

    ![Výraz aplikace logiky](media/alerts-common-schema-integrations/logic-app-expressions.png "výraz aplikace logiky")
    
     [Pole "monitoringService"]('https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields') můžete jednoznačně identifikovat typ výstrahy založené na které můžete vytvořit podmíněnou logiku.

    
    Například následující fragment kódu zkontroluje, jestli je upozornění protokolů Application Insights na základě výstrahy a pokud ano, zobrazí výsledky hledání. V opačném vytiskne "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Další informace o [psát logiku aplikace výrazy](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Další postup

* [Další informace o skupinách akcí](../../azure-monitor/platform/action-groups.md).
* [Další informace o ve společném schématu produktu výstrah](https://aka.ms/commonAlertSchemaDocs).

