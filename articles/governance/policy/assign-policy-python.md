---
title: 'Rychlý Start: nové přiřazení zásad pomocí Pythonu'
description: V tomto rychlém startu pomocí Pythonu vytvoříte přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: aa67b3a2bae2beb62daa6e64871411ee760f802b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832190"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí Pythonu

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které _nedodržují předpisy_.

Knihovna Pythonu se používá ke správě prostředků Azure z příkazového řádku nebo ve skriptech. V této příručce se dozvíte, jak pomocí knihovny Pythonu vytvořit přiřazení zásady.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Přidání knihovny zásad

Chcete-li povolit Pythonu pro práci s Azure Policy, musí být knihovna přidána. Tato knihovna funguje bez ohledu na to, kde je možné použít Python, včetně [bash ve Windows 10](/windows/wsl/install-win10) nebo lokálně nainstalovaných.

1. Ověřte, že je nainstalovaný nejnovější Python (minimálně **3,8**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [Python.org](https://www.python.org/downloads/).

1. Ověřte, že je nainstalované nejnovější rozhraní příkazového řádku Azure (aspoň **2.5.1**). Pokud ještě není nainstalovaná, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Rozhraní příkazového řádku Azure vyžaduje, aby Python mohl používat **ověřování založené** na rozhraní CLI v následujících příkladech. Informace o dalších možnostech najdete v tématu [ověřování pomocí knihoven pro správu Azure pro Python](/azure/developer/python/azure-sdk-authenticate).

1. Ověření prostřednictvím rozhraní příkazového řádku Azure

   ```azurecli
   az login
   ```

1. V prostředí Python dle výběru nainstalujte požadované knihovny pro Azure Resource Graph:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Pokud je Python nainstalovaný pro všechny uživatele, musí být tyto příkazy spuštěné z konzoly se zvýšenými oprávněními.

1. Ověřte, zda byly knihovny nainstalovány. `azure-mgmt-policyinsights` by měla být **0.5.0** nebo vyšší, `azure-mgmt-resource` měla by být **9.0.0** nebo vyšší a `azure-cli-core` měla by být **2.5.0** nebo vyšší.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte **virtuální počítače auditu, které nepoužívají definici Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

Spuštěním následujícího kódu vytvořte nové přiřazení zásady:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

Předchozí příkazy používají následující informace:

Podrobnosti přiřazení:
- **DISPLAY_NAME** – zobrazovaný název přiřazení zásady. V takovém případě použijete _přiřazení audit virtuálních počítačů bez spravovaných disků_.
- **policy_definition_id** – cesta definice zásad na základě toho, kterou používáte k vytvoření přiřazení. V tomto případě se jedná o ID _virtuálních počítačů auditu definice zásad, které nepoužívají spravované disky_. V tomto příkladu je definice zásad integrovaná a cesta neobsahuje informace o skupině pro správu nebo předplatném.
- **Scope** – obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude uplatňovat. Může být v rozsahu od skupiny pro správu k individuálnímu prostředku. Nezapomeňte nahradit `{scope}` jedním z následujících způsobů:
  - Skupina pro správu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Formě `/subscriptions/{subscriptionId}`
  - Skupina prostředků: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Partner `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **Popis** – podrobnější vysvětlení toho, co zásada dělá nebo proč je přiřazena tomuto oboru.

Vytváření přiřazení:

- Rozsah – tento obor určuje, kde se přiřazení zásady uloží. Rozsah nastavený v podrobnostech přiřazení musí existovat v rámci tohoto oboru.
- Name – skutečný název přiřazení. V tomto příkladu je použitý název _audit-vm-manageddisks_.
- Přiřazení zásad – objekt Python **PolicyAssignment** vytvořený v předchozím kroku.

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Pomocí následujících informací identifikujte prostředky, které nedodržují předpisy přiřazení zásady, kterou jste vytvořili. Spusťte následující kód:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Nahraďte `{subscriptionId}` předplatným, pro které chcete zobrazit výsledky dodržování předpisů pro toto přiřazení zásady. Seznam dalších oborů a způsobů sumarizace dat najdete v tématu [metody stavu zásad](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Vaše výsledky budou vypadat přibližně jako v následujícím příkladu:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Výsledky se shodují s tím, co vidíte na kartě **Kompatibilita prostředků** v přiřazení zásady v zobrazení Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořené přiřazení, použijte následující příkaz:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Nahraďte `{scope}` stejným oborem, který jste použili k vytvoření přiřazení zásady.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazení definic zásad pro ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)
