---
title: Ochrana hybridních a multi-cloudových Kubernetes nasazení pomocí Azure Defenderu pro Kubernetes
description: Použití Azure Defenderu pro Kubernetes s vašimi místními a Kubernetes clustery s více cloudy
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029195"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Zabraňte clusterům Kubernetes s povolenou podporou Azure ARC v prostředích místních i cloudových prostředí.

**Rozšíření clustery Azure Defenderu pro Kubernetes** můžou chránit vaše místní clustery se stejnými možnostmi detekce hrozeb, které nabízí clustery služby Azure Kubernetes. Povolte na svých clusterech [Kubernetes s povoleným ARC Azure](../azure-arc/kubernetes/overview.md) a nasaďte rozšíření podle pokynů na této stránce. 

Rozšíření může také chránit clustery Kubernetes u jiných poskytovatelů cloudů, i když nejsou na svých spravovaných Kubernetes službách.

> [!TIP]
> Vložili jsme několik ukázkových souborů, které vám pomůžou s instalačním procesem v [příkladech instalace na GitHubu](https://aka.ms/kubernetes-extension-installation-examples).

## <a name="availability"></a>Dostupnost

| Aspekt | Podrobnosti |
|--------|---------|
| Stav vydaných verzí | **Preview**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Požadované role a oprávnění | [Správce zabezpečení](../role-based-access-control/built-in-roles.md#security-admin) může zrušit výstrahy.<br>[Čtenář zabezpečení](../role-based-access-control/built-in-roles.md#security-reader) může zobrazit zjištění |
| Ceny | Vyžaduje [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md) . |
| Podporované distribuce Kubernetes | [Služba Azure Kubernetes na Azure Stack HCL](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [Modul AKS](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (verze 4,6 nebo novější) |
| Omezení | Služba Azure ARC s povoleným Kubernetes a rozšířením Azure Defender **nepodporuje** spravované nabídky Kubernetes, jako je Google Kubernetes Engine a elastické služby Kubernetes. [Služba Azure Defender je nativně dostupná pro Azure Kubernetes Service (AKS)](defender-for-kubernetes-introduction.md) a nevyžaduje připojení clusteru ke službě Azure ARC. |
| Prostředí a oblasti | Dostupnost pro toto rozšíření je stejná jako [Kubernetes s povoleným ARC Azure](../azure-arc/kubernetes/overview.md) .|

## <a name="architecture-overview"></a>Přehled architektury

Pro všechny Kubernetes clustery kromě AKS budete muset připojit cluster ke službě Azure ARC. Po připojení můžete Azure Defender pro Kubernetes nasadit na prostředky [Kubernetes s podporou ARC Azure](../azure-arc/kubernetes/overview.md) jako [rozšíření clusteru](../azure-arc/kubernetes/extensions.md).

Komponenty rozšíření shromažďují data protokolů Kubernetes audit ze všech uzlů řídicích rovin v clusteru a odesílají je do Azure Defenderu pro Kubernetes back-endu v cloudu pro další analýzu. Rozšíření je zaregistrované u Log Analytics pracovního prostoru, který se používá jako datový kanál, ale data protokolu auditu se neukládají v pracovním prostoru Log Analytics.

Tento diagram znázorňuje interakci mezi Azure Defenderem pro Kubernetes a clusterem Kubernetes s povoleným ARC Azure:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Diagram architektury vysoké úrovně znázorňuje interakci mezi Azure Defenderem pro Kubernetes a clustery Kubernetes s podporou ARC Azure." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Požadavky

- [V předplatném je povolený](enable-azure-defender.md) Azure Defender pro Kubernetes.
- Váš cluster Kubernetes je [připojený k Arc Azure](../azure-arc/kubernetes/quickstart-connect-cluster.md) .
- Splnili jste požadavky uvedené v části [Obecná dokumentace k rozšířením clusteru](../azure-arc/kubernetes/extensions.md#prerequisites).

## <a name="deploy-the-azure-defender-extension"></a>Nasazení rozšíření v programu Azure Defender

Rozšíření pro Azure Defender můžete nasadit pomocí různých metod. Podrobný postup si můžete vybrat na příslušné kartě.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Použití možnosti Rychlá oprava z Security Center doporučení

Vyhrazené doporučení v Azure Security Center poskytuje:

- **Přehled** o tom, které clustery mají nasazené rozšíření Defender for Kubernetes
- **Možnost rychlé opravy** pro nasazení do těchto clusterů bez rozšíření

1. Na stránce doporučení Azure Security Center otevřete ovládací prvek zabezpečení **Azure Defenderu** .

1. Pomocí filtru vyhledejte doporučení s názvem **clustery Kubernetes s povoleným ARC Azure, která by měla mít nainstalované rozšíření v programu Azure Defender**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center doporučení pro nasazení rozšíření Azure Defenderu pro clustery s podporou Azure ARC s povoleným Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Všimněte si ikony Rychlá oprava ve sloupci Actions (akce)

1. Kliknutím na rozšíření zobrazíte podrobnosti o zdravých a špatných prostředcích – clustery s rozšířením a bez něj.

1. V seznamu zdroje, který není v pořádku vyberte cluster a výběrem možnosti **opravit** otevřete podokno s možnostmi oprav.

1. Vyberte relevantní pracovní prostor Log Analytics a vyberte možnost **napravit x prostředek**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Nasaďte rozšíření Azure Defenderu pro Azure ARC s možností Rychlá oprava Security Center.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Nasazení rozšíření v Azure Defenderu pomocí rozhraní příkazového řádku Azure

1. Přihlaste se k Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Ujistěte se, že používáte stejné ID předplatného ``<your-subscription-id>`` jako ten, který jste použili při připojování clusteru ke službě Azure ARC.

1. Spuštěním následujícího příkazu nasaďte rozšíření nad cluster Kubernetes s povoleným ARC Azure:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Popis všech podporovaných nastavení konfigurace typu rozšíření v programu Azure Defender je uveden níže:

    | Vlastnost | Popis |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Volitelné**. Úplné ID prostředku vlastního pracovního prostoru Log Analytics.<br>Pokud není zadaný, použije se výchozí pracovní prostor oblasti.<br><br>Chcete-li získat úplné ID prostředku, spusťte následující příkaz, který zobrazí seznam pracovních prostorů v předplatných ve výchozím formátu JSON:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>ID prostředku pracovního prostoru Log Analytics má následující syntaxi:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>Další informace najdete v [Log Analytics pracovních prostorů](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) . |
    | auditLogPath |**Volitelné**. Úplná cesta k souborům protokolu auditu.<br>Pokud není zadaný, použije se výchozí cesta ``/var/log/kube-apiserver/audit.log`` .<br>Pro modul AKS je standardní cesta ``/var/log/kubeaudit/audit.log`` |

    Následující příkaz ukazuje příklad použití všech volitelných polí:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Nasazení rozšíření v programu Azure Defender pomocí Azure Resource Manager

Pokud chcete použít Azure Resource Manager k nasazení rozšíření v programu Azure Defender, budete potřebovat pracovní prostor Log Analytics v předplatném. Další informace najdete v [Log Analytics pracovních prostorů](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

V [příkladech instalace](https://aka.ms/kubernetes-extension-installation-examples)Security Center můžete použít **azure-defender-extension-arm-template.jsv** šabloně správce prostředků.

> [!TIP]
> Pokud Správce prostředků šablony začínáte, začněte tady: [co jsou Azure Resource Manager šablony?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Nasazení rozšíření v programu Azure Defender pomocí REST API 

Pokud chcete použít REST API k nasazení rozšíření v programu Azure Defender, budete potřebovat pracovní prostor Log Analytics v předplatném. Další informace najdete v [Log Analytics pracovních prostorů](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> Nejjednodušší způsob, jak použít rozhraní API k nasazení rozšíření v programu Azure Defender, je s dodaným příkladem **JSON Collection Collection** z [příkladů instalace](https://aka.ms/kubernetes-extension-installation-examples)Security Center.
- Chcete-li upravit soubor JSON pro kolekci pro zápis nebo ručně nasadit rozšíření pomocí REST API, spusťte následující příkaz PUT:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Kde:

    | Name            | V   | Požaduje se | Typ   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | ID předplatného | program | True     | řetězec | ID předplatného prostředku Kubernetes vašeho povoleného ARC Azure |
    | Skupina prostředků  | program | True     | řetězec | Název skupiny prostředků, která obsahuje prostředek Kubernetes s povoleným obloukem Azure ARC |
    | Název clusteru    | program | True     | řetězec | Název prostředku Kubernetes s povoleným obloukem Azure  |


    Pro **ověřování** musí mít vaše hlavička nosný token (stejně jako ostatní rozhraní API Azure). Pokud chcete získat nosný token, spusťte následující příkaz:

    ```az account get-access-token --subscription <your-subscription-id>``` Pro tělo zprávy použijte následující strukturu:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    Popis vlastností je uveden níže:

    | Vlastnost | Popis | 
    | -------- | ----------- |
    | logAnalytics. ID pracovního prostoru | ID pracovního prostoru Log Analytics prostředku |
    | logAnalytics. Key         | Klíč prostředku Log Analytics | 
    | auditLogPath             | **Volitelné**. Úplná cesta k souborům protokolu auditu. Výchozí hodnotou je ``/var/log/kube-apiserver/audit.log``. |

---

## <a name="verify-the-deployment"></a>Ověření nasazení

Pokud chcete ověřit, jestli je v clusteru nainstalované rozšíření Azure Defender, postupujte podle kroků uvedených na jedné z následujících karet:

### <a name="azure-portal---security-center"></a>[**Azure Portal – Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Pomocí Security Center doporučení ověřte stav rozšíření.

1. Na stránce doporučení Azure Security Center otevřete ovládací prvek zabezpečení  **Azure Defenderu** .

1. Vyberte doporučení s názvem **clustery Kubernetes s povoleným rozšířením Azure ARC, aby bylo nainstalované rozšíření programu Azure Defender**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center doporučení pro nasazení rozšíření Azure Defenderu pro clustery s podporou Azure ARC s povoleným Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Ověřte, že cluster, na který jste rozšíření nasadili, je uvedený **v pořádku**.


### <a name="azure-portal---azure-arc"></a>[**Azure Portal – ARC Azure**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>K ověření stavu rozšíření použijte stránky ARC v Azure.

1. Z Azure Portal otevřete **Azure ARC**.
1. V seznamu infrastruktura vyberte **clustery Kubernetes** a pak vyberte konkrétní cluster.
1. Otevřete stránku rozšíření. Jsou uvedena rozšíření v clusteru. Zkontrolujte sloupec **stav instalace** a potvrďte, že rozšíření programu Azure Defender bylo nainstalováno správně.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Stránka ARC Azure pro kontrolu stavu všech nainstalovaných rozšíření v clusteru Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Další podrobnosti získáte výběrem tohoto rozšíření.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Úplné podrobnosti rozšíření Azure ARC v clusteru Kubernetes.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Použití rozhraní příkazového řádku Azure k ověření, že rozšíření je nasazené

1. V Azure CLI spusťte následující příkaz:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. V odpovědi vyhledejte "extensionType": "Microsoft. azuredefender. Kubernetes" a "installState": "installed".

    > [!NOTE]
    > Může se zobrazit zpráva "installState": "čeká" během prvních několika minut.
    
1. Pokud stav zobrazuje **instalaci**, spusťte na počítači následující příkaz se souborem, který je `kubeconfig` odkazoval na váš cluster, a ověřte, že je pod názvem "azuredefender-XXXXX" ve stavu spuštěno:
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Použijte REST API k ověření, že rozšíření je nasazené.

Chcete-li potvrdit úspěšné nasazení nebo ověřit stav rozšíření kdykoli:

1. Spusťte následující příkaz GET:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. V odpovědi vyhledejte "extensionType": "Microsoft. azuredefender. Kubernetes" pro "installState": "installed".

    > [!TIP]
    > Může se zobrazit zpráva "installState": "čeká" během prvních několika minut.
    
1. Pokud stav zobrazuje **instalaci**, spusťte na počítači následující příkaz se souborem, který je `kubeconfig` odkazoval na váš cluster, a ověřte, že je pod názvem "azuredefender-XXXXX" ve stavu spuštěno:
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Simulace výstrah zabezpečení z Azure Defenderu pro Kubernetes

Úplný seznam podporovaných výstrah je k dispozici v [referenční tabulce všech výstrah zabezpečení v Azure Security Center](alerts-reference.md#alerts-akscluster).

1. Pokud chcete simulovat upozornění v programu Azure Defender, spusťte následující příkaz:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    Očekávaná odpověď je "žádný prostředek nebyl nalezen".

    Během 30 minut bude Azure Defender tuto aktivitu detekovat a aktivovat výstrahu zabezpečení.

1. V Azure Portal otevřete stránku výstrahy zabezpečení Azure Security Center a vyhledejte příslušnou výstrahu v příslušném prostředku:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Ukázka upozornění z Azure Defenderu pro Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Odebírá se rozšíření programu Azure Defender.

Rozšíření můžete odebrat pomocí Azure Portal, Azure CLI nebo REST API, jak je vysvětleno na kartách níže.

### <a name="azure-portal---arc"></a>[**Azure Portal – oblouk**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Odebrání rozšíření pomocí Azure Portal

1. Z Azure Portal otevřete Azure ARC.
1. V seznamu infrastruktura vyberte **clustery Kubernetes** a pak vyberte konkrétní cluster.
1. Otevřete stránku rozšíření. Jsou uvedena rozšíření v clusteru.
1. Vyberte cluster a vyberte **odinstalovat**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Odebrání rozšíření z clusteru Kubernetes s povoleným ARC." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Použití rozhraní příkazového řádku Azure k odebrání rozšíření programu Azure Defender

1. Odeberte rozšíření Kubernetes ARC pro Azure Defender pomocí následujících příkazů:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Odebrání rozšíření může trvat několik minut. Doporučujeme počkat, než se pokusíte ověřit, zda bylo úspěšné.

1. Chcete-li ověřit, zda bylo rozšíření úspěšně odebráno, spusťte následující příkazy:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Nemusíte mít žádné zpoždění v případě odstranění prostředku rozšíření z Azure Resource Manager. Potom ověřte, že v clusteru nejsou žádné lusky s názvem "azuredefender-XXXXX", spuštěním následujícího příkazu se souborem, který `kubeconfig` ukazuje na váš cluster: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Odstranění lusků může trvat několik minut.

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Odebrání rozšíření v programu Azure Defender pomocí REST API 

Pokud chcete rozšíření odebrat pomocí REST API, spusťte následující příkaz DELETE:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | V   | Požaduje se | Typ   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| ID předplatného | program | True     | řetězec | ID předplatného clusteru Kubernetes povoleného ARC |
| Skupina prostředků  | program | True     | řetězec | Skupina prostředků clusteru Kubernetes povoleného ARC  |
| Název clusteru    | program | True     | řetězec | Název clusteru Kubernetes s povoleným obloukem            |

Pro **ověřování** musí mít vaše hlavička nosný token (stejně jako ostatní rozhraní API Azure). Pokud chcete získat nosný token, spusťte následující příkaz:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

Dokončení požadavku může trvat několik minut.

---

## <a name="next-steps"></a>Další kroky

Tato stránka vysvětluje, jak nasadit rozšíření Azure Defender pro clustery Kubernetes s podporou ARC Azure. Přečtěte si další informace o službě Azure Defender a o funkcích zabezpečení kontejnerů Azure Security Center na následujících stránkách:

- [Zabezpečení kontejnerů ve službě Security Center](container-security.md)
- [Seznámení s Azure Defenderem pro Kubernetes](defender-for-kubernetes-introduction.md)
- [Ochrana úloh Kubernetes](kubernetes-workload-protections.md)
