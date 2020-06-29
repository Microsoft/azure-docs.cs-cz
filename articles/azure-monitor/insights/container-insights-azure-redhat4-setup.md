---
title: Konfigurace Azure Red Hat OpenShift v4. x s Azure Monitor for Containers | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat monitorování pro cluster Kubernetes s Azure Monitor hostovaným v Azure Red Hat OpenShift verze 4 nebo novější.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 7eee7ba6ba01679f72d1249058e4101b38d8461d
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2020
ms.locfileid: "85508006"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Konfigurace Azure Red Hat OpenShift v4. x s Azure Monitor for Containers

Azure Monitor for Containers poskytuje bohatou monitorovací prostředí pro clustery Azure Kubernetes Service (AKS) a AKS Engine. Tento článek popisuje, jak dosáhnout podobného monitorování tím, že povolíte monitorování pro clustery Kubernetes hostované v [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) verze 4. x.

>[!NOTE]
>Podpora pro Azure Red Hat OpenShift je v současnosti funkcí ve verzi Public Preview.
>

Pomocí podporovaných metod popsaných v tomto článku můžete povolit Azure Monitor pro kontejnery pro jedno nebo více existujících nasazení Azure Red Hat OpenShift v4. x.

V případě existujícího clusteru spusťte tento [skript bash v rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Podporované a nepodporované funkce

Azure Monitor for Containers podporuje monitorování Azure Red Hat OpenShift v4. x, jak je popsáno v tématu [přehled Azure monitor for Containers](container-insights-overview.md), s výjimkou následujících funkcí:

- Živá data (Preview)
- [Shromažďování metrik](container-insights-update-metrics.md) z uzlů clusteru a lusků a jejich ukládání do databáze Azure Monitorch metrik

## <a name="prerequisites"></a>Požadavky

- Azure CLI verze 2.0.72 nebo novější  

- Nástroj [Helm 3](https://helm.sh/docs/intro/install/) CLI

- [Bash verze 4](https://www.gnu.org/software/bash/)

- Nástroj příkazového řádku [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Pokud chcete povolit a přistupovat k funkcím v Azure Monitor pro kontejnery, musíte mít minimálně roli *Přispěvatel* Azure v předplatném Azure a roli [*přispěvatele Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics nakonfigurovanou Azure monitor for Containers.

- Chcete-li zobrazit data monitorování, je nutné mít v pracovním prostoru Log Analytics [*Log Analytics roli Čtenář*](../platform/manage-access.md#manage-access-using-azure-permissions) , nakonfigurovanou pomocí Azure monitor for Containers.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Povolit monitorování pro existující cluster

Pokud chcete povolit monitorování pro cluster Azure Red Hat OpenShift verze 4 nebo novější, který je nasazený v Azure pomocí poskytnutého skriptu bash, udělejte toto:

1. K Azure se přihlásíte spuštěním následujícího příkazu:

    ```azurecli
    az login
    ```

1. Stáhněte a uložte do místní složky skript, který konfiguruje cluster pomocí doplňku monitorování, spuštěním následujícího příkazu:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

1. Spusťte následující příkaz, abyste identifikovali *Kube kontext* clusteru po úspěšném *oC přihlášení* ke clusteru.

    `kubectl config current-context`
    
1. Zkopírujte hodnotu pro pozdější použití.

### <a name="integrate-with-an-existing-workspace"></a>Integrace s existujícím pracovním prostorem

V této části povolíte monitorování clusteru pomocí skriptu bash, který jste předtím stáhli. Chcete-li provést integraci s existujícím pracovním prostorem Log Analytics, začněte tím, že určíte úplné ID prostředku pracovního prostoru Log Analytics, který je požadován pro `workspaceResourceId` parametr, a pak spustíte příkaz pro povolení doplňku monitorování v zadaném pracovním prostoru. 

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

1. Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID**pole.

1. Chcete-li povolit monitorování, spusťte následující příkaz. Nahraďte hodnoty `azureAroV4ResourceId` `workspaceResourceId` parametrů a. 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <workspaceResourceId>`

    Příklad:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4 /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="integrate-with-the-default-workspace"></a>Integrace s výchozím pracovním prostorem

V této části povolíte monitorování pro cluster Azure Red Hat OpenShift v4. x pomocí skriptu bash, který jste stáhli. 

V tomto příkladu není nutné předem vytvořit nebo zadat existující pracovní prostor. Tento příkaz zjednodušuje proces vytvořením výchozího pracovního prostoru ve výchozí skupině prostředků v rámci předplatného clusteru, pokud už v oblasti neexistuje. 

Výchozí vytvořený pracovní prostor má formát *DefaultWorkspace- \<GUID> - \<Region> *.  

`bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

Například:

`bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

Zobrazení více clusterů v Azure Monitor pro kontejnery zvýrazňuje clustery Azure Red Hat OpenShift, které nemají povolené monitorování na kartě **nemonitorované clustery** . Možnost **Povolit** u vašeho clusteru neinicializuje připojování monitorování z portálu. Budete přesměrováni na tento článek, abyste mohli monitorování povolit ručně podle kroků uvedených výše v tomto článku.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V levém podokně nebo na domovské stránce vyberte **Azure monitor**. 

1. V části **přehledy** vyberte **kontejnery**.

1. Na stránce **monitor – kontejnery** vyberte **nemonitorované clustery**.

1. V seznamu nemonitorovaných clusterů vyberte cluster a pak vyberte **Povolit**. 

    Výsledky v seznamu můžete zjistit tak, že ve sloupci **typ clusteru** vyhledáte hodnotu **ARO** . Když vyberete **Povolit**, budete přesměrováni na tento článek.

## <a name="next-steps"></a>Další kroky

- Teď, když jste povolili monitorování pro shromažďování využití stavu a prostředků v clusteru RedHat OpenShift verze 4. x a zatížení, která jsou na nich spuštěná, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.

- Ve výchozím nastavení agent kontejnerů shromažďuje protokoly kontejnerů *stdout* a *stderr* všech kontejnerů, které jsou spuštěny ve všech oborech názvů kromě Kube-System. Pokud chcete nakonfigurovat kolekci protokolů kontejneru, která je specifická pro konkrétní obor názvů nebo obory názvů, Projděte si téma [Konfigurace agenta služby Container Insights](container-insights-agent-config.md) , ve kterém můžete nakonfigurovat nastavení shromažďování dat, která chcete pro konfigurační soubor *ConfigMap* .

- Pokud chcete vyřadit z clusteru metriky Prometheus a analyzovat je, přečtěte si téma [Konfigurace Prometheusch metrik](container-insights-prometheus-integration.md).

- Informace o tom, jak zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, najdete v tématu [Postup zastavení monitorování clusteru Azure Red Hat OpenShift](container-insights-optout-openshift.md).
