---
title: Zavedení integrace stavu – Azure Správce nasazení
description: Popisuje, jak nasadit službu ve více oblastech pomocí Azure Správce nasazení. Zobrazuje bezpečné postupy nasazení, které před zavedením do všech oblastí ověřují stabilitu nasazení.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99536963"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Zavedení integrace stavu do Azure Správce nasazení (Public Preview)

[Azure Správce nasazení](./deployment-manager-overview.md) umožňuje provádět připravené uváděníy prostředků Azure Resource Manager. Prostředky jsou nasazené v oblasti podle oblasti uspořádaným způsobem. Integrovaná kontrolu stavu Azure Správce nasazení můžou monitorovat uvádění a automaticky zastavovat problematické uvádění, abyste mohli řešit problémy a snižovat rozsah dopadu. Tato funkce může snížit nedostupnost služby způsobené regresi v aktualizacích.

## <a name="health-monitoring-providers"></a>Poskytovatelé sledování stavu

Abychom mohli co nejsnáze integrovat stav, společnost Microsoft spolupracuje s některými špičkovými společnostmi sledování stavu služby, které vám poskytnou jednoduché řešení kopírování a vkládání za účelem integrace kontrol stavu s nasazeními. Pokud ještě nepoužíváte monitorování stavu, jedná se o skvělé řešení, která můžete začít s:

| ![Azure monitor – poskytovatel monitorování stavu služby Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![služby Datadog zprostředkovatele monitorování stavu nástroje Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![site24x7 zprostředkovatele monitorování stavu nástroje Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Wavefront zprostředkovatele monitorování stavu nástroje Azure Deployment Manager](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Pro Cloud Native & hybridního monitorování a analýzy je Azure Monitor plná platforma pro pozorování celého zásobníku Microsoftu. |Služby Datadog je špičková platforma pro monitorování a analýzu pro moderní cloudová prostředí. Podívejte [se, jak služby Datadog integruje s Azure Správce nasazení](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, jedno řešení pro monitorování privátních a veřejných cloudových služeb. Podívejte [se, jak Site24x7 integruje s Azure Správce nasazení](https://www.site24x7.com/azure/adm.html).| Wavefront, platforma monitorování a analýzy pro prostředí více cloudových aplikací. Podívejte [se, jak Wavefront integruje s Azure Správce nasazení](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Jak se určuje stav služby

[Poskytovatelé sledování stavu](#health-monitoring-providers) nabízejí několik mechanismů pro služby monitorování a upozorňování na případné problémy se stavem služeb. [Azure monitor](../../azure-monitor/overview.md) je příklad jedné takové nabídky. Azure Monitor lze použít k vytvoření výstrah při překročení určité prahové hodnoty. Například při nasazení nové aktualizace služby dojde k nárůstu využití paměti a procesoru nad rámec očekávaných úrovní. Když se zobrazí oznámení, můžete provést nápravné akce.

Tito poskytovatelé stavu obvykle nabízejí rozhraní REST API, aby bylo možné zkontrolovat stav monitorů služby prostřednictvím kódu programu. Rozhraní REST API se můžou vrátit pomocí jednoduchého pořádku/nesprávného signálu (určeného kódem odpovědi HTTP) a/nebo s podrobnými informacemi o signálech, které přijímá.

Nový `healthCheck` krok ve službě Azure Správce nasazení umožňuje deklarovat kódy HTTP, které označují stav v pořádku. U složitých výsledků REST můžete zadat regulární výrazy, které se při shodě shodují s odezvou v pořádku.

Tok pro nastavení kontrol stavu služby Azure Správce nasazení:

1. Vytvořte monitorování stavu prostřednictvím poskytovatele služby Health Service dle vašeho výběru.
1. Vytvořte jeden nebo více `healthCheck` kroků v rámci zavedení služby Azure Správce nasazení. Do kroků zadejte `healthCheck` následující informace:

    1. Identifikátor URI pro REST API pro monitory stavu (definovaný vaším poskytovatelem služby Health Service).
    1. Informace o ověřování. V současné době je podporováno pouze ověřování stylu klíčů rozhraní API. U Azure Monitor by měl být typ ověřování nastavený jako `RolloutIdentity` uživatelsky přiřazená spravovaná identita, která se používá pro Azure Správce nasazení zavedení rozšíření pro Azure monitor.
    1. [Stavové kódy http](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) nebo regulární výrazy, které definují reakci v pořádku. Můžete zadat regulární výrazy, které se musí shodovat, aby odpověď považovala za v pořádku, nebo můžete poskytnout výrazy, které musí splňovat, aby odpověď byla považována za v pořádku. Obě metody jsou podporovány.

    Následující JSON je příklad, jak integrovat Azure Monitor s Azure Správce nasazení. Příklad používá `RolloutIdentity` a vytváří kontrolu stavu, kde zavedení pokračuje, pokud nejsou k dispozici žádné výstrahy. Jediná podporovaná Azure Monitor rozhraní API: [Alerts – získat vše](/rest/api/monitor/alertsmanagement/alerts/getall).

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

1. `healthCheck`V rámci zavedení Správce nasazení v Azure vyvolejte kroky v příslušném čase. V následujícím příkladu `healthCheck` je vyvolán krok v `postDeploymentSteps` `stepGroup2` .

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

Příklad najdete v tématu [kurz: použití kontroly stavu v Azure Správce nasazení](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Fáze kontroly stavu

V tomto okamžiku Azure Správce nasazení ví, jak zadat dotaz na stav vaší služby a ve kterých fázích zavádění. Azure Správce nasazení ale umožňuje také hloubkovou konfiguraci časování těchto kontrol. `healthCheck`Krok se provádí ve třech sekvenčních fázích, přičemž všechny mají konfigurovatelné doby trvání:

1. Wait

    1. Po dokončení operace nasazení se můžou virtuální počítače restartovat, znovu nakonfigurovat na základě nových dat nebo dokonce spustit poprvé. V některých případech také trvá, než služby začnou vysílat signály stavu, aby je mohl agregovat poskytovatel sledování stavu. Během tohoto procesu tumultuous nemusí být smyslem kontrolovat stav služby, protože aktualizace ještě nedosáhla stabilního stavu. Služba může být ve skutečnosti mezi dobrým a špatným stavem v rámci vyrovnání prostředků.
    1. V průběhu čekací fáze není stav služby monitorovaný. Tato možnost slouží k povolení zanesli nasazených prostředků před zahájením procesu kontroly stavu.

1. Udává

    1. Vzhledem k tomu, že není možné znát ve všech případech, jak dlouho bude trvat, než se prostředky dostanou stabilní, elastická fáze umožní flexibilní časové období mezi tím, kdy jsou prostředky potenciálně nestabilní a kdy jsou požadovány pro udržení dobrého stabilního stavu.
    1. Po zahájení elastické fáze začíná Azure Správce nasazení dotazování poskytnutého koncového bodu REST na stav služby pravidelně. Interval dotazování se dá konfigurovat.
    1. Pokud se monitor stavu vrátí s signály, které signalizují, že služba není v pořádku, tyto signály se ignorují, elastická fáze pokračuje a dotazování pokračuje.
    1. Pokud monitorování stavu vrátí signály indikující, že je služba v pořádku, bude ukončena elastická fáze a stav v pořádku fáze.
    1. Doba trvání pro elastickou fázi tedy představuje maximální dobu, která může být strávená dotazem na stav služby před tím, než je považována za povinná odpověď v pořádku.

1. Stav v pořádku

    1. Během fáze stav v pořádku se stav služby průběžně dotazuje ve stejném intervalu jako elastická fáze.
    1. Očekává se, že služba bude udržovat v pořádku signály z poskytovatele sledování stavu po celou určenou dobu trvání.
    1. Pokud je v jakémkoli okamžiku zjištěna chybná odpověď, služba Azure Správce nasazení zastaví celé zavedení a vrátí odpověď REST, která bude mít nefunkční signály služby.
    1. Po skončení stav v pořádku doby trvání `healthCheck` je dokončeno a nasazení pokračuje k dalšímu kroku.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak integrovat monitorování stavu v Azure Správce nasazení. Přejděte k dalšímu článku a Naučte se, jak nasadit pomocí Správce nasazení.

> [!div class="nextstepaction"]
> [Kurz: použití kontroly stavu v Azure Správce nasazení](./deployment-manager-tutorial-health-check.md)
