---
title: Posouzení aplikace Service Fabric pomocí Log Analytics pomocí webu Azure portal | Dokumentace Microsoftu
description: Řešení Service Fabric můžete použít v Log Analytics pomocí webu Azure portal k posuzuje rizika a stav Service Fabric aplikací, mikroslužby, uzly a clustery.
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
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: nini
ms.component: na
ms.openlocfilehash: 9bc1f7d9eab73a086e664dcc520ecf26befbbf0e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432887"
---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>Posouzení aplikace Service Fabric a mikroslužby pomocí webu Azure portal

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Symbol Service Fabric](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

Tento článek popisuje, jak použít řešení Service Fabric v Log Analytics k identifikaci a řeší problémy v clusteru Service Fabric.

Service Fabric řešení využívá Azure diagnostická data z vašich virtuálních počítačů Service Fabric shromažďováním těchto dat z Azure WAD tabulek. Log Analytics pak přečte události rozhraní Service Fabric, včetně **události Reliable Service**, **události objektu Actor**, **provozní události**, a **vlastní Události trasování událostí pro Windows**. Pomocí řídicího panelu řešení budete moct zobrazit významné problémy a relevantní události ve vašem prostředí Service Fabric.

Abyste mohli začít s řešením, potřebujete cluster Service Fabric připojit k pracovnímu prostoru Log Analytics. Tady jsou tři scénáře, které byste měli zvážit:

1. Pokud jste nenasadili vašeho clusteru Service Fabric, postupujte podle kroků v ***nasadit Service Fabric Cluster připojený k pracovnímu prostoru Log Analytics*** nasadí nový cluster a byl nakonfigurovaný k ukládání dat do služby Log Analytics.
1. Pokud potřebujete získat čítače výkonu z hostitele tak, aby ve vašem clusteru Service Fabric pomocí jiných řešení pro správu jako je zabezpečení, postupujte podle kroků v ***nasadit Service Fabric Cluster připojený k pracovnímu prostoru Log Analytics pomocí rozšíření virtuálního počítače nainstalovat.***
1. Pokud už jste nasadili cluster Service Fabric a chcete se připojit ke službě Log Analytics, postupujte podle kroků v ***přidání existující účet úložiště do služby Log Analytics.***

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>Nasazení clusteru Service Fabric připojený k pracovnímu prostoru Log Analytics.
Tato šablona provede následující akce:

1. Nasadí cluster Azure Service Fabric už připojený k pracovnímu prostoru Log Analytics. Máte možnost vytvořit nový pracovní prostor při nasazení šablony, nebo zadejte název již existující pracovní prostor Log Analytics.
1. Účet úložiště diagnostiky se přidá do pracovního prostoru Log Analytics.
1. Povolí řešení Service Fabric ve vašem pracovním prostoru Log Analytics.

[![Nasazení do Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

Po výběru na výše uvedené tlačítko nasazení se otevře s parametry můžete upravit na webu Azure portal. Je potřeba vytvořit novou skupinu prostředků, pokud zadáte název nového pracovního prostoru Log Analytics:

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

Přijměte právní podmínky a klikněte na tlačítko **vytvořit** ke spuštění nasazení. Po dokončení nasazení byste měli vidět nový pracovní prostor a vytvoření clusteru a WADServiceFabric * tabulky událostí, WADWindowsEventLogs a WADETWEvent přidán:

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>Nasazení clusteru Service Fabric připojený k pracovnímu prostoru Log Analytics pomocí rozšíření virtuálního počítače nainstalovaný.

Tato šablona provede následující akce:

1. Nasadí cluster Azure Service Fabric už připojený k pracovnímu prostoru Log Analytics. Můžete vytvořit nový pracovní prostor nebo použijte již existující.
1. Přidá do pracovního prostoru Log Analytics účty úložiště diagnostiky.
1. Povolí řešení Service Fabric v pracovním prostoru Log Analytics.
1. Rozšíření agenta MMA nainstaluje do každého virtuálního počítače škálovací sady v clusteru Service Fabric. Instalaci agenta MMA budete moct zobrazit metriky výkonu o uzly.

[![Nasazení do Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

Stejný postup výše vstupní parametry potřebné a pusťte se do nasazení. Ještě jednou byste měli vidět nový pracovní prostor, cluster a všechny vytvořené využitím WAD tabulky:

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>Zobrazení dat výkonu

Zobrazení dat výkonu z uzlů:


[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- Spusťte pracovní prostor Log Analytics z portálu Azure portal.
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- V levém podokně přejděte do nastavení a vyberte Data >> čítače výkonu Windows >> "Přidat vybrané čítače výkonu": ![Service Fabric](./media/log-analytics-service-fabric/7.png)
- V prohledávání protokolu pomocí delve do klíčových metrik svých uzlech následující dotazy:

    a. Porovnejte průměrné využití procesoru napříč všemi uzly za poslední hodinu zobrazíte uzly, na kterých jsou potíže a na jaké časový interval uzlu měl prudký nárůst:

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. Zobrazit podobné spojnicové grafy pro dostupná paměť na každý uzel s Tento dotaz:

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    Chcete-li zobrazit výpis všech uzlů, zobrazuje přesně průměrnou hodnotu pro počet MB k dispozici pro každý uzel, pomocí tohoto dotazu:

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. V případě, že chcete přejít na konkrétní uzel prozkoumáním hodinové průměrné, minimální, maximální a 75 percentilu využití procesoru mohli jste k tomu použít tento dotaz (nahraďte pole počítače):

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

Přečtěte si další informace o metrikách výkonu ve službě Log Analytics na [Operations Management Suite blogu](https://blogs.technet.microsoft.com/msoms/tag/metrics/).


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>Přidání existující účet úložiště do služby Log Analytics

Tato šablona jednoduše přidá existujících účtů úložiště do nového nebo existujícího pracovního prostoru Log Analytics.

[![Nasazení do Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> Při výběru skupiny prostředků, vyberte při práci s již existující pracovní prostor Log Analytics, "Použít existující" a vyhledejte skupinu prostředků obsahující pracovní prostor Log Analytics. Vytvořit nový jeden if jinak.
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

Po nasazení této šablony, budete moci zobrazit účet úložiště, který je připojený k pracovnímu prostoru Log Analytics. V takovém případě po přidání jeden další účet úložiště do pracovního prostoru Exchange vytvořeného výše.
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>Zobrazení událostí modulu Service Fabric

Po dokončení nasazení a řešení Service Fabric je povolen v pracovním prostoru, vyberte **Service Fabric** dlaždici na portálu Log Analytics pro spuštění řídicího panelu Service Fabric. Řídicí panel obsahuje sloupce v následující tabulce. Každý sloupec uvádí hlavní 10 událostí podle počtu odpovídajících kritériím tohoto sloupce pro zadaný časový rozsah. Můžete spustit prohledávání protokolu, který poskytuje úplný seznam kliknutím **zobrazit všechny** v pravé dolní části každého sloupce, nebo kliknutím na záhlaví sloupce.

| **Události Service Fabric** | **Popis** |
| --- | --- |
| Významné problémy |Zobrazit problémy, jako je RunAsyncFailures RunAsynCancellations a uzlu akce. |
| Provozní události |Důležité provozní události, jako je například nasazení a upgrade aplikace. |
| Události Reliable Service |Takové Runasyncinvocations události významné spolehlivé služby. |
| Události objektu actor |Objekt actor významné události generované modulem vaše mikroslužby, jako jsou například výjimky vyvolané metodou objektu actor, počet aktivací objektu actor a deaktivací a tak dále. |
| Události aplikace |Všechny vlastní události trasování událostí generovaných aplikací. |

![Řídicí panel Service Fabric](./media/log-analytics-service-fabric/sf3.png)

![Řídicí panel Service Fabric](./media/log-analytics-service-fabric/sf4.png)

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak data se shromažďují pro Service Fabric.

| Platforma | Přímý agent | Agent nástroje Operations Manager | Azure Storage | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 minut |

> [!NOTE]
> Kliknutím můžete změnit rozsah tyto události v Service Fabric řešení **Data podle posledních 7 dní** v horní části řídicího panelu. Můžete také zobrazit události generované během posledních sedmi dnů, jednoho dne nebo 6 hodin. Nebo můžete vybrat **vlastní** zadat vlastní rozsah.
>
>

## <a name="next-steps"></a>Další postup

* Použití [prohledávání protokolů v Log Analytics](log-analytics-log-searches.md) zobrazíte podrobná data události Service Fabric.
