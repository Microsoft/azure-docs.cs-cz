---
title: Azure Service Fabric - nastavení monitorování s agentem OMS | Microsoft Docs
description: Zjistěte, jak nastavit agenta OMS pro monitorování kontejnery a čítače výkonu pro Azure Service Fabric clusterů.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: 4c4095071235dac7e8be3c16b614bdfa5b706a1c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="add-the-oms-agent-to-a-cluster"></a>Přidat agenta OMS do clusteru

Tento článek popisuje kroky pro přidání agenta OMS jako sady škálování virtuálního počítače rozšíření do clusteru a připojte ho k vaší existující pracovní prostor analýzy protokolů Azure. To umožňuje shromažďování dat diagnostiky o kontejnery, aplikací a monitorování výkonu. Jej přidat jako rozšíření Azure Resource Manager zajistí, že ho získá nainstalovat na každý uzel, i když škálování clusteru.

> [!NOTE]
> Tento článek předpokládá, že máte k pracovnímu prostoru analýzy protokolů Azure už nastavili. Pokud ho použít nechcete, přejděte na [nastavení analýzy protokolů Azure](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Přidat agenta rozšíření prostřednictvím rozhraní příkazového řádku Azure

Nejlepší způsob, jak přidat agenta OMS ke clusteru prostřednictvím škálování virtuálních počítačů nastavena dostupných rozhraní API pomocí Azure CLI. Pokud jste ještě nastavení rozhraní příkazového řádku Azure, přejděte na portál Azure a otevře [cloudové prostředí](../cloud-shell/overview.md) instanci, nebo [nainstalovat Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Jakmile se vyžaduje vaše cloudové prostředí, zkontrolujte, zda že pracujete v rámci stejného předplatného jako prostředku. Zkontrolovat to s `az account show` a zajistěte, aby hodnota "název" odpovídá předplatného vašeho clusteru.

2. Na portálu přejděte do skupiny prostředků, kde je umístěn pracovní prostor analýzy protokolů. Klikněte na tlačítko do analýzy protokolů prostředku (typ prostředku bude analýzy protokolů), na pravé straně, posuňte se dolů a klikněte na **vlastnosti**.

    ![Stránka vlastností analýzy protokolů](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    Poznamenejte si vaše `workspaceId`. 

3. Budete také potřebovat vaše `workspaceKey` nasazení agenta nástroje. Získat klíč je, klikněte na **Upřesnit nastavení**v části *nastavení* části levé navigaci. Klikněte na **servery Windows** Pokud jsou stálého do clusteru se systémem Windows, a **servery se systémem Linux** při vytváření clusteru s podporou systému Linux. Budete potřebovat *primární klíč* který objeví nasadit agenty, jako `workspaceKey`.

4. Spusťte příkaz pro instalaci agenta OMS na cluster, pomocí `vmss extension set` rozhraní API ve své cloudové prostředí:

    Pro cluster s podporou Windows:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Pro cluster s podporou Linux:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Tady je příklad OMS agenta, který se přidává do clusteru se systémem Windows.

    ![Příkaz rozhraní příkazového řádku agenta OMS](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Spusťte příkaz pro použití této konfigurace vaše instance virtuálních počítačů, které již existují:  


    ```sh
    az vmss update-instances
    ```

Je třeba vzít méně než 15 minut úspěšně přidat agenta do uzlů. Můžete ověřit, že byly přidány agenty pomocí `az vmss extension list` rozhraní API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Přidat agenta pomocí šablony Resource Manageru

Ukázka Resource Manager šablony, které nasazení k pracovnímu prostoru analýzy protokolů Azure a přidat agenta na každý z uzlů je k dispozici pro [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) nebo [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Můžete stáhnout a upravit tato šablona nasazení clusteru, který nejlépe vyhovuje vašim potřebám.

## <a name="next-steps"></a>Další kroky

* Shromažďovat relevantní [čítače výkonu](service-fabric-diagnostics-event-generation-perf.md). Konfigurace agenta OMS ke shromažďování konkrétních čítačích výkonu, zkontrolujte [konfigurace zdroje dat](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurace analýzy protokolů nastavit [automatizované výstrahy](../log-analytics/log-analytics-alerts.md) které pomáhají při zjišťování a Diagnostika
