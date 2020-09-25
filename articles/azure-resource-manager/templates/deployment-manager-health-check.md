---
title: Zavedení integrace stavu – Azure Deployment Manager
description: Popisuje, jak nasadit službu ve více oblastech pomocí Azure Deployment Manager. Zobrazuje bezpečné postupy nasazení, které před zavedením do všech oblastí ověřují stabilitu nasazení.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: a6925ef8f72615cc3868c8b5cd4ea030ed3c3c40
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91278055"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Zavedení integrace stavu do Azure Deployment Manager (Public Preview)

[Azure Deployment Manager](./deployment-manager-overview.md) umožňuje provádět připravené uváděníy prostředků Azure Resource Manager. Prostředky jsou nasazené v oblasti podle oblasti uspořádaným způsobem. Integrovaná kontrolu stavu Azure Deployment Manager můžou monitorovat uvádění a automaticky zastavovat problematické uvádění, abyste mohli řešit problémy a snižovat rozsah dopadu. Tato funkce může snížit nedostupnost služby způsobené regresi v aktualizacích.

## <a name="health-monitoring-providers"></a>Poskytovatelé sledování stavu

Abychom mohli co nejsnáze integrovat stav, společnost Microsoft spolupracuje s některými špičkovými společnostmi sledování stavu služby, které vám poskytnou jednoduché řešení kopírování a vkládání za účelem integrace kontrol stavu s nasazeními. Pokud ještě nepoužíváte monitorování stavu, jedná se o skvělé řešení, která můžete začít s:

| ![Azure monitor – poskytovatel monitorování stavu služby Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![služby Datadog zprostředkovatele monitorování stavu nástroje Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![site24x7 zprostředkovatele monitorování stavu nástroje Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Wavefront zprostředkovatele monitorování stavu nástroje Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Pro Cloud Native & hybridního monitorování a analýzy je Azure Monitor plná platforma pro pozorování celého zásobníku Microsoftu. |Služby Datadog je špičková platforma pro monitorování a analýzu pro moderní cloudová prostředí. Podívejte [se, jak služby Datadog integruje s Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, jedno řešení pro monitorování privátních a veřejných cloudových služeb. Podívejte [se, jak Site24x7 integruje s Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, platforma monitorování a analýzy pro prostředí více cloudových aplikací. Podívejte [se, jak Wavefront integruje s Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Jak se určuje stav služby

[Poskytovatelé sledování stavu](#health-monitoring-providers) nabízejí několik mechanismů pro služby monitorování a upozorňování na případné problémy se stavem služeb. [Azure monitor](../../azure-monitor/overview.md) je příklad jedné takové nabídky. Azure Monitor lze použít k vytvoření výstrah při překročení určité prahové hodnoty. Například při nasazení nové aktualizace služby dojde k nárůstu využití paměti a procesoru nad rámec očekávaných úrovní. Když se zobrazí oznámení, můžete provést nápravné akce.

Tito poskytovatelé stavu obvykle nabízejí rozhraní REST API, aby bylo možné zkontrolovat stav monitorů služby prostřednictvím kódu programu. Rozhraní REST API se můžou vrátit pomocí jednoduchého pořádku/nesprávného signálu (určeného kódem odpovědi HTTP) a/nebo s podrobnými informacemi o signálech, které přijímá.

Nový krok *healthCheck* v Azure Deployment Manager umožňuje deklarovat kódy HTTP, které indikují v pořádku službu, nebo pro SLOŽITĚJŠÍ výsledky REST můžete dokonce zadat regulární výrazy, které, pokud se shodují, a označit v pořádku reakci.

Tok pro získání instalace pomocí kontrol stavu služby Azure Deployment Manager:

1. Vytvořte monitorování stavu prostřednictvím poskytovatele služby Health Service dle vašeho výběru.
1. Vytvořte jeden nebo více kroků healthCheck jako součást zavedení služby Azure Deployment Manager. Do kroků healthCheck zadejte následující informace:

    1. Identifikátor URI pro REST API pro monitory stavu (definovaný vaším poskytovatelem služby Health Service).
    1. Informace o ověřování. V současné době je podporováno pouze ověřování stylu klíčů rozhraní API. U Azure Monitor by měl být typ ověřování nastaven jako – "RolloutIdentity", protože spravovaná identita přiřazená uživateli, která se používá pro Azure Deployment Manager zavedení, pro Azure Monitor.
    1. [Stavové kódy http](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) nebo regulární výrazy, které definují reakci v pořádku. Všimněte si, že můžete zadat regulární výrazy, které se musí shodovat, aby odpověď považovala za v pořádku, nebo můžete poskytnout výrazy, které musí splňovat, aby odpověď byla považována za v dobrém stavu. Obě metody jsou podporovány.

    Následující JSON je příklad pro integraci Azure Monitor se službou Azure Deployment Manager, která využívá RolloutIdentity a vytváří kontrolu stavu, kde zavedení pokračuje, pokud nejsou k dispozici žádné výstrahy. Jediná podporovaná Azure Monitor rozhraní API: [Alerts – získat vše](/rest/api/monitor/alertsmanagement/alerts/getall.md).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
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
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    Následující JSON je příklad pro všechny ostatní poskytovatele monitorování stavu:

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

1. Vyvolejte kroky healthCheck ve vhodné době při zavádění služby Azure Deployment Manager. V následujícím příkladu je krok kontroly stavu vyvolán v **postDeploymentSteps** z **stepGroup2**.

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

Příklad najdete v tématu [kurz: použití kontroly stavu v Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fáze kontroly stavu

V tomto okamžiku Azure Deployment Manager ví, jak zadat dotaz na stav vaší služby a v jaké fázi zavádění. Azure Deployment Manager ale umožňuje také hloubkovou konfiguraci časování těchto kontrol. HealthCheck krok se provádí ve 3 sekvenčních fázích, přičemž všechny mají konfigurovatelné doby trvání:

1. Wait

    1. Po dokončení operace nasazení se můžou virtuální počítače restartovat, znovu nakonfigurovat na základě nových dat nebo dokonce spustit poprvé. V některých případech také trvá, než služby začnou vysílat signály stavu, aby je mohl agregovat poskytovatel sledování stavu. Během tohoto procesu tumultuous nemusí být smyslem kontrolovat stav služby, protože aktualizace ještě nedosáhla stabilního stavu. Služba může být ve skutečnosti mezi dobrým a špatným stavem v rámci vyrovnání prostředků.
    1. Během čekací fáze není stav služby monitorován. Tato možnost slouží k povolení zanesli nasazených prostředků před zahájením procesu kontroly stavu.
1. Udává

    1. Vzhledem k tomu, že ve všech případech nemůžete znát, jak dlouho budou prostředky trvat zanesli, než se budou stabilní, elastická fáze umožní flexibilní časové období mezi tím, kdy jsou prostředky potenciálně nestabilní a kdy jsou potřeba k udržení dobrého stabilního stavu.
    1. Po zahájení elastické fáze začíná Azure Deployment Manager dotazování poskytnutého koncového bodu REST na stav služby pravidelně. Interval dotazování se dá konfigurovat.
    1. Pokud se monitor stavu vrátí s signály, které signalizují, že služba není v pořádku, tyto signály se ignorují, elastická fáze pokračuje a dotazování pokračuje.
    1. Jakmile se monitor stavu vrátí se signály, které signalizují, že je služba v pořádku, ukončí se elastická fáze a spustí se fáze stav v pořádku.
    1. Doba trvání pro elastickou fázi tedy představuje maximální dobu, která může být strávená dotazem na stav služby před tím, než je považována za povinná odpověď v pořádku.
1. Stav v pořádku

    1. Během fáze stav v pořádku se stav služby průběžně dotazuje ve stejném intervalu jako elastická fáze.
    1. Očekává se, že služba bude udržovat v pořádku signály z poskytovatele sledování stavu po celou určenou dobu trvání.
    1. Pokud je v jakémkoli okamžiku zjištěna chybná odpověď, služba Azure Deployment Manager zastaví celé zavedení a vrátí odpověď REST, která bude mít nefunkční signály služby.
    1. Po skončení stav v pořádku doby trvání se healthCheck dokončí a nasazení pokračuje dalším krokem.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak integrovat monitorování stavu v Azure Deployment Manager. Přejděte k dalšímu článku a Naučte se, jak nasadit pomocí Deployment Manager.

> [!div class="nextstepaction"]
> [Kurz: integrace kontroly stavu v Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)