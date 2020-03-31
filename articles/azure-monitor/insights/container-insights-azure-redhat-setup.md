---
title: Konfigurace clusterů Azure Red Hat OpenShift pomocí Azure Monitoru pro kontejnery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nakonfigurovat monitorování clusteru Kubernetes pomocí Azure Monitor hostovaného na Azure Red Hat OpenShift.
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: c2fd3568be2c51296bb1377e91031ebfb7ca6ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275513"
---
# <a name="configure-azure-red-hat-openshift-clusters-with-azure-monitor-for-containers"></a>Konfigurace clusterů Azure Red Hat OpenShift pomocí Azure Monitoru pro kontejnery

Azure Monitor pro kontejnery poskytuje bohaté možnosti monitorování pro azure kubernetes service (AKS) a clustery AKS Engine. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných v [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) k dosažení podobné hojné možnosti monitorování.

>[!NOTE]
>Podpora Azure Red Hat OpenShift je funkce ve verzi Public Preview v tuto chvíli.
>

Azure Monitor pro kontejnery můžete povolit pro nové nebo jedno nebo více existujících nasazení Azure Red Hat OpenShift pomocí následujících podporovaných metod:

- Pro existující cluster z portálu Azure nebo pomocí šablony Azure Resource Manager.
- Pro nový cluster pomocí šablony Azure Resource Manager nebo při vytváření nového clusteru pomocí [azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Podporované a nepodporované funkce

Azure Monitor pro kontejnery podporuje monitorování Azure Red Hat OpenShift, jak je popsáno v článku [Přehled,](container-insights-overview.md) s výjimkou následujících funkcí:

- Živá data (náhled)
- [Shromažďujte metriky](container-insights-update-metrics.md) z uzlů a podů clusteru a ukládají je do databáze metrik Azure Monitoru

## <a name="prerequisites"></a>Požadavky

- Chcete-li povolit a získat přístup k funkcím v Azure Monitor u kontejnerů, minimálně musíte být členem role *Přispěvatel* Azure v předplatném Azure a členem role [*přispěvatele analýzy protokolů*](../platform/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics nakonfigurovaném pomocí Azure Monitor u kontejnerů.

- Chcete-li zobrazit data monitorování, jste členem oprávnění role [*čtečky Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) s pracovním prostorem Log Analytics nakonfigurované mandatle Azure Monitor pro kontejnery.

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Povolení nového clusteru pomocí šablony Azure Resource Manageru

Proveďte následující kroky k nasazení clusteru Azure Red Hat OpenShift s povoleným monitorováním. Než budete pokračovat, přečtěte si kurz [Vytvoření clusteru Azure Red Hat OpenShift,](../../openshift/tutorial-create-cluster.md#prerequisites) abyste pochopili závislosti, které je třeba nakonfigurovat, aby bylo vaše prostředí správně nastaveno.

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro nasazení clusteru s povoleným monitorováním a druhá obsahuje hodnoty parametrů, které nakonfigurujete pro určení následujících hodnot:

- ID prostředku clusteru Azure Red Hat OpenShift.

- Skupina prostředků, ve které je cluster nasazen.

- [ID klienta Azure Active Directory](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) zaznamenáno po provedení kroků k vytvoření jednoho nebo jednoho již vytvořeného.

- [ID klientské aplikace Azure Active Directory,](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) které bylo zaznamenáno po provedení kroků k vytvoření jednoho nebo jednoho již vytvořeného.

- [Tajný klíč klienta služby Azure Active Directory,](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) který byl zaznamenán po provedení kroků k vytvoření jednoho nebo jednoho již vytvořeného.

- [Skupina zabezpečení Azure AD](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) poznamenal po provedení kroků k vytvoření jednoho nebo jednoho již vytvořené.

- ID prostředku existujícího pracovního prostoru Analýzy protokolů.

- Počet hlavních uzlů, které chcete vytvořit v clusteru.

- Počet výpočetních uzlů v profilu fondu agenta.

- Počet uzlů infrastruktury v profilu fondu agenta.

Pokud nejste obeznámeni s konceptem nasazování prostředků pomocí šablony, přečtěte si:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

- [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.65 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Pracovní prostor Log Analytics musí být vytvořen před povolením monitorování pomocí Azure PowerShell nebo CLI. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), přes [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo na [webu Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Stáhněte a uložte do místní složky, šablony a souboru parametrů Azure Resource Manageru, a vytvořte cluster s doplňky pro monitorování pomocí následujících příkazů:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Přihlášení k Azure

    ```azurecli
    az login    
    ```

    Pokud máte přístup k více `az account set -s {subscription ID}` předplatných, spusťte nahrazení `{subscription ID}` předplatného, které chcete použít.

3. Pokud ještě nemáte skupinu prostředků pro cluster, vytvořte skupinu prostředků. Seznam oblastí Azure, které podporují OpenShift v Azure, najdete v [tématu Podporované oblasti](../../openshift/supported-resources.md#azure-regions).

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Upravte soubor parametru JSON **newClusterWithMonitoringParam.json** a aktualizujte následující hodnoty:

    - *Umístění*
    - *název_clusteru*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. Následující krok nasazuje cluster s povoleným monitorováním pomocí rozhraní příkazového příkazu Azure.

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    Výstup se podobá následující:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Povolení pro existující cluster

Pomocí následujících kroků povolte monitorování clusteru Azure Red Hat OpenShift nasazeného v Azure. Můžete to provést z portálu Azure nebo pomocí zadaných šablon.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. V nabídce Portál Azure nebo na domovské stránce vyberte **Azure Monitor**. V části **Přehledy** vyberte **Kontejnery**.

3. Na stránce **Monitor - kontejnery** vyberte **Nemonitorované clustery**.

4. V seznamu nesledovaných clusterů vyhledejte cluster v seznamu a klepněte na tlačítko **Povolit**. Výsledky v seznamu můžete identifikovat tak, že vyhledáte hodnotu **ARO** ve sloupci **TYP CLUSTERU**.

5. Na stránce **Onboarding to Azure Monitor for containers,** pokud máte existující pracovní prostor Log Analytics ve stejném předplatném jako cluster, vyberte ho z rozevíracího seznamu.  
    Seznam předem vybere výchozí pracovní prostor a umístění, do kterého je cluster nasazen v předplatném.

    ![Povolit monitorování nesledovaných clusterů](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Analýzy protokolů pro ukládání dat monitorování z clusteru, postupujte podle pokynů v [části Vytvoření pracovního prostoru Analýzy protokolů](../../azure-monitor/learn/quick-create-workspace.md). Nezapomeňte vytvořit pracovní prostor ve stejném předplatném, do kterého je nasazen cluster RedHat OpenShift.

Po povolení monitorování může trvat přibližně 15 minut, než budete moci zobrazit metriky stavu pro cluster.

### <a name="enable-using-an-azure-resource-manager-template"></a>Povolení použití šablony Azure Resource Manageru

Tato metoda zahrnuje dvě šablony JSON. Jedna šablona určuje konfiguraci pro povolení monitorování a druhá obsahuje hodnoty parametrů, které nakonfigurujete tak, aby určila následující:

- ID prostředku clusteru Azure RedHat OpenShift.

- Skupina prostředků, ve které je cluster nasazen.

- Pracovní prostor služby Log Analytics.

Pokud nejste obeznámeni s konceptem nasazování prostředků pomocí šablony, přečtěte si:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)

- [Nasazení prostředků pomocí šablon Správce prostředků a nastavení příkazového uživatelského příkazu Azure](../../azure-resource-manager/templates/deploy-cli.md)

Pokud se rozhodnete použít azure cli, musíte nejprve nainstalovat a použít příkazového příkazového příkazu místně. Musíte spouštět Azure CLI verze 2.0.65 nebo novější. Chcete-li identifikovat `az --version`verzi, spusťte aplikaci . Pokud potřebujete nainstalovat nebo upgradovat vázačitelné příkazy k Webu Azure, přečtěte si informace [o instalaci příkazového příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Pracovní prostor Log Analytics musí být vytvořen před povolením monitorování pomocí Azure PowerShell nebo CLI. Pokud chcete vytvořit pracovní prostor, můžete ho nastavit prostřednictvím [Azure Resource Manageru](../../azure-monitor/platform/template-workspace-configuration.md), přes [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)nebo na [webu Azure Portal](../../azure-monitor/learn/quick-create-workspace.md).

1. Stáhněte si soubor šablony a parametrů a aktualizujte cluster pomocí monitorovacího doplňku pomocí následujících příkazů:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Přihlášení k Azure

    ```azurecli
    az login    
    ```

    Pokud máte přístup k více `az account set -s {subscription ID}` předplatných, spusťte nahrazení `{subscription ID}` předplatného, které chcete použít.

3. Zadejte předplatné clusteru Azure RedHat OpenShift.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. K identifikaci umístění a ID prostředku clusteru spusťte následující příkaz:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Upravte soubor parametru JSON **existingClusterParam.json** a aktualizujte hodnoty *araResourceId* a *araResoruceLocation*. Hodnota pro **pracovní prostorResourceId** je úplné ID prostředku pracovního prostoru Analýzy protokolů, který obsahuje název pracovního prostoru.

6. Chcete-li nasadit pomocí příkazového příkazu k řešení Azure, spusťte následující příkazy:

    ```azurecli
    az group deployment create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    Výstup se podobá následující:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Další kroky

- Díky monitorování, které umožňuje shromažďovat využití stavu a prostředků vašeho clusteru RedHat OpenShift a úloh, které na nich běží, najdete informace o [tom, jak používat](container-insights-analyze.md) Azure Monitor pro kontejnery.

- Informace o tom, jak zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, najdete v [tématu Jak zastavit monitorování clusteru Azure Red Hat OpenShift](container-insights-optout-openshift.md).
