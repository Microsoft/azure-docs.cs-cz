---
title: Konfigurace Azure Red Hat OpenShift v4. x s Azure Monitor for Containers | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat monitorování clusteru Kubernetes s Azure Monitor hostovaným na Azure Red Hat OpenShift verze 4 a vyšší.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 872d842f02e19313940dfeba5258feb7d3799547
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888449"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Konfigurace Azure Red Hat OpenShift v4. x s Azure Monitor for Containers

Azure Monitor for Containers poskytuje bohatou monitorovací prostředí pro clustery Azure Kubernetes Service (AKS) a AKS Engine. Tento článek popisuje, jak povolit monitorování clusterů Kubernetes hostovaných v [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) verze 4. x, abyste dosáhli podobných možností monitorování.

>[!NOTE]
>Podpora pro Azure Red Hat OpenShift je v současnosti funkcí ve verzi Public Preview.
>

Azure Monitor pro kontejnery lze povolit pro jedno nebo více existujících nasazení Azure Red Hat OpenShift v4. x pomocí následujících podporovaných metod:

- Pro existující cluster pomocí poskytnutého bash skriptu a spuštěného v rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Podporované a nepodporované funkce

Azure Monitor for Containers podporuje monitorování Azure Red Hat OpenShift v4. x, jak je popsáno v článku [Přehled](container-insights-overview.md) s výjimkou následujících funkcí:

- Živá data (Preview)
- [Shromážděte metriky](container-insights-update-metrics.md) z uzlů clusteru a lusky a ukládají je do databáze Azure Monitorch metrik.

## <a name="prerequisites"></a>Požadavky

- Azure CLI verze 2.0.72 nebo vyšší

- [Helm 3](https://helm.sh/docs/intro/install/) CLI – nástroj

- [Bash verze 4](https://www.gnu.org/software/bash/)

- Nástroj příkazového řádku [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Pokud chcete povolit a přistupovat k funkcím v Azure Monitor pro kontejnery, minimálně musíte být členem role *přispěvatele* Azure v předplatném Azure a členem role [*přispěvatele Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) v pracovním prostoru Log Analytics s nakonfigurovaným Azure monitor for Containers.

- Chcete-li zobrazit data monitorování, jste členem oprávnění role [*čtenář Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) s pracovním prostorem Log Analytics nakonfigurovaným Azure monitor for Containers.

## <a name="enable-for-an-existing-cluster"></a>Povolit pro existující cluster

Proveďte následující kroky, abyste mohli sledovat cluster Azure Red Hat OpenShift verze 4 a vyšší, který je nasazený v Azure pomocí poskytnutého skriptu bash.

1. Přihlášení k Azure

    ```azurecli
    az login
    ```

2. Stáhněte a uložte skript do místní složky, která konfiguruje cluster pomocí doplňku monitorování pomocí následujících příkazů:

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Chcete-li identifikovat **Kube kontext** clusteru po úspěšném `oc login` přihlášení ke clusteru, spusťte příkaz `kubectl config current-context` a zkopírujte hodnotu.

### <a name="integrate-with-an-existing-workspace"></a>Integrace s existujícím pracovním prostorem

Následující krok umožňuje monitorovat cluster pomocí skriptu bash, který jste stáhli dříve. Chcete-li provést integraci s existujícím pracovním prostorem Log Analytics, proveďte následující kroky, abyste nejprve identifikovali úplné ID prostředku pracovního prostoru Log Analytics požadovaného pro daný `workspaceResourceId` parametr, a pak spustíte příkaz pro povolení doplňku monitorování v zadaném pracovním prostoru. Pokud nemáte pracovní prostor k zadání, můžete přeskočit na oddíl [integrace s výchozím pracovním prostorem](#integrate-with-default-workspace) a nechat skript vytvořit nový pracovní prostor.

1. Vypíše seznam všech předplatných, ke kterým máte přístup, pomocí následujícího příkazu:

    ```azurecli
    az account list --all -o table
    ```

    Výstup bude vypadat přibližně takto:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Zkopírujte hodnotu pro **SubscriptionId**.

2. Přepněte do předplatného hostujícího Log Analytics pracovní prostor pomocí následujícího příkazu:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Následující příklad zobrazí seznam pracovních prostorů v předplatných ve výchozím formátu JSON.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Ve výstupu vyhledejte název pracovního prostoru a zkopírujte úplné ID prostředku, které Log Analytics pracovní prostor pod **ID**pole.

4. Spusťte následující příkaz pro povolení monitorování a nahraďte hodnotu `workspaceResourceId` `azureAroV4ResourceIdparameter` parametrů a: 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <workspaceResourceId>`

    Příklad:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4 /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="integrate-with-default-workspace"></a>Integrace s výchozím pracovním prostorem

Následující krok umožňuje sledovat cluster Azure Red Hat OpenShift v4. x pomocí skriptu bash, který jste stáhli. V tomto příkladu nemusíte pro každý vytvořit nebo zadat existující pracovní prostor. Tento příkaz zjednodušuje proces vytvořením výchozího pracovního prostoru ve výchozí skupině prostředků v rámci předplatného clusteru, pokud ještě neexistuje v této oblasti. Výchozí vytvořený pracovní prostor se podobá formátu *DefaultWorkspace- \<GUID> - \<Region> *.  

`bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

Příklad:

`bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/0fb60ef2-03cc-4290-b595-e71108e8f4ce/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Po povolení monitorování může trvat přibližně 15 minut, než budete moct zobrazit metriky stavu clusteru.

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

Zobrazení více clusterů v Azure Monitor pro kontejnery zvýrazňuje clustery Azure Red Hat OpenShift, které nemají povolené monitorování na kartě **nemonitorované clustery** . Možnost **Povolit** vedle vašeho clusteru neinicializuje připojování monitorování z portálu. Budete přesměrováni do tohoto článku a ručně povolíte monitorování podle kroků uvedených výše v tomto článku.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce Azure Portal nebo na domovské stránce vyberte možnost **Azure monitor**. V části **přehledy** vyberte **kontejnery**.

3. Na stránce **monitor – kontejnery** vyberte **nemonitorované clustery**.

4. V seznamu nemonitorovaných clusterů Najděte cluster v seznamu a klikněte na **Povolit**. Výsledky v seznamu můžete zjistit tak, že ve sloupci **typu clusteru**vyhledáte hodnotu **ARO** společnosti. Po kliknutí na **Povolit**budete přesměrováni na tento článek.

## <a name="next-steps"></a>Další kroky

- Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků ve službě RedHat OpenShift a úlohách, které se na nich běží, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.

- Ve výchozím nastavení agent kontejnerů shromažďuje protokoly kontejnerů stdout/stderr všech kontejnerů spuštěných ve všech oborech názvů kromě Kube-System. Pokud chcete nakonfigurovat kolekci protokolů kontejnerů specificky pro konkrétní obory názvů nebo obory názvů, zkontrolujte [konfiguraci agenta služby Container Insights](container-insights-agent-config.md) a nakonfigurujte požadovaná nastavení shromažďování dat na váš soubor konfigurace ConfigMap.

- Pokud si chcete vyřadit a analyzovat metriky Prometheus z clusteru, přečtěte si téma Konfigurace vyřazení [metrik Prometheus](container-insights-prometheus-integration.md) .

- Informace o tom, jak zastavit monitorování clusteru pomocí Azure Monitor pro kontejnery, najdete v tématu [Postup zastavení monitorování clusteru Azure Red Hat OpenShift](container-insights-optout-openshift.md).
