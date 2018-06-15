---
title: Hodnocení aplikace Service Fabric pomocí portálu Azure Log Analytics | Microsoft Docs
description: Můžete vytvořit řešení Service Fabric v Log Analytics pomocí portálu Azure k vyhodnocení rizik a stavu Service Fabric aplikací, micro-services, uzly a clustery.
services: log-analytics
documentationcenter: ''
author: niniikhena
manager: jochan
editor: ''
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nini
ms.openlocfilehash: 10c5394935c59b21139c0e141c348ce20a7c0800
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28932677"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Hodnocení aplikace Service Fabric a micro služby pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Symbol Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Tento článek popisuje, jak používat řešení Service Fabric v analýzy protokolů pro identifikaci a řešení potíží s napříč cluster Service Fabric.

Service Fabric řešení používá Azure Diagnostics data z virtuálních počítačů služby infrastruktury, shromažďováním těchto dat z Azure WAD tabulek. Analýzy protokolů potom načte události framework Service Fabric, včetně **spolehlivé události služby**, **události objektu Actor**, **provozní události**, a **vlastní Události trasování událostí**. Pomocí řídicího panelu řešení budete moci zobrazit významné problémy a relevantní události ve vašem prostředí Service Fabric.

Chcete-li začít pracovat s řešením, cluster Service Fabric připojit k pracovní prostor analýzy protokolů. Tady jsou tři scénáře, které je třeba zvážit:

1. Pokud jste nenasadili cluster Service Fabric, postupujte podle kroků v ***nasadit Service Fabric Cluster připojené k pracovní prostor analýzy protokolů*** k nasazení do nového clusteru a konfigurace. do sestavy k analýze protokolů.
2. Pokud potřebujete shromáždit čítače výkonu z hostitele tak, aby pomocí jiných řešení pro správu jako je například zabezpečení na váš Cluster Service Fabric, postupujte podle kroků v ***nasadit Service Fabric Cluster připojené k pracovní prostor analýzy protokolů pomocí rozšíření virtuálního počítače nainstalovat.***
3. Pokud už jste nasadili cluster Service Fabric a chcete se připojit k analýze protokolů, postupujte podle kroků v ***přidání stávající účet úložiště k analýze protokolů.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Nasaďte Cluster Service Fabric, připojení k pracovní prostor analýzy protokolů.
Tato šablona provede následující akce:

1. Nasazení clusteru služby Azure Service Fabric už připojená k pracovnímu prostoru analýzy protokolů. Máte možnost vytvořit nový pracovní prostor při nasazení šablony, nebo zadejte název stávající pracovní prostor analýzy protokolů.
2. Účet úložiště diagnostiky přidá do pracovního prostoru analýzy protokolů.
3. Umožňuje řešení Service Fabric v pracovním prostoru analýzy protokolů.

[![Nasazení do Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Jakmile vyberete výše uvedené tlačítko nasadit, portálu Azure se otevře s parametry, je možné upravit. Ujistěte se, že vytvoříte novou skupinu prostředků, pokud zadejte nový název pracovního prostoru analýzy protokolů:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Přijměte právní podmínky a klikněte na tlačítko **vytvořit** ke spuštění nasazení. Po dokončení nasazení se měly zobrazit nový pracovní prostor a vytvoření clusteru a WADServiceFabric * událostí, WADWindowsEventLogs a WADETWEvent tabulek, které jsou přidány:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Nasaďte Cluster Service Fabric, připojení k pracovní prostor analýzy protokolů se nainstalovat rozšíření virtuálního počítače.

Tato šablona provede následující akce:

1. Nasazení clusteru služby Azure Service Fabric už připojená k pracovnímu prostoru analýzy protokolů. Můžete vytvořit nový pracovní prostor nebo použijte existující.
2. Účty úložiště diagnostiky přidá do pracovního prostoru analýzy protokolů.
3. Umožňuje řešení Service Fabric v pracovním prostoru analýzy protokolů.
4. Instalaci rozšíření agenta MMA v jednotlivých sad v clusteru Service Fabric škálování virtuálního počítače. Instalaci agenta MMA budete moci zobrazovat metrika výkonu o uzly.

[![Nasazení do Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Stejný postup výše zadejte potřebné parametry a ji nasazení. Měli byste vidět znovu nový pracovní prostor, clusteru a všechny vytvořené WAD tabulky:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Zobrazení dat výkonu

Zobrazení dat výkonu z uzlů:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Spusťte pracovní prostor analýzy protokolů z portálu Azure.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- V levém podokně přejděte do nastavení a vyberte Data >> čítačů výkonu systému Windows >> "Přidat vybrané čítače výkonu": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- V hledání protokolů použijte následující dotazy na pustíte do klíčových metrik týkajících se uzly:

    a. Za poslední hodinu uzlů, které jsou problémy s a v jaké časovém intervalu uzlu měl Špička porovnejte průměrné využití procesoru pro všechny uzly:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Zobrazit podobné spojnicových grafů pro dostupná paměť na každý uzel k tomuto dotazu:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Chcete-li zobrazit seznam všech uzlech, zobrazující přesný průměrnou hodnotu pro počet MB k dispozici pro každý uzel, použijte tento dotaz:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. V případě, že chcete rozbalit konkrétním uzlu tak, že prověří hodinové průměr, minimální, maximální a 75 percentilu využití procesoru budete moci provést pomocí tohoto dotazu (nahraďte pole počítače):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Další informace o metriky výkonu v analýzy protokolů v [Operations Management Suite blog](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Přidání stávající účet úložiště k analýze protokolů

Tato šablona jednoduše přidá existující účty úložiště do nového nebo stávajícího pracovního prostoru analýzy protokolů.

[![Nasazení do Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Výběr skupiny prostředků, pokud pracujete s již existující pracovní prostor analýzy protokolů, vyberte "Použití existující" a vyhledávání pro skupinu prostředků obsahující pracovní prostor analýzy protokolů. Vytvořit nový jeden Pokud jinak.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Po nasazení této šablony, nebudete moci zobrazit účet úložiště, které jsou připojené k pracovní prostor analýzy protokolů. V tomto případě přidaný do pracovního prostoru Exchange, kterou jste vytvořili výše jeden další účet úložiště.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Zobrazit události Service Fabric

Po dokončení nasazení a řešení Service Fabric povolen v pracovním prostoru, vybrat **Service Fabric** dlaždici na portálu analýzy protokolů a spusťte Service Fabric řídicího panelu. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí top 10 události podle počtu odpovídající kritériím tento sloupec pro zadaný časový rozsah. Můžete spustit hledání protokolu, které poskytuje celý seznam kliknutím **zobrazit všechny** v pravé dolní jednotlivých sloupců, nebo kliknutím na záhlaví sloupce.

| **Události Service Fabric** | **Popis** |
| --- | --- |
| Významné problémy |Zobrazí problémy, jako je RunAsyncFailures RunAsynCancellations a seznamy uzlu. |
| Provozní události |Významné provozní události, jako je například upgradu aplikace a nasazení. |
| Události spolehlivé služby |Události významné spolehlivé služby takové Runasyncinvocations. |
| Události objektu actor |Významné objektu actor události vygenerované modulem vaší micro služby, jako je výjimky vyvolané metodu objektu actor, objektu actor aktivací a deaktivací a tak dále. |
| Události aplikace |Všechny vlastní události trasování událostí generovaných aplikací. |

![Řídicí panel Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Řídicí panel Service Fabric](./media/log-analytics-service-fabric/sf4.png)

Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují pro Service Fabric.

| Platforma | Přímé agenta | Agent nástroje Operations Manager | Azure Storage | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu | Frekvence kolekce |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minut |

> [!NOTE]
> Rozsah těchto událostí v Service Fabric řešení můžete změnit kliknutím **dat podle posledních 7 dnů** v horní části řídicího panelu. Můžete také zobrazit události generované během posledních sedmi dnů, jeden den nebo šest hodin. Nebo můžete vybrat **vlastní** zadat vlastní období.
>
>

## <a name="next-steps"></a>Další postup

* Použití [protokolu hledání v analýzy protokolů](log-analytics-log-searches.md) zobrazíte podrobné data události Service Fabric.
