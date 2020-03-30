---
title: Zavedení integrace stavu – Správce nasazení Azure
description: Popisuje, jak nasadit službu v mnoha oblastech pomocí Azure Deployment Manager. Zobrazuje postupy bezpečného nasazení k ověření stability vašeho nasazení před zavedením do všech oblastí.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273797"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Zavedení integrace stavu do Správce nasazení Azure (Veřejná verze preview)

[Azure Deployment Manager](./deployment-manager-overview.md) umožňuje provádět postupné zavádění prostředků Azure Resource Manager. Prostředky jsou nasazeny oblast podle oblasti v seřazené způsobem. Integrovaná kontrola stavu Správce nasazení Azure může monitorovat zavádění a automaticky zastavit problematické zavádění, takže můžete řešit potíže a snížit rozsah dopadu. Tato funkce může snížit nedostupnost služby způsobenou regresí v aktualizacích.

## <a name="health-monitoring-providers"></a>Poskytovatelé monitorování stavu

Aby byla integrace stavu co nejjednodušší, společnost Microsoft spolupracuje s některými špičkovými společnostmi pro sledování stavu služeb, aby vám poskytla jednoduché řešení kopírování a vkládání pro integraci kontrol stavu s vašimi nasazeními. Pokud ještě nepoužíváte monitor stavu, jedná se o skvělá řešení, která můžete začít:

| ![azure deployment manager health monitor provider datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure deployment manager health monitor provider site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![Azure Deployment Manager health monitor poskytovatele wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, přední platforma pro monitorování a analýzu moderních cloudových prostředí. Podívejte se, jak se [Datadog integruje se Správcem nasazení Azure](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, all-in-one soukromé a veřejné cloudové služby monitorovací řešení. Podívejte [se, jak se Site24x7 integruje se Správcem nasazení Azure](https://www.site24x7.com/azure/adm.html).| Wavefront, platforma pro monitorování a analýzu pro vícecloudová aplikační prostředí. Podívejte [se, jak se Wavefront integruje se Správcem nasazení Azure](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Jak je určen stav služby

[Poskytovatelé monitorování stavu](#health-monitoring-providers) nabízejí několik mechanismů pro sledování služeb a upozorňování na jakékoli problémy se stavem služby. [Azure Monitor](../../azure-monitor/overview.md) je příkladem jedné takové nabídky. Azure Monitor se dá použít k vytvoření výstrah, když jsou překročeny určité prahové hodnoty. Například využití paměti a procesoru špička nad očekávané úrovně při nasazení nové aktualizace do služby. Když budete upozorněni, můžete provést nápravná opatření.

Tito poskytovatelé stavu obvykle nabízejí rest API tak, aby stav monitorů vaší služby lze zkoumat programově. REST API můžete buď vrátit s jednoduchým v pořádku/není v pořádku signál (určena kód odpovědi HTTP) a/nebo s podrobnými informacemi o signály, které přijímá.

Nový krok *healthCheck* ve Správci nasazení Azure umožňuje deklarovat kódy HTTP, které označují službu v pořádku, nebo pro složitější výsledky REST můžete dokonce zadat regulární výrazy, které, pokud se shodují, označují odpověď v pořádku.

Tok k získání nastavení pomocí kontrolstavu Správce nasazení Azure:

1. Vytvořte si monitory stavu prostřednictvím poskytovatele zdravotních služeb podle vašeho výběru.
1. Vytvořte jeden nebo více kroků stavuZkontrolujtejte kroky jako součást zavedení Azure Deployment Manageru. Vyplňte kroky stavuZkontrolujte následujícími informacemi:

    1. Identifikátor URI pro rozhraní REST API pro monitorování stavu (podle definice poskytovatele služeb zdravotní ho d).
    1. Ověřovací informace. V současné době je podporováno pouze ověřování stylu klíče rozhraní API.
    1. [Kódy stavu HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) nebo regulární výrazy, které definují odpověď v pořádku. Všimněte si, že můžete poskytnout regulární výrazy, které all musí odpovídat odpovědi, které mají být považovány za v pořádku, nebo můžete poskytnout výrazy, které any musí odpovídat pro odpověď považovat za v pořádku. Obě metody jsou podporovány.

    Následující Json je příkladem:

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

1. Vyvolat kroky stavuKontrola ve vhodnou dobu ve vašem Azure Deployment Manager zavedení. V následujícím příkladu je v **postDeploymentSteps of** **stepGroup2**vyvolán krok kontroly stavu.

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

Příklad najdete v [tématu Kurz: Použití kontroly stavu ve Správci nasazení Azure](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fáze kontroly stavu

V tomto okamžiku Azure Deployment Manager ví, jak dotaz na stav vaší služby a v jakých fázích v zavádění k tomu. Azure Deployment Manager však také umožňuje hluboké konfigurace časování těchto kontrol. Krok healthCheck se provádí ve 3 sekvenčních fázích, z nichž všechny mají konfigurovatelné doby trvání: 

1. Wait

    1. Po dokončení operace nasazení virtuálních počítače může být restartování, rekonfigurace na základě nových dat, nebo dokonce spuštění poprvé. Také trvá nějakou dobu, než služby začnou vyzařovat signály stavu, které mají být agregovány poskytovatelem monitorování stavu do něčeho užitečného. Během tohoto bouřlivého procesu nemusí mít smysl kontrolovat stav služby, protože aktualizace ještě nedosáhla ustáleného stavu. Služba může být oscilující mezi stavy v pořádku a nezdravé jako prostředky usadit. 
    1. Během fáze čekání není sledován stav služby. To se používá k povolení nasazených prostředků čas péct před zahájením procesu kontroly stavu. 
1. Elastické

    1. Vzhledem k tomu, že není možné ve všech případech vědět, jak dlouho bude trvat pečení zdrojů, než se stanou stabilními, elastická fáze umožňuje pružné časové období mezi tím, kdy jsou zdroje potenciálně nestabilní, a kdy jsou potřebné k udržení zdravé hospo- Státu.
    1. Když začne elastická fáze, Azure Deployment Manager začne dotazování poskytovaného koncového bodu REST pro stav služby pravidelně. Interval dotazování je konfigurovatelný. 
    1. Pokud monitorování stavu vrátí se signály označující, že služba není v pořádku, tyto signály jsou ignorovány, elastická fáze pokračuje a dotazování pokračuje. 
    1. Jakmile monitorování stavu vrátí se signály označující, že služba je v pořádku, elastická fáze končí a HealthyState fáze začíná. 
    1. Doba trvání zadaná pro elastickou fázi je tedy maximální množství času, které lze strávit dotazování pro stav služby před v pořádku odpověď je považovánza povinný. 
1. HealthyState

    1. Během fáze HealthyState stav služby je neustále dotazovánve stejném intervalu jako elastická fáze. 
    1. Očekává se, že služba bude udržovat signály v pořádku od poskytovatele monitorování stavu po celou zadanou dobu. 
    1. Pokud je v libovolném okamžiku zjištěna nefunkční odpověď, Azure Deployment Manager zastaví celé zavedení a vrátí odpověď REST nesoucí signály služby není v pořádku.
    1. Po ukončení doby trvání HealthyState je kontrola stavu dokončena a nasazení pokračuje k dalšímu kroku.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o tom, jak integrovat monitorování stavu ve Správci nasazení Azure. Přejděte k dalšímu článku a zjistěte, jak nasadit pomocí Správce nasazení.

> [!div class="nextstepaction"]
> [Kurz: integrace kontroly stavu ve Správci nasazení Azure](./deployment-manager-tutorial-health-check.md)