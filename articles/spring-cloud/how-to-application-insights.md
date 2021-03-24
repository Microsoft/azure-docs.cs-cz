---
title: Jak používat Application Insights agenta Java In-Process v Azure jaře cloudu
description: Jak monitorovat aplikace a mikroslužby pomocí Application Insights agenta Java In-Process v Azure jarním cloudu.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: f6f1ed1a3e09397a720dfd4d842b79cd88aa738d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877929"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights agenta Java In-Process v Azure jaře cloudu (Preview)

Tento dokument vysvětluje, jak monitorovat aplikace a mikroslužby pomocí Application Insightsho agenta Java v Azure jarním cloudu. 

Pomocí této funkce můžete:

* Vyhledávejte data trasování pomocí různých filtrů.
* Zobrazit mapu závislostí mikroslužeb.
* Ověřte výkon žádosti.
* Monitorujte živé metriky v reálném čase.
* Ověřte selhání žádosti.
* Ověřte metriky aplikace.

Application Insights poskytují mnoho pozorovatelných perspektiv, včetně:

* Mapa aplikace
* Výkon
* Selhání
* Metriky
* Live Metrics
* Dostupnost

## <a name="enable-java-in-process-agent-for-application-insights"></a>Povolit pro Application Insights agenta Java In-Process

Pomocí následujícího postupu povolte funkci Java In-Process Agent verze Preview.

1. Přejít na stránku Přehled služby instance služby.
2. V okně monitorování klikněte na položku **Application Insights** .
3. Kliknutím na tlačítko **povolit Application Insights** povolíte integraci **Application Insights** .
4. Vyberte existující instanci Application Insights nebo vytvořte novou.
5. Kuřat povolení funkce agenta Java in-Process agent **v jazyce Java** Tady můžete také přizpůsobit vzorkovací frekvenci z 0 na 100.
6.  Kliknutím na **Uložit** uložte změnu.

## <a name="portal"></a>Portál

1. Přejít ke **službě | Přehled** a vyberte **Application Insights** v části **monitorování** . 
2. Kliknutím na **povolit Application Insights** povolíte Application Insights ve jarním cloudu Azure.
3. Kliknutím na **Povolit vnitroprocesového agenta Java** povolíte funkci Java IPA ve verzi Preview. Když je povolená funkce IPA ve verzi Preview, můžete nakonfigurovat jednu volitelnou vzorkovací frekvenci (výchozí 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Použití funkce Application Insights

Když je povolená funkce **Application Insights** , můžete:

V levém navigačním podokně kliknutím na **Application Insights** přejděte na stránku **Přehled** Application Insights. 

* Kliknutím na **Mapa aplikace** zobrazíte stav volání mezi aplikacemi.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Kliknutím na odkaz mezi zákazníky – služba a `petclinic` zobrazíte další podrobnosti, jako je dotaz z SQL.

* V levém navigačním podokně klikněte na **výkon** , abyste viděli údaje o výkonu pro všechny aplikace a také závislosti a role.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* V levém navigačním podokně klikněte na **selhání** a podívejte se, jestli nedošlo k neočekávanému využití vašich aplikací.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* V levém navigačním podokně klikněte na **metriky** a vyberte obor názvů, v případě potřeby uvidíte jak metriky spuštění, tak i vlastní metriky.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* V levém navigačním podokně klikněte na **živá metriky** , abyste viděli metriky v reálném čase pro různé dimenze.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* V levém navigačním podokně klikněte na **dostupnost** a sledujte dostupnost a odezvu webových aplikací tím, že se vytvoří [testy dostupnosti v Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Šablona ARM
Chcete-li použít šablonu Azure Resource Manager, zkopírujte následující obsah do `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>Rozhraní příkazového řádku
Použijte šablonu ARM pomocí příkazu CLI:

* Pro existující instanci cloudu pro Azure jaře:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Pro nově vytvořenou instanci cloudu Azure pro jaře:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Postup zakázání aplikace – přehled:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Viz také
* [Použití distribuovaného trasování u jarního cloudu Azure](spring-cloud-howto-distributed-tracing.md)
* [Analýza protokolů a metrik](diagnostic-services.md)
* [Streamování protokolů v reálném čase](spring-cloud-howto-log-streaming.md)