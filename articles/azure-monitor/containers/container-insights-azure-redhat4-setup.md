---
title: Konfigurace Azure Red Hat OpenShift v4. x se službou Container Insights | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat monitorování pro cluster Kubernetes s Azure Monitor hostovaným v Azure Red Hat OpenShift verze 4 nebo novější.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784636"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Konfigurace Azure Red Hat OpenShift v4. x s využitím kontejneru Insights

Služba Container Insights poskytuje bohatou monitorovací prostředí pro clustery Azure Kubernetes Service (AKS) a AKS Engines. Tento článek popisuje, jak dosáhnout podobného monitorování tím, že povolíte monitorování pro clustery Kubernetes hostované v [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) verze 4. x.

>[!NOTE]
>Podpora pro Azure Red Hat OpenShift je v současnosti funkcí ve verzi Public Preview.
>

Pomocí podporovaných metod popsaných v tomto článku můžete povolit službu Container Insights pro jedno nebo více existujících nasazení Azure Red Hat OpenShift v4. x.

V případě existujícího clusteru spusťte tento [skript bash v rozhraní příkazového řádku Azure CLI](/cli/azure/openshift#az_openshift_create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Podporované a nepodporované funkce

Container Insights podporuje monitorování Azure Red Hat OpenShift v4. x, jak je popsáno v tématu [Přehled služby Container Insights](container-insights-overview.md), s výjimkou následujících funkcí:

- Živá data (Preview)
- [Shromažďování metrik](container-insights-update-metrics.md) z uzlů clusteru a lusků a jejich ukládání do databáze Azure Monitorch metrik

## <a name="prerequisites"></a>Požadavky

- Azure CLI verze 2.0.72 nebo novější  

- Nástroj [Helm 3](https://helm.sh/docs/intro/install/) CLI

- Nejnovější verze rozhraní příkazového [řádku OpenShift](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash verze 4](https://www.gnu.org/software/bash/)

- Nástroj příkazového řádku [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- [Pracovní prostor služby Log Analytics](../logs/design-logs-deployment.md).

    Služba Container Insights podporuje pracovní prostor Log Analytics v oblastech uvedených v [produktech Azure podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor). Pokud chcete vytvořit vlastní pracovní prostor, můžete ho vytvořit prostřednictvím [Azure Resource Manager](../logs/resource-manager-workspace.md), prostřednictvím [PowerShellu](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../logs/quick-create-workspace.md).

- Aby bylo možné povolit a přistupovat k funkcím v kontejneru Insights, musíte mít minimálně roli *Přispěvatel* Azure v předplatném Azure a roli [*přispěvatele Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics, která je nakonfigurovaná pomocí služby Container Insights.

- Chcete-li zobrazit data monitorování, musíte mít roli [*čtenář Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics, která je nakonfigurována pomocí kontejneru Insights.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Povolit monitorování pro existující cluster

Pokud chcete povolit monitorování pro cluster Azure Red Hat OpenShift verze 4 nebo novější, který je nasazený v Azure pomocí poskytnutého skriptu bash, udělejte toto:

1. K Azure se přihlásíte spuštěním následujícího příkazu:

    ```azurecli
    az login
    ```

1. Stáhněte a uložte do místní složky skript, který konfiguruje cluster pomocí doplňku monitorování, spuštěním následujícího příkazu:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Pomocí pokynů v kurzu se připojte ke clusteru ARO v4 [: Připojte se ke clusteru Azure Red Hat OpenShift 4](../../openshift/tutorial-connect-cluster.md).


### <a name="integrate-with-an-existing-workspace"></a>Integrace s existujícím pracovním prostorem

V této části povolíte monitorování clusteru pomocí skriptu bash, který jste předtím stáhli. Chcete-li provést integraci s existujícím pracovním prostorem Log Analytics, začněte tím, že určíte úplné ID prostředku pracovního prostoru Log Analytics, který je požadován pro `logAnalyticsWorkspaceResourceId` parametr, a pak spustíte příkaz pro povolení doplňku monitorování v zadaném pracovním prostoru.

Pokud nemáte pracovní prostor, který byste mohli zadat, můžete přeskočit na oddíl [integrace s výchozím pracovním prostorem](#integrate-with-the-default-workspace) a nechat skript vytvořit nový pracovní prostor.

1. Seznam všech předplatných, ke kterým máte přístup, spustíte spuštěním následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup bude vypadat následovně:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Zkopírujte hodnotu pro **SubscriptionId**.

1. Přepněte do předplatného, které hostuje Log Analytics pracovní prostor spuštěním následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Spuštěním následujícího příkazu zobrazte seznam pracovních prostorů ve vašem předplatném ve výchozím formátu JSON:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID** pole.

1. Chcete-li povolit monitorování, spusťte následující příkaz. Nahraďte hodnoty `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` parametrů a.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Tady je příkaz, který musíte spustit, když jste naplnili 3 proměnné pomocí příkazů exportu:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="integrate-with-the-default-workspace"></a>Integrace s výchozím pracovním prostorem

V této části povolíte monitorování pro cluster Azure Red Hat OpenShift v4. x pomocí skriptu bash, který jste stáhli.

V tomto příkladu není nutné předem vytvořit nebo zadat existující pracovní prostor. Tento příkaz zjednodušuje proces vytvořením výchozího pracovního prostoru ve výchozí skupině prostředků v rámci předplatného clusteru, pokud už v oblasti neexistuje.

Výchozí vytvořený pracovní prostor má formát *DefaultWorkspace- \<GUID> - \<Region>*.  

Nahraďte hodnotu `azureAroV4ClusterResourceId` parametru.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Například:

' bash enable-monitoring.sh--Resource-ID $azureAroV 4ClusterResourceId 

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="enable-monitoring-from-the-azure-portal"></a>Povolit monitorování z Azure Portal

Zobrazení více clusterů ve službě Container Insights zvýrazní clustery Azure Red Hat OpenShift, které nemají povolené monitorování na kartě **nemonitorované clustery** . Možnost **Povolit** u vašeho clusteru neinicializuje připojování monitorování z portálu. Budete přesměrováni na tento článek, abyste mohli monitorování povolit ručně podle kroků uvedených výše v tomto článku.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V levém podokně nebo na domovské stránce vyberte **Azure monitor**.

1. V části **přehledy** vyberte **kontejnery**.

1. Na stránce **monitor – kontejnery** vyberte **nemonitorované clustery**.

1. V seznamu nemonitorovaných clusterů vyberte cluster a pak vyberte **Povolit**.

    Výsledky v seznamu můžete zjistit tak, že ve sloupci **typ clusteru** vyhledáte hodnotu **ARO** . Když vyberete **Povolit**, budete přesměrováni na tento článek.

## <a name="next-steps"></a>Další kroky

- Teď, když jste povolili monitorování ke shromáždění stavu a využití prostředků v clusteru RedHat OpenShift verze 4. x a zatížení, která jsou na nich spuštěná, se naučíte, [Jak používat službu](container-insights-analyze.md) Container Insights.

- Ve výchozím nastavení agent kontejnerů shromažďuje protokoly kontejnerů *stdout* a *stderr* všech kontejnerů, které jsou spuštěny ve všech oborech názvů kromě Kube-System. Pokud chcete nakonfigurovat kolekci protokolů kontejneru, která je specifická pro konkrétní obor názvů nebo obory názvů, Projděte si téma [Konfigurace agenta služby Container Insights](container-insights-agent-config.md) , ve kterém můžete nakonfigurovat nastavení shromažďování dat, která chcete pro konfigurační soubor *ConfigMap* .

- Pokud chcete vyřadit z clusteru metriky Prometheus a analyzovat je, přečtěte si téma [Konfigurace Prometheusch metrik](container-insights-prometheus-integration.md).

- Informace o tom, jak zastavit monitorování clusteru pomocí služby Container Insights, najdete v tématu [Postup zastavení monitorování clusteru Azure Red Hat OpenShift](./container-insights-optout-openshift-v3.md).
