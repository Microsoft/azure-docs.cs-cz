---
title: Monitorování stavu služby Azure Kubernetes Service (AKS) (Preview) | Dokumentace Microsoftu
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
ms.date: 07/08/2018
ms.author: magoedte
ms.openlocfilehash: a94f7289c75a4f4d466542c608d81cf5b954f4b1
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37917322"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Monitorovat stav kontejneru Azure Kubernetes Service (AKS) (Preview)

Tento článek popisuje, jak nastavit a používat stav kontejneru Azure monitoru ke sledování výkonu vašich úloh nasazených do prostředí Kubernetes hostované ve službě Azure Kubernetes Service (AKS).  Monitorování clusteru a kontejnerů Kubernetes je důležité, zejména pokud provozujete produkční cluster ve velkém měřítku a s několika aplikacemi.

Stav kontejneru poskytuje monitorování možnost shromažďováním paměti a procesoru metriky z řadiče, uzly a kontejnery, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metrik výkonu.  Po povolení stavu kontejneru, tyto metriky jsou automaticky za vás pomocí kontejnerizovaných verzi agenta OMS pro Linux shromažďují a ukládají ve vašich [Log Analytics](../log-analytics/log-analytics-overview.md) pracovního prostoru.  Předdefinované zobrazení zahrnuté zobrazit nacházejícím úlohy kontejneru a co ovlivňuje výkon stav clusteru Kubernetes to vám umožní pochopit:  

* Jaké kontejnery běží na uzlu a jejich průměrné využití procesoru a paměti k identifikaci kritických bodů prostředků
* Identifikovat, ve kterém se nachází kontejneru v kontroleru a/nebo podů zobrazíte celkový výkon pro kontroler nebo pod 
* Zkontrolujte využití prostředků úlohy běžící na hostiteli nezávislé na standardních procesů lusků podpoře
* Pochopte chování clusteru pod zátěží průměrné a nejčastěji používaný k identifikaci na kapacitu a určení maximálního zatížení, které může tolerovat 

Pokud vás zajímá monitorování a správu Docker a Windows najdete v hostitelích kontejnerů na zobrazení konfigurace, auditování a využití prostředků [řešení pro monitorování kontejnerů](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Požadavky 
Než začnete, zkontrolujte následující podrobnosti to vám umožní pochopit požadavky podporované.

- Nový nebo existující cluster AKS
- Kontejnerizované agenta OMS pro Linux verze microsoft / oms:ciprod04202018 a novější. Číslo verze je reprezentována datum ve formátu - *mmddyyyy*.  Automaticky se nainstaluje během připojování stavu kontejneru.  
- Pracovní prostor Log Analytics.  Když povolíte monitorování nový cluster AKS, nebo můžete vytvořit jednu prostřednictvím lze vytvořit [Azure Resource Manageru](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), nebo z [webu Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).
- Člen role Přispěvatel Log Analytics, chcete-li povolit monitorování kontejnerů.  Další informace o tom, jak řídit přístup k pracovnímu prostoru Log Analytics najdete v tématu [Správa pracovních prostorů](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Komponenty 

Tato funkce využívá kontejnerizovaných agenta OMS pro Linux ke shromažďování výkonu a data události ze všech uzlů v clusteru.  Agent automaticky nasazení a registraci s zadaný pracovní prostor Log Analytics, když povolíte monitorování kontejnerů. 

>[!NOTE] 
>Pokud už jste nasadili AKS cluster, povolte monitorování pomocí zadané šablony Azure Resource Manageru, jak je uvedeno dále v tomto článku. Nemůžete použít `kubectl` k upgradu, odstranit, znovu nasadit nebo nasadit agenta.  
>

## <a name="sign-in-to-azure-portal"></a>Přihlaste se k webu Azure Portal.
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Povolit monitorování stavu kontejneru pro nový cluster
Můžete povolit monitorování nový cluster AKS během nasazení na webu Azure Portal.  Postupujte podle kroků v tomto článku rychlý Start [Nasaďte cluster Azure Kubernetes Service (AKS)](../aks/kubernetes-walkthrough-portal.md).  Když jste na **monitorování** stránce **Ano** pro možnost **povolit monitorování** povolit a potom vyberte existující nebo vytvořte nový pracovní prostor Log Analytics.  

Poté, co je zapnuto monitorování jsou úspěšně dokončeny všechny úlohy konfigurace, můžete sledovat výkon vašeho clusteru z jednoho ze dvou způsobů:

1. Přímo z clusteru AKS tak, že vyberete **stavu** v levém podokně.<br><br> 
2. Kliknutím na **monitorovat stav kontejneru** dlaždice na stránce clusteru AKS pro vybraný cluster.  Ve službě Azure Monitor, vyberte **stavu** v levém podokně.  

![V možnostech stavu kontejneru ve službě AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Jakmile je zapnuté monitorování, může trvat přibližně 15 minut, než budete moct zobrazit provozní data v clusteru.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Povolit monitorování stavu kontejneru pro existující spravované clustery
Můžete povolit monitorování cluster AKS, už nasazená na webu Azure Portal nebo pomocí zadané šablony Azure Resource Manageru pomocí rutiny Powershellu **New-AzureRmResourceGroupDeployment** nebo rozhraní příkazového řádku Azure.  


### <a name="enable-from-azure-portal"></a>Povolit z portálu Azure portal
Proveďte následující kroky, pokud chcete povolit monitorování vašeho kontejneru AKS z webu Azure portal.

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **kontejnery**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **služby Kubernetes**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. V seznamu kontejnerů vyberte kontejner.
3. Na stránce Přehled kontejnerů, vyberte **monitorovat stav kontejneru** a **připojení ke službě stavu kontejneru a protokoly** se zobrazí stránka.
4. Na **připojení ke službě stavu kontejneru a protokoly** stránky, pokud máte existující Log Analytics pracovní prostor ve stejném předplatném jako cluster, vyberte z rozevíracího seznamu.  V seznamu vybrána hodnota výchozího pracovního prostoru a umístění kontejneru AKS nasazuje v rámci předplatného.<br><br> ![Povolit monitorování stavu kontejneru AKS](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>Pokud chcete vytvořit nový pracovní prostor Log Analytics k ukládání dat monitorování z clusteru, postupujte podle kroků v [Cretae pracovnímu prostoru Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md) a je potřeba vytvořit pracovní prostor v rámci stejného předplatného, který je kontejneru AKS nasadit.  
>
 
Jakmile je zapnuté monitorování, může trvat přibližně 15 minut, než budete moct zobrazit provozní data v clusteru. 

### <a name="enable-using-azure-resource-manager-template"></a>Povolení s využitím šablony Azure Resource Manageru
Tato metoda obsahuje dvě šablony JSON, jedna šablona určuje konfiguraci povolení monitorování a jiné šablony JSON obsahuje hodnoty parametrů, které nakonfigurujete, zadejte následující informace:

* ID prostředku kontejneru AKS 
* Skupina prostředků clusteru je nasazena v 
* Pracovní prostor log Analytics a oblasti, kterou chcete vytvořit pracovní prostor v 

Pracovní prostor Log Analytics je potřeba vytvořit ručně.  Pokud chcete vytvořit pracovní prostor, můžete nastavit jednu prostřednictvím [Azure Resource Manageru](../log-analytics/log-analytics-template-workspace-configuration.md), [Powershellu](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), z [webu Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

Pokud nejste obeznámeni s koncepty nasazení prostředků pomocí šablony s využitím Powershellu, přečtěte si téma [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)nebo rozhraní příkazového řádku Azure najdete v tématu, [nasazení prostředků pomocí Šablony Resource Manageru a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Pokud jste se rozhodli používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně.  Je vyžadován, že používáte Azure CLI verze 2.0.27 nebo novější. Spustit `az --version` pro určení verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Vytvoření a provedení šablony

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
3. Zkopírujte a vložte do souboru následující syntaxi JSON:

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

4. Upravit její hodnotu **aksResourceId**, **aksResourceLocation** s hodnotami, které můžete najít na **Přehled služby AKS** stránky pro AKS cluster.  Hodnota pro **workspaceResourceId** je úplné ID prostředku pracovního prostoru Log Analytics, která zahrnuje název pracovního prostoru.  Také zadejte umístění, pracovní prostor je v **workspaceRegion**.    
5. Uložte soubor jako **existingClusterParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Ze složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, který obsahuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Pokud chcete spustit následující příkaz pomocí rozhraní příkazového řádku Azure v Linuxu:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, který obsahuje výsledek:

        ```azurecli
        provisioningState       : Succeeded
        ```
Jakmile je zapnuté monitorování, může trvat přibližně 15 minut, než budete moct zobrazit provozní data v clusteru.  

## <a name="verify-agent-and-solution-deployment"></a>Ověření nasazení agenta a řešení
Verze agenta *06072018* a vyšší, budete moct ověřit, že agent a řešení se úspěšně nasadily.  V předchozích verzích agenta můžete pouze ověření nasazení agenta.

### <a name="agent-version-06072018-and-higher"></a>Verze agenta 06072018 a vyšší
Spusťte následující příkaz k ověření, že je agent úspěšně nasazen.   

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat následující určující, které správně nasadit:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Pokud chcete ověřit nasazení řešení, spusťte následující příkaz:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Výstup by měl vypadat následující určující, které správně nasadit:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Dříve než 06072018 verze agenta

Chcete-li ověřit verzi agenta OMS vydané dřív než *06072018* nasazení správně, spusťte následující příkaz:  

```
kubectl get ds omsagent --namespace=kube-system
```

Výstup by měl vypadat následující určující, které správně nasadit:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Zobrazení výkonu využití
Když otevřete stavu kontejneru, na stránce okamžitě uvede výkonu využití uzlů clusteru.  Zobrazení informací o clusteru AKS je organizována do tří perspektivy

- Uzly 
- Kontrolery  
- Containers

Hierarchie řádek postupuje od uzlu v clusteru Kubernetes objektový model.  Rozbalte uzel se zobrazí jedna nebo více podů se spuštěnou na uzlu a pokud existuje více než jednoho kontejneru seskupením pod, se zobrazují jako poslední řádek v hierarchii.<br><br> ![Příklad Kubernetes uzlu hierarchie v zobrazení výkonu](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Můžete vybrat řadiče nebo kontejnerů z horní části stránky a zkontrolujte stav a využití prostředků týkajících se těchto objektů.  Pomocí pole rozevíracího seznamu v horní části obrazovky můžete filtrovat podle oboru názvů, služby a uzel. Pokud místo toho chcete zkontrolovat využití paměti, z **metrika** rozevíracího seznamu vyberte **paměti RSS** nebo **pracovní sada paměti**.  **Paměť RSS** je podporována pouze pro Kubernetes verze 1.8 a novější. V opačném případě se zobrazí hodnoty pro **Průměrný %** zobrazuje jako *NaN %*, což je hodnota číselný datový typ představující hodnotu undefined nebo přičtení. 

![Zobrazení výkonu výkonu uzly kontejneru](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Ve výchozím nastavení, údaje o výkonu podle posledních 6 hodin, ale můžete změnit v okně s **časový rozsah** rozevírací seznam najdete v pravém horním rohu stránky. V tuto chvíli stránky není automatické aktualizace, takže budete muset ručně aktualizovat. 

V následujícím příkladu, si všimnete pro uzel *aks Neznámá 3402399 0*, hodnota **kontejnery** je 10, která je kumulativní počet kontejnerů.<br><br> ![Souhrn kontejnerů na příklad uzlu](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Pomůže vám rychle zjistit, zda nemáte správná zůstatek kontejnerů mezi uzly v clusteru.  

Následující tabulka popisuje informace při zobrazení uzlů.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název hostitele |
| Status | Kubernetes zobrazení stavu uzlu |
| PRŮMĚRNÝ % | Průměrné procento uzel na základě vybrané metriky pro vybranou dobu trvání. |
| PRŮMĚR | Průměrný počet uzlů na základě vybrané metriky pro vybranou dobu trvání skutečnou hodnotu.  Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro uzel; pro podů a kontejnery je průměrná hodnotu hlášenou síťovým hostitele. |
| Containers | Počet kontejnerů. |
| Doba provozu | Představuje čas, protože uzel spuštěn nebo byl restartován. |
| Pod | Pouze pro kontejnery. Zobrazuje, který je umístěný pods. |
| Kontrolery | Pouze pro kontejnery a tyto pody. Ukazuje kontroler, který je umístěný. Ne všechny podů bude v kontroleru, tak některé můžou zobrazovat není k dispozici. | 
| Trend Průměrný % | Pruhový graf trendu podle kontejnerů a uzlů průměr metriky %. |


V modulu pro výběr, zvolte **řadiče**.<br><br> ![Vyberte kontrolerů zobrazení](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Zde můžete zobrazit stav výkonu řadiče.<br><br> ![zobrazení výkonu řadiče < název >](./media/monitoring-container-health/container-performance-and-health-view-05.png)

Hierarchie řádek začíná kontroleru a rozbalí kontroleru a zobrazí jeden nebo více podů nebo jeden nebo více kontejnerů.  Rozbalte pod a poslední řádek zobrazit seskupené pod celému kontejneru.  

Následující tabulka popisuje informace při zobrazení řadičů.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru|
| Status | Stav kontejnerů po dokončení spuštění se stavem, jako například *ukončeno*, *neúspěšné* *Zastaveno*, nebo *pozastaveno*. Pokud je kontejner spuštěný, ale stav byl buď není správně zobrazí nebo nebyl vyzvednou agenta a neodpověděl více než 30 minut, stav bude *neznámý*. |
| PRŮMĚRNÝ % | Souhrnné průměrem průměrné procento Každá entita pro vybranou metriku. |
| PRŮMĚR | Shrnutí průměrné využití procesoru millicore nebo paměti výkon kontejneru.  Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler nebo pod. |
| Restartování | Shrnutí počtu restartování z kontejnerů. |
| Doba provozu | Představuje čas od spuštění kontejneru. |
| Pod | Pouze pro kontejnery. Zobrazuje, který je umístěný pods. |
| Node | Pouze pro kontejnery a tyto pody. Ukazuje kontroler, který je umístěný. | 
| Trend Průměrný % | Pruhový graf trendu nabízí ten samý průměr metriky % kontejneru. |

V modulu pro výběr, zvolte **kontejnery**.<br><br> ![Vyberte kontejnery zobrazení](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Tady vidíme hlediska výkonu pro vaše kontejnery.<br><br> ![zobrazení výkonu řadiče < název >](./media/monitoring-container-health/container-performance-and-health-view-06.png)

Následující tabulka popisuje informace při zobrazení kontejnerů.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název kontroleru|
| Status | Souhrnné stav kontejnerů, pokud existuje. |
| PRŮMĚRNÝ % | Souhrnné průměrem průměrné procento Každá entita pro vybranou metriku. |
| PRŮMĚR | Shrnutí průměrné využití procesoru millicore nebo paměti výkon kontejneru. Průměrné hodnoty se měří z procesoru nebo paměti limitu nastaveného pro pod. |
| Containers | Celkový počet kontejnerů pro kontroler.|
| Restartování | Představuje čas od spuštění kontejneru. |
| Doba provozu | Představuje čas, protože kontejneru se spustit nebo restartovat. |
| Pod | Kde se nachází pod informace. |
| Node |  Uzel, ve které se nachází kontejneru.  | 
| Trend Průměrný % | Pruhový graf trendu nabízí ten samý průměr metriky % kontejneru. |

## <a name="container-data-collection-details"></a>Podrobnosti o kontejneru dat kolekce
Stav kontejneru různých metrik a protokolů shromažďuje údaje o výkonu z hostitelů kontejnerů a kontejnery. Data jsou shromažďována každé 3 minuty.

### <a name="container-records"></a>Záznamy kontejneru

Následující tabulka uvádí příklady záznamy shromážděné stav kontejneru a datové typy, které se zobrazí ve výsledcích hledání v protokolu.

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
Log Analytics můžete hledat trendy, diagnostikovat problémových míst, předpovědi nebo korelovat data, která vám může pomoct určit, zda je aktuální konfiguraci clusteru optimální výkon.  Prohledávání protokolů předem definovaných jsou k dispozici okamžitě začít používat a přizpůsobit, aby mohla vrátit informace způsobem, jaký požadujete. 

Můžete provádět interaktivní analýzu dat v pracovním prostoru tak, že vyberete **zobrazit protokol** možnost je k dispozici na úplně vpravo, když rozšiřujete kontejneru.  **Prohledávání protokolů** stránka se zobrazí vpravo nahoře na stránce jste byli na portálu.<br><br> ![Analýza dat v Log Analytics](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Výstupní protokoly kontejneru předávají do Log Analytics jsou STDOUT a STDERR. Protože Azure spravované Kubernetes (AKS) je monitorování stavu kontejneru, Kube-system nejsou z důvodu velkého objemu dat vygenerovaných shromažďovány ještě dnes.     

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávacích dotazů
Často je užitečné k sestavování dotazů od jedné až dvou příklad a následnou úpravou jejich podle svých požadavků. Můžete experimentovat s následující ukázkové dotazy, které vám pomůžou vytvářet složitější dotazy.

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Projekt počítače, jméno, obrázek, ImageTag, ContainerState, čas vytvoření, StartedTime, FinishedTime<br> &#124;vykreslit tabulku | Seznam všech kontejnerů životního cyklu informací| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Seřadit podle TimeGenerated desc<br> &#124;vykreslit tabulku | Události Kubernetes|
| ContainerImageInventory<br> &#124;summarize AggregatedValue = count() by bitové kopie, ImageTag, spuštění | Inventář imagí | 
| **V Advanced Analytics, vyberte spojnicové grafy**:<br> Výkonu<br> &#124;kde ObjectName == "Kontejneru" a hodnota CounterName == "čas procesoru v %"<br> &#124;shrnutí AvgCPUPercent = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Procesoru kontejneru | 
| **V Advanced Analytics, vyberte spojnicové grafy**:<br> Perf &#124; kde ObjectName == "Kontejneru" a hodnota CounterName == "MB využití paměti"<br> &#124;shrnutí AvgUsedMemory = avg(CounterValue) podle bin (TimeGenerated, 30 min), InstanceName | Paměti kontejneru |

## <a name="how-to-stop-monitoring-with-container-health"></a>Postup zastavení monitorování pomocí stavu kontejneru
Po povolení monitorování vašeho kontejneru AKS se rozhodnete chcete monitorovat, můžete *Odhlásit se totiž* dodané šablony Azure Resource Manageru pomocí rutiny Powershellu  **Nový AzureRmResourceGroupDeployment** nebo rozhraní příkazového řádku Azure.  Jedna šablona JSON Určuje konfiguraci tak, aby *Odhlásit se totiž* a jiné šablony JSON obsahuje hodnoty parametrů můžete nakonfigurovat a určit tak AKS skupinu prostředků clusteru ID a prostředků v nasazení clusteru.  Pokud nejste obeznámeni s koncepty nasazení prostředků pomocí šablony s využitím Powershellu, přečtěte si téma [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) nebo rozhraní příkazového řádku Azure najdete v tématu, [nasazení prostředků pomocí Šablony Resource Manageru a Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Pokud jste se rozhodli používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně.  Je vyžadován, že používáte Azure CLI verze 2.0.27 nebo novější. Spustit `az --version` pro určení verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Uložte soubor jako **OptOutTemplate.json** do místní složky.
3. Zkopírujte a vložte do souboru následující syntaxi JSON:

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

4. Upravit její hodnotu **aksResourceId** a **aksResourceLocation** s hodnotami cluster AKS, který můžete najít na **vlastnosti** stránek pro vybraný cluster.<br><br> ![Stránka vlastnosti kontejneru](./media/monitoring-container-health/container-properties-page.png)<br>

    Když jste na **vlastnosti** stránky, zkopírujte také **ID prostředku pracovního prostoru**.  Tato hodnota je povinná, pokud se rozhodnete, že chcete odstranit pracovnímu prostoru Log Analytics později, což není provedeno jako součást tohoto procesu.  

5. Uložte soubor jako **OptOutParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Chcete-li ze složky obsahující šablonu použijte následující příkazy Powershellu:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Změna konfigurace může trvat několik minut. Po dokončení, je vrácena zpráva podobná následující, který obsahuje výsledek:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Pokud chcete spustit následující příkaz pomocí rozhraní příkazového řádku Azure v Linuxu:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Změna konfigurace může trvat několik minut. Po dokončení, je vrácena zpráva podobná následující, který obsahuje výsledek:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Pokud pracovní prostor byl vytvořen pouze k podpoře monitorování clusteru a už je nepotřebujete, budete muset odstranit ručně. Pokud nejste obeznámeni s postup odstranění pracovního prostoru, přečtěte si téma [odstranění pracovního prostoru Azure Log Analytics pomocí webu Azure portal](../log-analytics/log-analytics-manage-del-workspace.md).  Nezapomeňte **ID prostředku pracovního prostoru** jsme si zkopírovali dříve v kroku 4, budete potřebovat, který.  

## <a name="troubleshooting"></a>Řešení potíží
Tato část obsahuje informace k řešení potíží s stavu kontejneru.

Pokud stav kontejneru byl úspěšně povolen a nakonfigurován, ale není zobrazuje informace o stavu nebo výsledky v Log Analytics, když provedete hledání v protokolu, můžete provést následující postup vám pomůže diagnostikovat problém.   

1. Zkontrolujte stav agenta spuštěním následujícího příkazu: 

    `kubectl get ds omsagent --namespace=kube-system`

    Výstup by měl vypadat následující určující, které správně nasadit:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Zkontrolujte stav nasazení řešení s verzí agenta *06072018* nebo vyšší spuštěním následujícího příkazu:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Výstup by měl vypadat následující určující, které správně nasadit:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Postup kontroly stavu pod ověřte, zda že je spuštěna nebo není spuštěním následujícího příkazu: `kubectl get pods --namespace=kube-system`

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

4. V protokolech agenta. Když se nasadí kontejnerizovanou agenta, spustí Rychlá kontrola spuštěním příkazů OMI a zobrazuje verzi agenta a Docker zprostředkovatele. Pokud chcete zobrazit, že agent byl úspěšně připojit, spusťte následující příkaz: `kubectl logs omsagent-484hw --namespace=kube-system`

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

[Hledání protokolů](../log-analytics/log-analytics-log-search.md) zobrazíte podrobné kontejneru stavu a výkonu informace o aplikacích.  