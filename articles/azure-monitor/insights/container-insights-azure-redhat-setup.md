---
title: Konfigurace Azure Red Hat OpenShift v3. x s Azure Monitor for Containers | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat monitorování clusteru Kubernetes s Azure Monitor hostovaným na Azure Red Hat OpenShift verze 3 a vyšší.
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 98ac5752e047c4f5f6db63d228bec7c47271aa00
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82166289"
---
# <a name="configure-azure-red-hat-openshift-v3-with-azure-monitor-for-containers"></a>Konfigurace Azure Red Hat OpenShift V3 pomocí Azure Monitor for Containers

Azure Monitor for Containers poskytuje bohatou monitorovací prostředí pro clustery Azure Kubernetes Service (AKS) a AKS Engine. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných v [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) verze 3 a nejnovější verzi verze 3, aby bylo možné dosáhnout podobného monitorování.

>[!NOTE]
>Podpora pro Azure Red Hat OpenShift je v současnosti funkcí ve verzi Public Preview.
>

Azure Monitor for Containers je možné povolit pro nové nebo jedno nebo více stávajících nasazení Azure Red Hat OpenShift pomocí následujících podporovaných metod:

- Pro existující cluster z Azure Portal nebo pomocí šablony Azure Resource Manager.
- Pro nový cluster pomocí šablony Azure Resource Manager nebo při vytváření nového clusteru pomocí rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Podporované a nepodporované funkce

Azure Monitor for Containers podporuje monitorování Azure Red Hat OpenShift, jak je popsáno v článku [Přehled](container-insights-overview.md) s výjimkou následujících funkcí:

- Živá data (Preview)
- [Shromážděte metriky](container-insights-update-metrics.md) z uzlů clusteru a lusky a ukládají je do databáze Azure Monitorch metrik.

## <a name="prerequisites"></a>Požadavky

- Pokud chcete povolit a přistupovat k funkcím v Azure Monitor pro kontejnery, minimálně musíte být členem role *přispěvatele* Azure v předplatném Azure a členem role [*přispěvatele Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics s nakonfigurovaným Azure monitor for Containers.

- Chcete-li zobrazit data monitorování, jste členem oprávnění role [*čtenář Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) s pracovním prostorem Log Analytics nakonfigurovaným Azure monitor for Containers.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Povolení pro nový cluster pomocí šablony Azure Resource Manager

Provedením následujících kroků nasadíte cluster Azure Red Hat OpenShift s povoleným monitorováním. Než budete pokračovat, přečtěte si kurz [Vytvoření clusteru Azure Red Hat OpenShift](../../openshift/tutorial-create-cluster.md#prerequisites) , který vám pomůže pochopit závislosti, které je potřeba nakonfigurovat, aby se vaše prostředí správně nastavilo.

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro nasazení clusteru s povoleným monitorováním a druhý obsahuje hodnoty parametrů, které nakonfigurujete, aby určovaly následující:

- ID prostředku clusteru Azure Red Hat OpenShift

- Skupina prostředků, ve které je cluster nasazen

- [ID tenanta Azure Active Directory](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) poznamenali po provedení kroků k vytvoření jednoho nebo již vytvořeného postupu.

- [ID klientské aplikace Azure Active Directory](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) poznamenali, když provedete kroky k vytvoření jednoho nebo jednoho již vytvořeného postupu.

- [Azure Active Directory tajný kód klienta](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) byl zaznamenán po provedení kroků k vytvoření jednoho nebo již vytvořeného postupu.

- [Skupina zabezpečení Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) se poznamenala po provedení kroků k vytvoření jednoho nebo nového vytvořeného postupu.

- ID prostředku existujícího pracovního prostoru Log Analytics.

- Počet hlavních uzlů, které se mají v clusteru vytvořit

- Počet výpočetních uzlů v profilu fondu agentů.

- Počet uzlů infrastruktury v profilu fondu agentů.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, přečtěte si téma:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

- [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.65 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Aby bylo možné povolit monitorování pomocí Azure PowerShell nebo rozhraní příkazového řádku, je třeba vytvořit pracovní prostor Log Analytics. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Stáhněte a uložte do místní složky, Azure Resource Manager šablony a souboru parametrů, a vytvořte tak cluster s doplňkem monitorování pomocí následujících příkazů:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Přihlášení k Azure

    ```azurecli
    az login    
    ```

    Pokud máte přístup k několika předplatným, `az account set -s {subscription ID}` spusťte `{subscription ID}` nahrazení pomocí předplatného, které chcete použít.

3. Vytvořte skupinu prostředků pro váš cluster, pokud ji ještě nemáte. Seznam oblastí Azure, které podporují OpenShift v Azure, najdete v tématu [podporované oblasti](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Upravte soubor parametrů JSON **newClusterWithMonitoringParam. JSON** a aktualizujte následující hodnoty:

    - *oblasti*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Následující krok nasadí cluster s povoleným monitorováním pomocí Azure CLI.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Výstup se podobá následujícímu:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Povolit pro existující cluster

Provedením následujících kroků povolíte monitorování clusteru Azure Red Hat OpenShift nasazeného v Azure. Můžete to provést z Azure Portal nebo pomocí dodaných šablon.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce Azure Portal nebo na domovské stránce vyberte možnost **Azure monitor**. V části **přehledy** vyberte **kontejnery**.

3. Na stránce **monitor – kontejnery** vyberte **nemonitorované clustery**.

4. V seznamu nemonitorovaných clusterů Najděte cluster v seznamu a klikněte na **Povolit**. Výsledky v seznamu můžete zjistit tak, že ve sloupci **typu clusteru**vyhledáte hodnotu **ARO** společnosti.

5. Pokud máte existující pracovní prostor Log Analytics v rámci stejného předplatného jako cluster, vyberte v rozevíracím seznamu na stránce **připojování k Azure monitor for Containers** .  
    Seznam předchází výchozí pracovní prostor a umístění, do kterého se cluster nasazuje v rámci předplatného.

    ![Povolit monitorování pro nemonitorované clustery](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro uložení dat monitorování z clusteru, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Nezapomeňte vytvořit pracovní prostor ve stejném předplatném, ke kterému je nasazený cluster RedHat OpenShift.

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="enable-using-an-azure-resource-manager-template"></a>Povolení použití šablony Azure Resource Manager

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro povolení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete, aby určovaly následující:

- ID prostředku clusteru Azure RedHat OpenShift.

- Skupina prostředků, ve které je cluster nasazen

- Pracovní prostor služby Log Analytics.

Pokud nejste obeznámeni s konceptem nasazení prostředků pomocí šablony, přečtěte si téma:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

- [Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete používat rozhraní příkazového řádku Azure, musíte nejdřív nainstalovat a používat rozhraní příkazového řádku (CLI). Musíte používat Azure CLI verze 2.0.65 nebo novější. Pro identifikaci vaší verze spusťte `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

Aby bylo možné povolit monitorování pomocí Azure PowerShell nebo rozhraní příkazového řádku, je třeba vytvořit pracovní prostor Log Analytics. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md), prostřednictvím [PowerShellu](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo v [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Stáhněte si šablonu a soubor parametrů a aktualizujte svůj cluster pomocí doplňku monitorování pomocí následujících příkazů:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Přihlášení k Azure

    ```azurecli
    az login    
    ```

    Pokud máte přístup k několika předplatným, `az account set -s {subscription ID}` spusťte `{subscription ID}` nahrazení pomocí předplatného, které chcete použít.

3. Zadejte předplatné clusteru Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Pro identifikaci umístění clusteru a ID prostředku spusťte následující příkaz:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Upravte soubor parametrů JSON **existingClusterParam. JSON** a aktualizujte hodnoty *araResourceId* a *araResoruceLocation*. Hodnota pro **workspaceResourceId** je úplné ID prostředku pracovního prostoru Log Analytics, který zahrnuje název pracovního prostoru.

6. Pokud ho chcete nasadit pomocí Azure CLI, spusťte následující příkazy:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Výstup se podobá následujícímu:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Další kroky

- Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků v clusteru RedHat OpenShift a úlohách, které se na nich běží, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.

- Ve výchozím nastavení agent kontejnerů shromažďuje protokoly kontejnerů stdout/stderr všech kontejnerů spuštěných ve všech oborech názvů kromě Kube-System. Pokud chcete nakonfigurovat kolekci protokolů kontejnerů specificky pro konkrétní obory názvů nebo obory názvů, zkontrolujte [konfiguraci agenta služby Container Insights](container-insights-agent-config.md) a nakonfigurujte požadovaná nastavení shromažďování dat na váš soubor konfigurace ConfigMap.

- Pokud si chcete vyřadit a analyzovat metriky Prometheus z clusteru, přečtěte si téma Konfigurace vyřazení [metrik Prometheus](container-insights-prometheus-integration.md) .

- Informace o tom, jak zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, najdete v tématu [Postup zastavení monitorování clusteru Azure Red Hat OpenShift](container-insights-optout-openshift.md).
