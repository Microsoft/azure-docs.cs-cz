---
title: Zavést stavu zavedení integrace pro Azure Deployment Manager
description: Popisuje postup nasazení služby v mnoha oblastech Azure Deployment Manager. Zobrazuje postupy bezpečného nasazení, aby se ověřilo nasazení před zavedením do všech oblastí.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 41b16498fb79166b2c77c77a517ee5c443ebec75
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796257"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Zavést stavu zavedení integrace pro Azure Deployment Manager (Public preview)

[Azure Deployment Manager](./deployment-manager-overview.md) umožňuje provádět postupné uvádění prostředky Azure Resource Manageru. Se prostředky nasadí regiony seřazený způsobem. Kontrola stavu integrované nástroje Azure Deployment Manager můžete monitorovat uvedení a automaticky zastavit problematické uvedení, tak, aby můžete řešení potíží a snížení škálování dopad. Tato funkce může snížit způsobeno Regrese v aktualizacích nedostupnosti služby.

## <a name="health-monitoring-providers"></a>Poskytovatelé sledování stavu

Aby integrace stavu co nejjednodušší, Microsoft má pracovali s některými monitorování společnosti, kde přinášejí jednoduché kopírování/vkládání řešení pro integraci kontroly stavu s nasazeními hlavní služby stavu. Pokud ještě není při použití monitor stavu, jsou skvělé řešení začít:

| ![nasazení Azure správce stavu monitorování poskytovatele služby datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![nasazení Azure správce stavu monitorování poskytovatele site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![nasazení Azure správce stavu monitorování poskytovatele wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Služby Datadog, přední monitorování a analytické platformy pro moderní cloudové prostředí. Zobrazit [jak služby Datadog se dá integrovat s Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, vše v jednom soukromými a veřejnými cloudovými služby řešení pro monitorování. Zobrazit [jak Site24x7 se dá integrovat s Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, monitorování a analýzy platformy pro prostředí více cloudových aplikací. Zobrazit [jak Wavefront se dá integrovat s Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Jak určit stav služby

[Stav monitorování poskytovatelů](#health-monitoring-providers) nabízí několik mechanismů pro sledování služeb a upozorní vás žádné problémy se stavem služby. [Azure Monitor](../azure-monitor/overview.md) je příkladem jednoho nabídkou. Azure Monitor umožňuje vytvářet upozornění při překročení určité hranice. Například paměť a využití procesoru dosahuje mimo očekávané úrovně při nasazení nové aktualizace ke službě. Upozornění, můžete provést opravné akce.

Tito poskytovatelé zdravotní běžně nabízí rozhraní REST API, tak, aby stav monitorování vaší služby se dají prozkoumat prostřednictvím kódu programu. Rozhraní REST API, můžete buď vrátit s jednoduchou v pořádku a není v pořádku signál (podle kódu odpovědi HTTP) a/nebo s podrobnými informacemi o signály, že přijímá.

Nové *healthCheck* krok v Azure Deployment Manager umožňuje deklarovat kódy HTTP, které označují službu stavu, nebo pro složitější REST výsledky, můžete dokonce určit regulární výrazy, označující, pokud se shodují, v dobrém stavu odpověď.

Tento tok získání nastavení pomocí kontroly stavu Azure Deployment Manager:

1. Vytvoření stavu monitorů prostřednictvím poskytovatele služeb stavu podle svého výběru.
1. Vytvořte jeden nebo více kroků healthCheck jako součást procesu zavádění řešení Azure Deployment Manager. Zadejte kroky healthCheck s následujícími informacemi:

    1. Identifikátor URI pro rozhraní REST API pro monitory stavu (jak jsou definovány ve zprostředkovateli služby stavu).
    1. Informace o ověřování. Aktuálně se podporuje jenom ověřování styl klíč API-key.
    1. [Stavové kódy HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) nebo regulárních výrazů, které definují v dobrém stavu odpovědi. Všimněte si, že můžete zadat regulární výrazy, které musí všechny odpovídající odpověď považovat v pořádku, nebo může poskytovat výrazy, z nichž některé musí odpovídat odpovědi považovat v pořádku. Obě metody jsou podporovány.

    Následující kód Json je příklad:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Vyvolání healthCheck kroky v příslušnou dobu v procesu zavádění řešení Azure Deployment Manager. V následujícím příkladu, je vyvolána kroku kontroly stavu v **postDeploymentSteps** z **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Chcete-li si příklad, přečtěte si téma [kurzu: Použít kontrolu v nástroji Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fáze kontroly stavu

V tuto chvíli Azure Deployment Manager ví, jak zadávat dotazy na stav služby a na co fáze zaváděním Uděláte to tak. Ale Azure Deployment Manager také umožňuje konfigurovat podrobné načasování těchto kontrol. Krok healthCheck je proveden v 3 sekvenční fází, které mají všechny konfigurovatelné doby trvání: 

1. Čekání

    1. Po dokončení operace nasazení může být restartování virtuálních počítačů, opětovná konfigurace na základě nová data, nebo dokonce se spouští poprvé. Přijímá také čas pro služby generování signálů stavu mají agregovat do něco užitečné monitorování zprostředkovatele stavu spuštění. Během tohoto procesu tumultuous nemusí mít smysl ke kontrole služby service health, protože aktualizace nebyla ještě nedostaly do stabilního stavu. Služba může ve skutečnosti oscilační mezi stavy funkčních a nefunkčních jako vyrovnat prostředky. 
    1. Během fáze čekání není monitorovat stav služby. To slouží k povolení nasazených prostředků čas vytvoření před zahájením procesu kontroly stavu. 
1. Elastická

    1. Protože je možné zjistit ve všech případech, jak dlouho bude trvat zanést dřív, než narostou stabilní, Elastic fáze umožňuje flexibilní období mezi Pokud jsou potenciálně nestabilních prostředky, a když jsou potřeba udržovat fungujícím ustáleného prostředky stav.
    1. Po zahájení elastické fáze Azure Deployment Manager začíná pravidelně dotazování poskytnutý koncový bod REST pro službu service health. Interval cyklického dotazování je možné konfigurovat. 
    1. Pokud monitorování stavu se vrátí zpět s signály označující, že je tato služba není v pořádku, tyto signály jsou ignorovány, elastické fáze pokračuje a pokračuje cyklického dotazování. 
    1. Jakmile monitorování stavu zpět součástí signály označující, zda je služba v pořádku, Elastic fáze skončí a fáze HealthyState začíná. 
    1. Doba trvání zadaný pro elastické fáze tedy maximální množství času, které lze využít při dotazování na stav služby předtím, než v dobrém stavu odpovědi je považován za povinný. 
1. HealthyState

    1. Během fáze HealthyState stavu služby neustále dotazovat na stejném intervalu jako elastické fáze. 
    1. Služba má udržovat v poskytovateli monitorování stavu v pořádku signály celý během určené doby. 
    1. Pokud kdykoli se detekuje odpověď není v pořádku, zastaví celý zavedení Azure Deployment Manager a vrátit odpověď REST provádění signály služby není v pořádku.
    1. Po dobu trvání HealthyState skončila, healthCheck dokončení a nasazení pokračuje k dalšímu kroku.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o tom, jak integrovat monitorování stavu v Azure Deployment Manager. Přejděte k dalším článku se dozvíte, jak nasadit pomocí nástroje Deployment Manager.

> [!div class="nextstepaction"]
> [Kurz: integrace kontrolu v nástroji Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)