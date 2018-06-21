---
title: Sledování stavu Azure Kubernetes služby (AKS) (Preview) | Microsoft Docs
description: Tento článek popisuje, jak můžete snadno zkontrolovat výkon vašeho kontejneru AKS rychle pochopit využití hostovaného prostředí Kubernetes.
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
ms.date: 06/19/2018
ms.author: magoedte
ms.openlocfilehash: 7c4294947cba72b1638e77c2dd8de1f5ee37b62a
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285982"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Sledování stavu kontejneru Azure Kubernetes služby (AKS) (Preview)

Tento článek popisuje, jak nastavit a používat Azure monitorování kontejneru stav sledovat výkon vašich úloh nasadit do prostředí Kubernetes hostované v Azure Kubernetes služby (AKS).  Monitorování clusteru a kontejnerů Kubernetes je důležité, zejména pokud provozujete produkční cluster ve velkém měřítku a s několika aplikacemi.

Kontejner stavu vám dává možnost shromažďování paměti a procesoru metriky z řadiče, uzly a kontejnerů, které jsou k dispozici v Kubernetes prostřednictvím rozhraní API metriky pro monitorování výkonu.  Po povolení kontejneru stavu, jsou tyto metriky automaticky shromážděných pro používáte kontejnerizované verzi agenta OMS pro Linux a uložených v vaše [analýzy protokolů](../log-analytics/log-analytics-overview.md) pracovního prostoru.  Předem definované zobrazení zahrnuté zobrazit zdržují úlohy kontejneru a co ovlivňuje stav výkonu Kubernetes clusteru tak, že rozumíte:  

* Jaké kontejnery běží na uzlu a jejich průměrné využití procesoru a paměti k identifikaci kritických bodů prostředků
* Identifikovat, které se nachází kontejneru v kontroleru a pracovními stanicemi soustředěnými kolem zobrazíte celkový výkon řadiče nebo pod 
* Zkontrolujte využití prostředků úloh běžících na hostiteli, který nesouvisí se standardních procesů podpora pod
* Pochopit chování clusteru průměrný a nejtěžší zatížení pro identifikaci požadavků na kapacitu a určit maximální zatížení, které dokáže odolat 

Pokud vás zajímá v monitorování a správu Docker a Windows hostitele kontejneru konfigurace zobrazení, auditování a využití prostředků, najdete v článku [řešením pro monitorování kontejner](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Požadavky 
Než začnete, zkontrolujte následující podrobnosti, můžete pochopit požadavky, podporované.

- Podporovány jsou následující verze AKS clusteru: 1.7.7 na otázku 1.9.6.
- Kontejnerizované agenta OMS pro Linux verze microsoft / oms:ciprod04202018 a novější. Tento agent je automaticky nainstalován během registrace stavu kontejneru.  
- Pracovní prostor Log Analytics.  Když povolíte monitorování nového clusteru AKS, nebo můžete vytvořit jeden prostřednictvím dá vytvořit [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [prostředí PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), nebo z [portál Azure](../log-analytics/log-analytics-quick-create-workspace.md).
- Člen role Přispěvatel analýzy protokolů, chcete-li povolit monitorování kontejneru.  Další informace o tom, jak řídit přístup k pracovní prostor analýzy protokolů najdete v tématu [spravovat pracovní prostory](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Komponenty 

Tato funkce využívá kontejnerizované agenta OMS pro Linux shromažďovat výkonu a data události ze všech uzlů v clusteru.  Agent automaticky nasazení a po povolení monitorování kontejneru zaregistrovány v zadané pracovní prostor analýzy protokolů. 

>[!NOTE] 
>Pokud už jste nasadili cluster služby AKS, povolte monitorování pomocí zadané šablony Azure Resource Manageru, jak je ukázán později v tomto článku. Nemůžete použít `kubectl` Pokud chcete upgradovat, odstranit, znovu nasaďte nebo nasadit agenta.  
>

## <a name="sign-in-to-azure-portal"></a>Přihlaste se k webu Azure Portal.
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Povolit sledování stavu kontejneru pro nový cluster
Můžete povolit jenom monitorování AKS clusteru při jeho nasazení na portálu Azure.  Postupujte podle kroků v článku rychlý Start [nasazení clusteru Azure Kubernetes služby (AKS)](../aks/kubernetes-walkthrough-portal.md).  Pokud jste na **monitorování** vyberte **Ano** pro možnost **povolit monitorování** povolit a potom vyberte existující nebo vytvořit nový pracovní prostor analýzy protokolů.  

Jakmile je povoleno sledování úspěšném dokončení všech úloh konfigurace, můžete sledovat výkon vašeho clusteru z jednoho ze dvou způsobů:

1. Přímo z clusteru AKS výběrem **stavu** v levém podokně.<br><br> 
2. Kliknutím na **sledování stavu kontejneru** dlaždice na stránce AKS clusteru pro vybraný cluster.  V nástroji Sledování Azure, vyberte **stavu** v levém podokně.  

![Možnosti vyberte kontejner stavu v AKS](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Jakmile je zapnuté monitorování, může trvat přibližně 15 minut, než se budete moci zobrazit provozních dat pro cluster.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Zapnout sledování stavu na kontejner pro existující spravované clustery
Povolení monitorování vašeho kontejneru AKS nasazené můžete to udělat na portálu Azure nebo pomocí zadané šablony Azure Resource Manager pomocí rutiny prostředí PowerShell **New-AzureRmResourceGroupDeployment** nebo Rozhraní příkazového řádku Azure.  


### <a name="enable-from-azure-portal"></a>Povolit z portálu Azure
Proveďte následující kroky k povolení monitorování vašeho kontejneru AKS z portálu Azure.

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **kontejnery**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Kubernetes služby**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. V seznamu kontejnerů vyberte kontejner.
3. Na stránce Přehled kontejneru vyberte **sledování stavu kontejneru** a **registrace stavu kontejneru a protokoly** se zobrazí stránka.
4. Na **registrace stavu kontejneru a protokoly** stránky, pokud máte existující Log Analytics prostoru ve stejném předplatném jako cluster, vyberte ho ze seznamu rozevíracího seznamu.  V seznamu vybrána hodnota výchozí pracovní prostor a umístění kontejneru AKS nasazuje v rámci předplatného. Nebo můžete vybrat **vytvořit nový** a zadejte nový pracovní prostor ve stejném předplatném.<br><br> ![Povolit sledování stavu AKS kontejneru](./media/monitoring-container-health/container-health-enable-brownfield.png) 

    Pokud vyberete **vytvořit nový**, **vytvořit nový pracovní prostor** podokně se zobrazí. **Oblast** výchozí hodnoty pro oblast kontejner prostředku je vytvořen v a přijměte výchozí nastavení nebo vybrat jinou oblast a potom zadejte název pro pracovní prostor.  Klikněte na tlačítko **vytvořit** tak, aby přijímal váš výběr.<br><br> ![Definovat pracovní prostor pro monintoring kontejneru](./media/monitoring-container-health/create-new-workspace-01.png)  

    >[!NOTE]
    >V tuto chvíli nelze vytvořit nový pracovní prostor v oblasti západní centrální USA můžete zvolit pouze existující pracovní prostor v této oblasti.  I v případě, že oblast můžete vybrat ze seznamu, nasazení se spustí, ale selže krátce později.  
    >
 
Jakmile je zapnuté monitorování, může trvat přibližně 15 minut, než se budete moci zobrazit provozních dat pro cluster. 

### <a name="enable-using-azure-resource-manager-template"></a>Povolit pomocí šablony Azure Resource Manageru
Tato metoda obsahuje dvě šablony JSON, jedna šablona určuje konfiguraci, chcete-li povolit monitorování a dalších šablona JSON obsahuje hodnoty parametrů můžete nakonfigurovat tak, aby zadejte následující informace:

* ID prostředku AKS kontejneru 
* Skupina prostředků clusteru je nasazena v 
* Pracovní prostor analýzy protokolů a oblasti, kterou chcete vytvořit pracovní prostor v 

Pracovní prostor analýzy protokolů je potřeba vytvořit ručně.  Pokud chcete vytvořit pracovní prostor, můžete nastavit jeden prostřednictvím [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [prostředí PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), z [portál Azure](../log-analytics/log-analytics-quick-create-workspace.md).

Pokud nejste obeznámeni s koncepty nasazení prostředků pomocí šablony v prostředí PowerShell, přečtěte si téma [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)nebo rozhraní příkazového řádku Azure najdete v tématu, [nasadit prostředky s Šablony Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Pokud jste se rozhodli používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně.  Je požadováno spuštěný Azure CLI verze 2.0.27 nebo novější. Spustit `az --version` k identifikaci verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

2. Uložte tento soubor jako **existingClusterOnboarding.json** do místní složky.
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

4. Upravit její hodnotu **aksResourceId**, **aksResourceLocation** s hodnotami, které můžete najít na **AKS přehled** stránky AKS clusteru.  Hodnota **workspaceResourceId** je je úplné ID prostředku pracovního prostoru analýzy protokolů, která zahrnuje název pracovního prostoru.  Také určit umístění je pracovním prostoru pro v **workspaceRegion**.    
5. Uložte tento soubor jako **existingClusterParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Použijte následující příkazy prostředí PowerShell ve složce se šablonou:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která zahrnuje výsledek:

        ```powershell
        provisioningState       : Succeeded
        ```

    * Následující příkaz spuštění pomocí rozhraní příkazového řádku Azure pro Linux:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        Změna konfigurace může trvat několik minut. Po dokončení se zobrazí zpráva podobná následující, která zahrnuje výsledek:

        ```azurecli
        provisioningState       : Succeeded
        ```
Jakmile je zapnuté monitorování, může trvat přibližně 15 minut, než se budete moci zobrazit provozních dat pro cluster.  

## <a name="verify-agent-deployed-successfully"></a>Ověření úspěšné nasazení agenta
Pokud chcete ověřit agenta OMS správně nasazená, spusťte následující příkaz: ` kubectl get ds omsagent --namespace=kube-system`.

Výstup by měl vypadat následující označující, které správně nasadit:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Zobrazení výkonu využití
Při otevření stavu kontejneru stránce okamžitě uvede využití výkonu uzly clusteru.  Zobrazení informací o AKS clusteru jsou uspořádány do tří perspektivy:

- Uzly 
- Kontrolery  
- Containers

Hierarchie řádek odpovídá objektový model Kubernetes počínaje uzlu v clusteru.  Rozbalte uzel a zobrazí jeden nebo více pracovními stanicemi soustředěnými kolem spuštěné na uzlu, a pokud existuje více než jednoho kontejneru seskupené do pod, zobrazí se jako poslední řádek v hierarchii.<br><br> ![Příklad Kubernetes uzlu hierarchie v zobrazení výkonu](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Můžete vybrat řadiče nebo kontejnery z horní části stránky a zkontrolujte využití stavu a prostředků k těmto objektům.  Filtrovat podle oboru názvů služby a uzlu pomocí rozevíracího seznamu polí v horní části obrazovky. Pokud místo toho chcete zkontrolovat využití paměti z **metrika** rozevíracího seznamu vyberte **paměti RSS** nebo **paměti pracovní sady**.  **Paměť RSS** je podporována pouze pro Kubernetes verze 1,8 a novější. Jinak, najdete v části hodnoty pro **průměr %** zobrazuje jako *NaN %*, což je hodnota číselný datový typ reprezentující nedefinované nebo unrepresentable hodnotu. 

![Zobrazení výkonu uzly kontejneru výkonu](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Ve výchozím nastavení, údaje o výkonu podle posledních šest hodin, ale můžete změnit okno s **časový rozsah** rozevíracím seznamu v pravém horním rohu stránky nalezen. V tomto okamžiku stránky není automatická aktualizace, takže je třeba jej ručně aktualizovat. 

V následujícím příkladu, si všimnete pro uzel *aks Neznámá-3402399-0*, hodnota **kontejnery** je 10, což je kumulativní celkový počet kontejnerů nasazení.<br><br> ![Souhrn kontejnerů za příklad uzlu](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Ho můžete rychle zjistit, zda nemáte správnou rovnováhu kontejnerů mezi uzly v clusteru.  

Následující tabulka popisuje informace uvedené při zobrazení uzlů.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název hostitele |
| Status | Kubernetes zobrazení stavu uzlu |
| % PRŮMĚR | Průměrná uzlu procento podle vybrané metriky pro vybrané doby trvání. |
| PRŮMĚR | Průměrná uzlů na základě skutečnou hodnotu vybrané metriky pro vybrané doby trvání.  Průměrná hodnota se měří z využití procesoru nebo paměti limit nastavený pro uzel; pracovními stanicemi soustředěnými kolem a kontejnerů, je hodnota průměr hlášené hostitele. |
| Containers | Počet kontejnerů. |
| Doba provozu | Představuje čas, protože uzel spuštění nebo byl restartován. |
| Pod | Pouze pro kontejnery. Zobrazuje, který je umístěný pods. |
| Kontrolery | Pouze pro kontejnery a pracovními stanicemi soustředěnými kolem. Zobrazuje řadič, který je umístěný. Ne všechny pracovními stanicemi soustředěnými kolem bude v řadič, takže některé může zobrazovat není k dispozici. | 
| Trend průměr % | Pruhový graf trendů na základě na kontejner a uzel průměr metriky %. |


Z modulu pro výběr, zvolte **řadiče**.<br><br> ![Vyberte řadiče zobrazení](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Zde se zobrazí stav výkonu řadiče.<br><br> ![zobrazení výkonu řadiče < název >](./media/monitoring-container-health/container-performance-and-health-view-05.png)

Hierarchie řádek začíná řadič a rozšíří kontroleru a zobrazí jeden nebo více pracovními stanicemi soustředěnými kolem nebo jeden nebo více kontejnerů.  Rozbalte pod a kontejneru seskupené na pod zobrazit poslední řádek.  

Následující tabulka popisuje informace uvedené při zobrazení řadičů.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název řadiče|
| Status | Stav kontejnery po dokončení spuštění se stavem, jako například *ukončeno*, *se nezdařilo* *Zastaveno*, nebo *pozastaveno*. Pokud je spuštěná kontejneru, ale stav byl buď není správně uvedené nebo nebyla zachyceny pomocí agenta a neodpověděl víc než 30 minut, stav bude *neznámé*. |
| % PRŮMĚR | Souhrnné průměr průměrný % každé entity pro vybrané metriky. |
| PRŮMĚR | Souhrnné průměrné využití procesoru millicore nebo paměť výkonu kontejneru.  Průměrná hodnota se měří od nastavit pro pod limit využití procesoru nebo paměti. |
| Containers | Celkový počet kontejnery pro kontroler nebo pod. |
| Restartování | Souhrnné počtu restartování z kontejnerů. |
| Doba provozu | Představuje dobu od spuštění kontejner. |
| Pod | Pouze pro kontejnery. Zobrazuje, který je umístěný pods. |
| Node | Pouze pro kontejnery a pracovními stanicemi soustředěnými kolem. Zobrazuje řadič, který je umístěný. | 
| Trend průměr % | Trend pruhového grafu představuje průměrný metriky % kontejneru. |

Z modulu pro výběr, zvolte **kontejnery**.<br><br> ![Vyberte kontejnery zobrazení](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Tady můžete vidíte stav výkonu kontejnerů.<br><br> ![zobrazení výkonu řadiče < název >](./media/monitoring-container-health/container-performance-and-health-view-06.png)

Následující tabulka popisuje informace uvedené při zobrazení kontejnery.

| Sloupec | Popis | 
|--------|-------------|
| Název | Název řadiče|
| Status | Souhrnné stav kontejnery, pokud existuje. |
| % PRŮMĚR | Souhrnné průměr průměrný % každé entity pro vybrané metriky. |
| PRŮMĚR | Souhrnné průměrné využití procesoru millicore nebo paměť výkonu kontejneru. Průměrná hodnota se měří od nastavit pro pod limit využití procesoru nebo paměti. |
| Containers | Celkový počet kontejnery pro kontroler.|
| Restartování | Představuje dobu od spuštění kontejner. |
| Doba provozu | Představuje čas, vzhledem k tomu, že kontejner byl spustit nebo restartovat. |
| Pod | Kde se nachází pod informace. |
| Node |  Uzel, kde se nachází kontejneru.  | 
| Trend průměr % | Trend pruhového grafu představuje průměrný metriky % kontejneru. |

## <a name="container-data-collection-details"></a>Kontejner dat kolekce podrobnosti
Kontejner stavu shromažďuje různé metriky a protokolu údaje o výkonu z kontejneru hostitelů a kontejnery. Data jsou shromažďována každé tři minuty.

### <a name="container-records"></a>Záznamů kontejneru

Následující tabulka uvádí příklady záznamů shromažďují stavu kontejneru a datové typy, které se zobrazí ve výsledcích hledání protokolu.

| Typ dat | Datový typ v hledání protokolů | Fields (Pole) |
| --- | --- | --- |
| Výkon pro hostitele a kontejnery | `Perf` | Počítač, ObjectName, název_čítače &#40;% času procesoru, disku čte MB, zapíše MB, MB využití paměti, disku sítě přijatých bajtů, sítě odesílat bajtů, procesor doba využití, sítě&#41;, přepočtené, TimeGenerated, Cesta_k_čítači, SourceSystem |
| Kontejner inventáře | `ContainerInventory` | TimeGenerated, počítače a název kontejneru, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, příkazu, CreatedTime, StartedTime, FinishedTime, SourceSystem, identifikátor ContainerID, ID obrázku |
| Kontejner image inventáře | `ContainerImageInventory` | TimeGenerated, počítače, Image, ImageTag, ImageSize, VirtualSize, spuštění, pozastavena, zastavit, se nezdařilo, SourceSystem, ID obrázku, TotalContainer |
| Kontejner protokolu | `ContainerLog` | TimeGenerated, počítač, ID bitové kopie, název kontejneru, LogEntrySource, LogEntry, SourceSystem, identifikátor ContainerID |
| Protokol služby kontejneru | `ContainerServiceLog`  | TimeGenerated, počítače, TimeOfCommand, Image, příkazu, SourceSystem, identifikátor ContainerID |
| Uzel inventáře kontejneru | `ContainerNodeInventory_CL`| TimeGenerated, počítače, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Proces kontejneru | `ContainerProcess_CL` | TimeGenerated, počítače, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventář pracovními stanicemi soustředěnými kolem v clusteru s podporou Kubernetes | `KubePodInventory` | TimeGenerated, počítače, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, Identifikátor ContainerID, ContainerName, název, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventář uzly součástí clusteru s podporou Kubernetes | `KubeNodeInventory` | TimeGenerated, počítače, název clusteru, ClusterId, LastTransitionTimeReady, popisky, stav, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes události | `KubeEvents_CL` | TimeGenerated, počítače, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, zprávu, SourceSystem | 
| Služby v clusteru Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metrika výkonu pro uzly součástí clusteru Kubernetes | Výkonu &#124; kde ObjectName == "K8SNode" | Počítač, ObjectName, název_čítače &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, přepočtené, TimeGenerated, Cesta_k_čítači, SourceSystem | 
| Metrika výkonu pro kontejnery součástí clusteru Kubernetes | Výkonu &#124; kde ObjectName == "K8SContainer" | Název_čítače &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, přepočtené, TimeGenerated, Cesta_k_čítači, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Protokoly vyhledávání k analýze dat
Analýzy protokolů můžete hledat trendy, diagnostikovat kritická místa, prognózy nebo correlate dat, které vám mohou pomoci určit, zda je aktuální konfiguraci clusteru optimální výkon.  Hledání předem definované protokolu jsou k dispozici okamžitě začít používat a přizpůsobit cílem vrátit informace požadovaným způsobem. 

Interaktivní analýzu dat v pracovním prostoru můžete provádět výběrem **zobrazit protokol** možnost je k dispozici na úplně vpravo při rozbalte kontejner.  **Přihlaste se vyhledávání** stránky se zobrazí vpravo nahoře stránce jste byli na portálu.<br><br> ![Analyzovat data v analýzy protokolů](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

Výstup protokoly kontejneru předají k analýze protokolů jsou STDOUT a STDERR. Protože Azure spravované Kubernetes (AKS) je sledování stavu kontejneru, Kube systému není vzhledem k velkému objemu vygenerovaných dat shromážděných ještě dnes.     

### <a name="example-log-search-queries"></a>Příklad protokolu vyhledávací dotazy
Je často užitečné k vytvoření dotazů počínaje příklad nebo dva a potom úpravou těchto podle svých požadavků. Můžete experimentovat s následující ukázkové dotazy, které vám umožní vytvořit složitější dotazy.

| Dotaz | Popis | 
|-------|-------------|
| ContainerInventory<br> &#124;Počítače, jméno, obrázek, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projektu<br> &#124;vykreslení tabulky | Zobrazí seznam všech kontejneru životního cyklu informace| 
| KubeEvents_CL<br> &#124;kde not(isempty(Namespace_s))<br> &#124;Řadit podle TimeGenerated desc<br> &#124;vykreslení tabulky | Kubernetes události|
| ContainerImageInventory<br> &#124;shrnout AggregatedValue = count() obrázek, ImageTag, spuštěná | Obrázek inventáře | 
| **V rozšířené analýzy, vyberte spojnicových grafů**:<br> Výkonu<br> &#124;kde ObjectName == "Kontejneru" a název_čítače == "čas procesoru v %"<br> &#124;shrnout AvgCPUPercent = avg(CounterValue) podle bin (TimeGenerated, 30m), InstanceName | Kontejner procesoru | 
| **V rozšířené analýzy, vyberte spojnicových grafů**:<br> Výkonu &#124; kde ObjectName == "Kontejneru" a název_čítače == "MB paměti využití"<br> &#124;shrnout AvgUsedMemory = avg(CounterValue) podle bin (TimeGenerated, 30m), InstanceName | Kontejner paměti |

## <a name="how-to-stop-monitoring-with-container-health"></a>Postup zastavení monitorování pomocí stavu kontejneru
Po povolení monitorování vašeho kontejneru AKS rozhodnout jste už chcete ho chcete sledovat, můžete *chodit* pomocí zadané šablony Azure Resource Manager pomocí rutiny prostředí PowerShell  **Nové AzureRmResourceGroupDeployment** nebo rozhraní příkazového řádku Azure.  Jedna šablona JSON Určuje konfiguraci *chodit* a dalších šablona JSON obsahuje hodnoty parametrů můžete nakonfigurovat a určit tak AKS skupinu prostředků clusteru ID a prostředek clusteru je nasazena v.  Pokud nejste obeznámeni s koncepty nasazení prostředků pomocí šablony v prostředí PowerShell, přečtěte si téma [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) nebo rozhraní příkazového řádku Azure najdete v tématu, [nasadit prostředky s Šablony Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md).

Pokud jste se rozhodli používat rozhraní příkazového řádku Azure, musíte nejprve nainstalovat a používat rozhraní příkazového řádku místně.  Je požadováno spuštěný Azure CLI verze 2.0.27 nebo novější. Spustit `az --version` k identifikaci verze. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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

2. Uložte tento soubor jako **OptOutTemplate.json** do místní složky.
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

4. Upravit její hodnotu **aksResourceId** a **aksResourceLocation** hodnotami AKS clusteru, které můžete najít na **vlastnosti** stránky pro vybraný cluster.<br><br> ![Stránka vlastností kontejneru](./media/monitoring-container-health/container-properties-page.png)<br>

    Na **vlastnosti** stránky, zkopírujte také **ID prostředku prostoru**.  Tato hodnota je vyžadována, pokud se rozhodnete, že chcete odstranit pracovní prostor analýzy protokolů později, která se provádí v rámci tohoto procesu.  

5. Uložte tento soubor jako **OptOutParam.json** do místní složky.
6. Jste připraveni k nasazení této šablony. 

    * Chcete-li použít následující příkazy prostředí PowerShell ve složce obsahující šablony:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        Změna konfigurace může trvat několik minut. Po dokončení, je vrácená zpráva podobná následující, která zahrnuje výsledek:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * Následující příkaz spuštění pomocí rozhraní příkazového řádku Azure pro Linux:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        Změna konfigurace může trvat několik minut. Po dokončení, je vrácená zpráva podobná následující, která zahrnuje výsledek:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Pokud pracovní prostor byl vytvořen pouze pro podporu monitorování clusteru a již není potřeba, budete muset ručně odstranit. Pokud nejste obeznámeni s jak odstranit pracovní prostor, přečtěte si téma [odstranit pracovní prostor služby Azure Log Analytics pomocí portálu Azure](../log-analytics/log-analytics-manage-del-workspace.md).  Nezapomeňte o **ID prostředku prostoru** jsme zkopírovali dříve v kroku 4, budete potřebovat, který.  

## <a name="troubleshooting"></a>Řešení potíží
Tato část obsahuje informace k řešení potíží s stavu kontejneru.

Pokud kontejner stavu byla úspěšně povolena a konfigurována, ale není zjistíte informace o stavu nebo má za následek analýzy protokolů při hledání protokolu, můžete provést následující kroky pro usnadnění diagnostiky problému.   

1. Zkontrolujte stav agenta spuštěním následujícího příkazu: `kubectl get ds omsagent --namespace=kube-system`

    Výstup by měl vypadat následující označující, které agent běží na všech uzlech v clusteru.  Například tento cluster má dva uzly a byste měli očekávat hodnota, která má stejný počet uzlů.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Zkontrolujte stav pod k ověření, že je spuštěna nebo není spuštěním následujícího příkazu: `kubectl get pods --namespace=kube-system`

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

3. Zkontrolujte protokol agenta. Po nasazení agenta nástroje kontejnerizované získá provede rychlou kontrolu spuštěním příkazů OMI a zobrazuje verzi agenta a Docker zprostředkovatele. Pokud chcete zobrazit, že agent byl úspěšně zařazený nemá, spusťte následující příkaz: `kubectl logs omsagent-484hw --namespace=kube-system`

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

[V protokolech Hledat](../log-analytics/log-analytics-log-search.md) zobrazíte informace o výkonu podrobné kontejneru stavu a aplikace.  
