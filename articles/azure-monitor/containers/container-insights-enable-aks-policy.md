---
title: Povolení doplňku pro monitorování AKS pomocí Azure Policy
description: Popisuje, jak povolit doplněk pro monitorování AKS pomocí vlastních zásad Azure.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713894"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Povolení doplňku pro monitorování AKS pomocí Azure Policy
Tento článek popisuje, jak povolit doplněk pro monitorování AKS pomocí vlastních zásad Azure. Vlastní zásadu pro monitorování doplňku lze přiřadit buď v rámci předplatného, nebo v oboru skupiny prostředků. Pokud je prostředí Azure Log Analytics Workspace a cluster AKS v různých předplatných, musí mít spravovaná identita používaná přiřazením zásad oprávnění požadovaná role u předplatných nebo nejméně u prostředku pracovního prostoru Log Analytics. Podobně platí, že pokud je zásada vymezená na skupinu prostředků, měla by mít spravovaná identita požadovaná oprávnění role v pracovním prostoru Log Analytics, pokud pracovní prostor není ve vybraném oboru skupiny prostředků.

Monitorovací doplněk vyžaduje následující role na spravované identitě, kterou používá Azure Policy:

 - [Azure-Kubernetes-Service-Přispěvatel – role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [Log-Analytics – Přispěvatel](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Vytvoření a přiřazení definice zásady pomocí Azure Portal

### <a name="create-policy-definition"></a>Vytvořit definici zásady

1. Stáhněte si vlastní definici zásad Azure a povolte doplněk AKS monitoring.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/DefinitionsV dialogovém okně Vytvoření definice zásad přejděte na a vytvořte definici zásady s následujícími podrobnostmi.
 
    - **Umístění definice**: vyberte předplatné Azure, kde se má definice zásady Uložit.
    - **Název**: *(Preview) AKS-monitoring-addon*
    - **Popis**: *vlastní zásady Azure povolující doplněk monitorování do clusterů Azure Kubernetes v zadaném oboru*
    - **Kategorie**: zvolte možnost *použít existující* a vybrat *Kubernetes* z rozevíracího seznamu.
    - **Pravidlo zásad**: Odeberte existující vzorová pravidla a zkopírujte obsah *azurepolicy.jske* stažení v kroku #1 výše.

### <a name="assign-policy-definition-to-specified-scope"></a>Přiřadit definici zásady do zadaného rozsahu

> [!NOTE]
>  Spravovaná identita se vytvoří automaticky a v definici zásady se jim přiřadí zadané role.

1. Vyberte položku definice zásad *(Preview) AKS monitoring* , kterou jste právě vytvořili.
4. Klikněte na *přiřadit** * a zadejte **obor** , kde se má zásada přiřadit. 
5. Klikněte na **Další** a zadejte ID prostředku pracovního prostoru Azure Log Analytics. ID prostředku by mělo být v tomto formátu `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>` .
6. V případě, že chcete použít zásady na existující clustery AKS ve vybraném oboru, vytvořte úkol nápravy.
7. Kliknutím na tlačítko **Revize + vytvořit** vytvořte přiřazení zásady.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Vytvoření a přiřazení definice zásad pomocí Azure CLI

### <a name="create-policy-definition"></a>Vytvořit definici zásady

1. Stáhněte si pravidla a soubory parametrů vlastní definice zásad Azure pomocí následujících příkazů:

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Vytvořte definici zásady pomocí následujícího příkazu:

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Přiřadit definici zásady do zadaného rozsahu

- Pomocí následujícího příkazu vytvořte přiřazení zásady:

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Policy](../../governance/policy/overview.md).
- Přečtěte si, jak [funguje řešení potíží se zabezpečením](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- Přečtěte si další informace o službě [Container Insights](./container-insights-overview.md).
- Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).