---
title: Monitorování stavu služby Azure Kubernetes Service (AKS) (preview) | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete snadno zkontrolovat výkon vašeho kontejneru AKS, které umožňují rychle porozumět využití vaše hostované prostředí Kubernetes.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: magoedte
ms.openlocfilehash: 806487ec731a1b7fe02ccdfe6b285f5b2e119787
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249093"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorovat stav kontejneru Azure Kubernetes Service (AKS) (preview)

Tento článek popisuje, jak nastavit a používat stav kontejneru Azure monitoru ke sledování výkonu úlohy, které se nasazují do prostředí Kubernetes a hostované ve službě Azure Kubernetes Service (AKS). Monitorování vašeho clusteru a kontejnerů Kubernetes je důležité, zejména v případě, že spouštíte produkční cluster ve velkém měřítku, s několika aplikacemi.

Stav kontejneru poskytuje monitorování možnost shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik výkonu. Po povolení stavu kontejneru, tyto metriky jsou automaticky shromažďovaná za vás prostřednictvím kontejnerizovaných verzi agenta Operations Management Suite (OMS) pro Linux a uloženy v vaše [Log Analytics](../log-analytics/log-analytics-overview.md) pracovního prostoru. Na zahrnuté předdefinované zobrazení nacházejícím úlohy kontejneru a co ovlivňuje výkon stav clusteru Kubernetes, abyste mohli:  

* Určete kontejnery, které běží na uzlu a jejich průměrné využití procesoru a paměti. Tyto znalosti můžete identifikovat kritické body prostředků.
* Určete, kde se nachází kontejneru v kontroleru nebo pod. Tyto znalosti můžete zobrazit kontroleru nebo pod na celkový výkon. 
* Zkontrolujte využití prostředků úlohy běžící na hostiteli, které nesouvisí s standardních procesů, které podporují pod.
* Pochopte chování clusteru ve skupinovém rámečku průměrných a rozděluje zatížení. Tyto znalosti můžete určit, potřeb kapacity a určení maximálního zatížení, který může cluster tolerovat. 

Pokud vás zajímá monitorování a správu Docker a Windows najdete v hostitelích kontejnerů na zobrazení konfigurace, auditování a využití prostředků [řešení pro monitorování kontejnerů](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Požadavky 
Než začnete, ujistěte se, že máte následující:

- Nové nebo existující cluster AKS.
- A kontejnerizovaných agenta OMS pro Linux verze microsoft / oms:ciprod04202018 nebo novější. Číslo verze představuje datum v následujícím formátu: *mmddyyyy*. Agent je automaticky nainstalován během registrace stavu kontejneru. 
- Pracovní prostor Log Analytics. Můžete vytvořit, když povolíte monitorování nový cluster AKS, nebo můžete vytvořit pomocí [Azure Resource Manageru](../log-analytics/log-analytics-template-workspace-configuration.md), pomocí [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).
- Log Analytics roli Přispěvatel, chcete-li povolit monitorování kontejnerů. Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Komponenty 

Vaše schopnost sledování výkonu spoléhá na kontejnerizovaných agenta OMS pro Linux, které shromažďuje data událostí výkonu a ze všech uzlů v clusteru. Agent automaticky nasazení a registraci s zadaný pracovní prostor Log Analytics, když povolíte monitorování kontejnerů. 

>[!NOTE] 
>Pokud už jste nasadili AKS cluster, povolíte monitorování s použitím zadané šablony Azure Resource Manageru, jak je uvedeno dále v tomto článku. Nemůžete použít `kubectl` k upgradu, odstranit, znovu nasadit nebo nasadit agenta. 
>

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Povolit monitorování stavu kontejneru pro nový cluster
Během nasazení můžete povolit monitorování nový cluster AKS na portálu Azure portal. Postupujte podle kroků v tomto článku rychlý Start [Nasaďte cluster Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md). Na **monitorování** stránky, pro **povolit monitorování** možnosti, vyberte **Ano**a potom vyberte existující pracovní prostor Log Analytics nebo vytvořte novou. 

Poté, co jste povolili monitorování a úspěšném dokončení všech konfiguračních úloh, můžete sledovat výkon cluster v některém ze dvou způsobů:

* Přímo v clusteru AKS tak, že vyberete **stavu** v levém podokně.
* Výběrem **monitorovat stav kontejneru** dlaždice na stránce clusteru AKS pro vybraný cluster. Ve službě Azure Monitor, v levém podokně vyberte **stavu**. 

  ![Možnosti pro výběr stavu kontejneru ve službě AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Po povolení sledování, může trvat přibližně 15 minut, než lze zobrazit provozní data pro cluster. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Povolit monitorování stavu kontejneru pro existující spravované clustery
Monitorování clusteru AKS, který je nasazen na webu Azure Portal nebo pomocí zadané šablony Azure Resource Manageru pomocí rutiny prostředí PowerShell můžete povolit `New-AzureRmResourceGroupDeployment` nebo rozhraní příkazového řádku Azure. 

### <a name="enable-monitoring-in-the-azure-portal"></a>Povolit monitorování na webu Azure Portal
Pokud chcete povolit monitorování vašeho kontejneru AKS na portálu Azure portal, postupujte takto:

1. Na webu Azure Portal, vyberte **všechny služby**. 
2. V seznamu prostředků, začněte psát **kontejnery**.  
    Seznam se průběžně filtruje podle vašeho zadání. 
3. Vyberte **služby Kubernetes**.  

    ![Propojení služby Kubernetes](./media/monitoring-container-health/azure-portal-01.png)

4. V seznamu kontejnerů vyberte kontejner.
5. Na stránce Přehled kontejnerů, vyberte **monitorovat stav kontejneru**.  
6. Na **připojení ke službě stavu kontejneru a protokoly** stránky, pokud máte existující Log Analytics pracovní prostor ve stejném předplatném jako cluster, vyberte v rozevíracím seznamu.  
    V seznamu vybrána hodnota výchozího pracovního prostoru a umístění, ke kterému kontejneru AKS nasazuje v rámci předplatného. 

    ![Povolit monitorování stavu kontejneru AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

>[!NOTE]
>Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z clusteru, postupujte podle pokynů v [vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Je potřeba vytvořit pracovní prostor v rámci stejného předplatného, který se nasazuje kontejneru AKS. 
 
Po povolení sledování, může trvat přibližně 15 minut, než lze zobrazit provozní data pro cluster. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Povolte monitorování pomocí šablony Azure Resource Manageru
Tato metoda obsahuje dvě šablony JSON. Jedna šablona určuje konfiguraci povolení monitorování a druhý obsahuje hodnoty parametrů, které nakonfigurujete, zadejte následující informace:

* ID prostředku kontejneru AKS 
* Skupina prostředků, která je nasazená clusteru.
* Pracovní prostor Log Analytics a oblasti se má vytvořit v pracovním prostoru. 

Pracovní prostor Log Analytics je potřeba vytvořit ručně. Vytvořit pracovní prostor, můžete nastavit ji prostřednictvím [Azure Resource Manageru](../log-analytics/log-analytics-template-workspace-configuration.md), pomocí [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), nebo [webu Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Vytvoření a provedení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
      }
     }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     },
    {
        "type": "Microsoft.Resources/deployments",
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
        "apiVersion": "2017-05-10",
        "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
        "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
        "properties": {
            "mode": "Incremental",
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {},
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "location": "[parameters('workspaceRegion')]",
                        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                        "properties": {
                            "workspaceResourceId": "[parameters('workspaceResourceId')]"
                        },
                        "plan": {
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                            "promotionCode": "",
                            "publisher": "Microsoft"
                        }
                    }
                ]
            },
            "parameters": {}
        }
       }
     ]
    }
    ```

2. Uložte soubor jako **existingClusterOnboarding.json** do místní složky.
3. Vložte následující syntaxi JSON do souboru:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Upravte hodnoty **aksResourceId** a **aksResourceLocation** pomocí hodnot na **Přehled služby AKS** stránky pro AKS cluster. Hodnota pro **workspaceResourceId** je úplné ID prostředku pracovního prostoru Log Analytics, která zahrnuje název pracovního prostoru. Také zadejte umístění pracovního prostoru pro **workspaceRegion**. 
5. Uložte soubor jako **existingClusterParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Do složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Chcete-li pomocí rozhraní příkazového řádku Azure v Linuxu spusťte následující příkaz:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Změna konfigurace může trvat několik minut. Když se dokončí, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek:

        ```azurecli
        provisioningState       : Succeeded
        ```
Po povolení sledování, může trvat přibližně 15 minut, než lze zobrazit provozní data pro cluster. 

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení
Verze agenta *06072018* nebo později, můžete ověřit, že agent a řešení se úspěšně nasadily. V předchozích verzích agenta můžete ověřit pouze nasazení agenta.

### <a name="agent-version-06072018-or-later"></a>Verze agenta 06072018 nebo novější
Spusťte následující příkaz k ověření, že je agent úspěšně nasazen. 

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Pokud chcete ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Dříve než 06072018 verze agenta

Chcete-li ověřit, že verze agenta OMS vydané dřív než *06072018* nasazení správně, spusťte následující příkaz:  

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Zobrazení výkonu využití
Při otevření stavu kontejneru, na stránce okamžitě zobrazí využití výkonu celý cluster. Zobrazení informací o clusteru AKS je uspořádaný do čtyř perspektiv:

- Cluster
- Uzly 
- Kontrolery  
- Containers

Na **clusteru** kartě čtyři spojnicové grafy výkonu zobrazit klíčové metriky výkonu ve vašem clusteru. 

![Příklad grafy výkonu na kartě clusteru](./media/monitoring-container-health/container-health-cluster-perfview.png)

Graf výkonu zobrazí čtyři metriky výkonu:

- **Využití procesoru uzlu&nbsp;%**: agregovaná perspektivy využití výkonu procesoru pro celý cluster. Můžete filtrovat výsledky pro časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentily nad grafem, buď samostatně nebo v kombinaci. 
- **Využití paměti v aplikaci node&nbsp;%**: agregovaná perspektivy využití paměti pro celý cluster. Můžete filtrovat výsledky pro časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentily nad grafem, buď samostatně nebo v kombinaci. 
- **Počet uzlů**: počet uzlů a stav ze Kubernetes. Stavy uzlů clusteru, který je reprezentován *všechny*, *připravené*, a *Nepřipraveno* a můžete filtrovat jednotlivě nebo kombinaci v modulu pro výběr nad grafem. 
- **Počet podů aktivit**: počet podů a stav ze Kubernetes. Stavy podů reprezentované *všechny*, *čekající*, *systémem*, a *neznámý* a můžete filtrovat jednotlivě nebo v kombinaci selektor nad grafem. 

Pokud přejdete na **uzly** kartě hierarchii řádek následuje počínaje uzlu v clusteru Kubernetes objektového modelu. Rozbalte uzel a můžete zobrazit jeden nebo více podů se spuštěnou na uzlu. Pokud více než jednoho kontejneru je seskupení pod, se zobrazí jako poslední řádek v hierarchii. Můžete také zobrazit, kolik souvisejících úlohách bez pod běží na hostiteli, pokud má hostitel procesoru nebo tlaku na paměť.

![Příklad Kubernetes uzlu hierarchie v zobrazení výkonu](./media/monitoring-container-health/container-health-nodes-view.png)

Můžete vybrat, řadiče nebo kontejnery v horní části stránky a zkontrolujte stav a využití prostředků týkajících se těchto objektů. Použití polí rozevíracího seznamu v horní části můžete filtrovat podle oboru názvů, služby a uzel. Pokud místo toho chcete zkontrolovat využití paměti v **metrika** rozevíracího seznamu vyberte **paměti RSS** nebo **pracovní sada paměti**. **Paměť RSS** se podporuje jenom pro Kubernetes verze 1.8 a novější. V opačném případě zobrazení hodnot pro **Min&nbsp; %**  jako *NaN&nbsp;%*, má hodnotu číselný datový typ, který představuje Nedefinovaná nebo přičtení hodnotu. 

![Zobrazení výkonu uzly kontejneru](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Ve výchozím nastavení, údaje o výkonu podle posledních 6 hodin, ale můžete změnit v okně pomocí **časový rozsah** rozevíracího seznamu v pravém horním rohu. V tuto chvíli stránky není automatické aktualizace, takže budete muset ručně aktualizovat. Můžete také filtrovat výsledky v rámci časový rozsah výběrem **Avg**, **Min**, **maximální**, **50**, **90. percentil**, a **95** v modulu pro výběr percentil. 

![Výběr percentilu pro filtrování dat](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

V následujícím příkladu si všimněte pro uzel *aks. nodepool 3977305*, hodnota **kontejnery** je 5, což je souhrnné celkový počet kontejnerů.

![Souhrn kontejnerů na příklad uzlu](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Pomůže vám rychle zjistit, jestli máte správnou rovnováhu mezi uzly v clusteru a kontejnerů. 

Informace, které se zobrazí při zobrazení uzlů je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název hostitele. |
| Status | Kubernetes pohled na stav uzlu. |
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Průměrné procento uzlů podle percentilu během vybrané doby trvání. |
| Avg, Min, Max, 50, 90 | Průměrná uzlů podle percentilu během tuto dobu trvání vybrané skutečnou hodnotu. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro uzel; pro podů a kontejnery je průměrná hodnotu hlášenou síťovým hostitele. |
| Containers | Počet kontejnerů. |
| Doba provozu | Představuje čas, protože uzel spuštěn nebo byl restartován. |
| Kontrolery | Pouze pro kontejnery a tyto pody. Zobrazuje kontroleru, který je umístěný v. Ne všechny podů jsou v kontroleru, takže některé se může zobrazit **není k dispozici**. | 
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Pruhový graf trendu nabízí ten samý percentilu metrik procento kontroleru. |


V modulu pro výběr, vyberte **řadiče**.

![Vyberte kontrolerů zobrazení](./media/monitoring-container-health/container-health-controllers-tab.png)

Zde můžete zobrazit stav výkonu řadiče.

![zobrazení výkonu řadiče < název >](./media/monitoring-container-health/container-health-controllers-view.png)

Hierarchie řádek začíná kontroleru a rozbalí kontroleru. Můžete zobrazit jeden nebo více kontejnerů. Rozbalte pod a poslední řádek zobrazuje seskupené pod celému kontejneru. 

Informace, které se zobrazí, když zobrazujete řadiče je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Shrnutí stavu kontejnerů po dokončení spuštění se stavem, jako například *OK*, *ukončeno*, *neúspěšné* *Zastaveno*, nebo *Pozastaveno*. Pokud je kontejner spuštěný, ale stav byl buď není správně zobrazeny nebo nebyl vyzvednou agenta a neodpověděl více než 30 minut, stav je *neznámý*. V následující tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Souhrn průměrem průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Shrnující průměrné využití procesoru millicore nebo paměti výkon kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler nebo pod. |
| Restartování | Souhrn počtu restartování z kontejnerů. |
| Doba provozu | Představuje čas od spuštění kontejneru. |
| Node | Pouze pro kontejnery a tyto pody. Ukazuje kontroler, který je umístěný. | 
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;%| Pruhový graf trendu představující percentilu metrik kontroleru. |

Ikony v poli Stav označují online stav kontejnerů:
 
| Ikona | Status | 
|--------|-------------|
| ![Připraveno spuštěné ikona stavu](./media/monitoring-container-health/container-health-ready-icon.png) | Systémem (připravená)|
| ![Ikona čekání nebo pozastaveném stavu](./media/monitoring-container-health/container-health-waiting-icon.png) | Čeká se na nebo pozastavena|
| ![Naposledy hlásila systémem ikona stavu](./media/monitoring-container-health/container-health-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá více než 30 minut|
| ![Ikona úspěšný stav](./media/monitoring-container-health/container-health-green-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|

Ikona stavu zobrazuje počet založené na co chcete pod poskytuje. Zobrazuje nejhorší dvou stavů, a když najedete myší stav, zobrazí shrnutí stavu ze všech podů v kontejneru. Pokud není k dispozici stavu Připraveno, hodnota stavu zobrazí **(0)**. 

V modulu pro výběr, vyberte **kontejnery**.

![Vyberte kontejnery zobrazení](./media/monitoring-container-health/container-health-containers-tab.png)

Zde můžete zobrazit hlediska výkonu pro vaše kontejnery.

![zobrazení výkonu řadiče < název >](./media/monitoring-container-health/container-health-containers-view.png)

Informace, které se zobrazí, když zobrazujete kontejnery je popsaný v následující tabulce:

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru.|
| Status | Stav kontejnerů, pokud existuje. V další tabulce jsou uvedeny další podrobnosti o ikona stavu.|
| AVG&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Kumulativní z průměrnou procentuální hodnotu Každá entita pro vybranou metriku a percentil. |
| Avg, Min, Max, 50, 90  | Kumulativní průměrné využití procesoru millicore nebo paměti výkonu kontejneru pro vybrané percentil. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Pod | Kontejner, ve které se nachází pod.| 
| Node |  Uzel, ve které se nachází kontejneru. | 
| Restartování | Představuje čas od spuštění kontejneru. |
| Doba provozu | Představuje čas, protože kontejneru se spustit nebo restartovat. |
| Trend Avg&nbsp;%, Min&nbsp;% Max&nbsp;%, 50&nbsp;%, 90. percentil&nbsp;% | Trend pruhový graf, který představuje průměrnou procentuální hodnotu metriky kontejneru. |

Ikony v poli Stav označují online stavy podů, jak je popsáno v následující tabulce:
 
| Ikona | Status | 
|--------|-------------|
| ![Připraveno spuštěné ikona stavu](./media/monitoring-container-health/container-health-ready-icon.png) | Systémem (připravená)|
| ![Ikona čekání nebo pozastaveném stavu](./media/monitoring-container-health/container-health-waiting-icon.png) | Čeká se na nebo pozastavena|
| ![Naposledy hlásila systémem ikona stavu](./media/monitoring-container-health/container-health-grey-icon.png) | Poslední ohlásil spuštěná, ale neodpovídá za více než 30 minut|
| ![Ikona stavu ukončení](./media/monitoring-container-health/container-health-terminated-icon.png) | Úspěšně zastaven nebo se nepovedlo zastavit|
| ![Ikona stavu se nezdařilo](./media/monitoring-container-health/container-health-failed-icon.png) | Chybovém stavu |

## <a name="container-data-collection-details"></a>Podrobnosti o kontejneru shromažďování dat
Stav kontejneru různých metrik a protokolů shromažďuje údaje o výkonu z hostitelů kontejnerů a kontejnery. Data jsou shromažďována každé 3 minuty.

### <a name="container-records"></a>Záznamy kontejneru

Příklady záznamů, které byly shromážděny sadou stav kontejneru a datové typy, které se zobrazí ve výsledcích hledání protokolů, které jsou zobrazeny v následující tabulce:

| Typ dat | Datový typ v prohledávání protokolu | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítače, název_objektu, CounterName &#40;% času procesoru, disku přečte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, síť posílat bajtů, procesor doby využití, sítě&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače, název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ukončovací kód, EnvironmentVar, příkaz, čas vytvoření, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Inventář imagí kontejnerů | `ContainerImageInventory` | TimeGenerated, počítače, Image, ImageTag, ImageSize, VirtualSize, spuštění, pozastavení, zastavení, se nezdařilo, SourceSystem, ID obrázku, TotalContainer |
| Protokol kontejneru | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource LogEntry, SourceSystem, identifikátor ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, počítače, TimeOfCommand, Image, příkaz, SourceSystem, identifikátor ContainerID |
| Inventář kontejnerových uzlů | `ContainerNodeInventory_CL`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kontejnerový proces | `ContainerProcess_CL` | TimeGenerated, počítače, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventář podů v clusteru Kubernetes | `KubePodInventory` | TimeGenerated, počítače, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, parametr ControllerName, ContainerStatus, Identifikátor ContainerID, ContainerName, název, PodLabel, Namespace, PodStatus, název clusteru, PodIp, SourceSystem |
| Seznam uzlů nepatří do clusteru Kubernetes | `KubeNodeInventory` | TimeGenerated, počítače, název clusteru, ClusterId, LastTransitionTimeReady, popisky, stav, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Události Kubernetes | `KubeEvents_CL` | TimeGenerated, počítače, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zprávy, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriky výkonu pro uzly jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SNode" | Počítače, název_objektu, CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec cpuAllocatableNanoCores, memoryAllocatableBytes cpuCapacityNanoCores, memoryCapacityBytes&#41;, CounterValue TimeGenerated, Cesta_k_čítači, SourceSystem | 
| Metriky výkonu pro kontejnery jsou součástí clusteru Kubernetes | Perf &#124; kde ObjectName == "K8SContainer" | Hodnota counterName &#40;cpuUsageNanoCores memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, CounterValue, TimeGenerated, Cesta_k_čítači, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Hledání protokolů pro analýzu dat
Log Analytics můžete hledat trendy, diagnostikovat problémových míst, předpovědi nebo korelovat data, která vám může pomoct určit, zda je aktuální konfiguraci clusteru optimální výkon. Prohledávání protokolů předem definovaných jsou k dispozici pro vás okamžitě začít využívat nebo přizpůsobit vrátit informace způsobem, jaký požadujete. 

Můžete provádět interaktivní analýzu dat v pracovním prostoru tak, že vyberete **zobrazit protokol** možnost je k dispozici úplně vpravo, když rozšiřujete kontroleru nebo kontejneru. **Prohledávání protokolů** nad stránky Azure portal, který jste byli, zobrazí se stránka.

![Analýza dat v Log Analytics](./media/monitoring-container-health/container-health-view-logs.png)   

Výstup protokoly kontejneru, který se předávají do Log Analytics jsou STDOUT a STDERR. Protože Azure managed Kubernetes (AKS) je monitorování stavu kontejneru, Kube-system nejsou ještě dnes shromažďovány z důvodu velkého objemu generovaná data. 

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů
Často je užitečné k sestavování dotazů, které začínají s příkladem jedné až dvou a následnou úpravou podle svých požadavků. Které vám pomůžou vytvářet složitější dotazy, můžete experimentovat s následující ukázkové dotazy:

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech informací o životním cyklu kontejneru| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **V Advanced Analytics, vyberte spojnicové grafy**:<br> Výkonu<br> &#124;kde ObjectName == "Kontejneru" a hodnota CounterName == "čas procesoru v %"<br> &#124;shrnutí AvgCPUPercent = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **V Advanced Analytics, vyberte spojnicové grafy**:<br> Perf &#124; kde ObjectName == "Kontejneru" a hodnota CounterName == "MB využití paměti"<br> &#124;shrnutí AvgUsedMemory = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |

## <a name="how-to-stop-monitoring-with-container-health"></a>Postup zastavení monitorování pomocí stavu kontejneru
Pokud povolíte monitorování vašeho kontejneru AKS, rozhodnete, že už chcete monitorovat, můžete *Odhlásit se totiž* pomocí dodané šablony Azure Resource Manageru pomocí rutiny Powershellu  **Nový AzureRmResourceGroupDeployment** nebo rozhraní příkazového řádku Azure CLI. Jedna šablona JSON Určuje konfiguraci tak, aby *Odhlásit se totiž*. Druhý obsahuje hodnoty parametrů, které můžete nakonfigurovat a určit tak AKS skupinu prostředků clusteru ID a prostředků, která je nasazená clusteru. 

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, naleznete v tématu:
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
* [Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně. Musíte používat Azure CLI verze 2.0.27 nebo novější. Zjistěte verzi, spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Vytvoření a provedení šablony

1. Zkopírujte a vložte do souboru následující syntaxi JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Uložte soubor jako **OptOutTemplate.json** do místní složky.
3. Vložte následující syntaxi JSON do souboru:

    ```json
    {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "aksResourceId": {
         "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
      },
      "aksResourceLocation": {
        "value": "<aksClusterRegion>"
        }
      }
    }
    ```

4. Upravte hodnoty **aksResourceId** a **aksResourceLocation** pomocí hodnot clusteru AKS, které můžete vyhledat na **vlastnosti** stránek pro vybraný cluster .

    ![Stránka vlastnosti kontejneru](./media/monitoring-container-health/container-properties-page.png)

    Když jste na **vlastnosti** stránky, zkopírujte také **ID prostředku pracovního prostoru**. Tato hodnota je povinná, pokud se rozhodnete, že chcete odstranit pracovní prostor Log Analytics později. Odstraňuje se pracovní prostor Log Analytics se neprovádí jako součást tohoto procesu. 

5. Uložte soubor jako **OptOutParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Chcete-li do složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Změna konfigurace může trvat několik minut. Když se dokončí, je vrácena zpráva podobná následující, který obsahuje výsledek:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Pokud chcete spustit následující příkaz pomocí rozhraní příkazového řádku Azure v Linuxu:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Změna konfigurace může trvat několik minut. Když se dokončí, je vrácena zpráva podobná následující, který obsahuje výsledek:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Pokud pracovní prostor byl vytvořen pouze k podpoře monitorování clusteru a už je nepotřebujete, budete muset odstranit ručně. Pokud nejste obeznámeni s postup odstranění pracovního prostoru, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics pomocí webu Azure portal](../log-analytics/log-analytics-manage-del-workspace.md). Nezapomeňte **ID prostředku pracovního prostoru** jsme si zkopírovali dříve v kroku 4, budete potřebovat, který. 

## <a name="troubleshooting"></a>Řešení potíží
Tato část obsahuje informace k řešení potíží s stavu kontejneru.

Pokud stav kontejneru byl úspěšně povolen a nakonfigurován, ale nelze zobrazit informace o stavu nebo výsledky v Log Analytics při provádění prohledávání protokolu, pomůžete diagnostikovat problém následujícím způsobem: 

1. Zkontrolujte stav agenta spuštěním následujícího příkazu: 

    `kubectl get ds omsagent --namespace=kube-system`

    Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Zkontrolujte stav nasazení řešení s verzí agenta *06072018* nebo novější spuštěním následujícího příkazu:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Výstup by měl vypadat podobně jako následující text, který označuje, že byla správně nasazena:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Postup kontroly stavu pod celému ověřte, zda je spuštěna ve spuštění následujícího příkazu: `kubectl get pods --namespace=kube-system`

    Výstup by měl vypadat takto se stavem *systémem* pro omsagent:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. V protokolech agenta. Když se nasadí kontejnerizovanou agenta, spustí rychlou kontrolu spuštěním příkazů (OMI) a zobrazí verzi agenta a poskytovatele. 

5. Pokud chcete ověřit, že agent byl úspěšně připojit, spusťte následující příkaz: `kubectl logs omsagent-484hw --namespace=kube-system`

    Stav by měl vypadat takto:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>Další postup

Chcete-li zobrazit podrobné kontejneru stavu a výkonu informace o aplikacích, naleznete v tématu [vyhledávání protokolů](../log-analytics/log-analytics-log-search.md). 
