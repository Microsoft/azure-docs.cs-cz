---
title: Ukázkový – Audit, pokud nejsou nainstalovány aplikace uvnitř virtuálních počítačů s Linuxem
description: Tato ukázka konfigurace zásad hosta iniciativy a definice auditovat, jestli nejsou určené aplikace nainstalované uvnitř virtuálních počítačů s Linuxem.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: b432d8557c4244d58c23e7b068874dd747f6249f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60545091"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>Ukázkový – Audit, pokud nejsou určené aplikace nainstalované uvnitř virtuálních počítačů s Linuxem

Této iniciativy hosta konfiguraci zásad auditování nainstalované zadaná aplikace uvnitř virtuálních počítačů s Linuxem. ID této integrované iniciativy je `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Všechny konfigurace hostovaného iniciativy se skládají z **auditu** a **deployIfNotExists** definic zásad. Přiřazuje se jen jednu z definic zásad způsobit, že konfigurace hostovaného nefunguje správně.

Můžete přiřadit tato ukázka pomocí:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Součásti iniciativy

To [hosta konfigurace](../concepts/guest-configuration.md) iniciativy se skládá z následujících zásad:

- [auditovat](#audit-definition) – auditování, že je nainstalovaná určitá aplikace uvnitř virtuálních počítačů s Linuxem
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) – nasazení virtuálního počítače rozšíření auditovat, jestli je nainstalovaná určitá aplikace uvnitř virtuálních počítačů s Linuxem
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definice iniciativy

Iniciativy je vytvořený spojením **auditu** a **deployIfNotExists** definice společně a [parametry iniciativy](#initiative-parameters). Toto je ve formátu JSON definice.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parametry iniciativ

| Název | Typ || Popis | |---|---|| ---| | applicationName | Řetězec | Názvy aplikací. Příklad: "python", "powershell" nebo čárkami oddělený seznam, jako je například "python, prostředí powershell". Použití \* pro porovnávání se zástupnými znaky, jako je třeba "power\*". |

Při vytváření přiřazení přes PowerShell nebo Azure CLI je možné předat hodnoty parametrů ve formátu JSON buď v řetězci, nebo prostřednictvím souboru pomocí parametru `-PolicyParameter` (PowerShell) nebo `--params` (Azure CLI).
PowerShell podporuje také parametr `-PolicyParameterObject`, který vyžaduje, aby se rutině předala zatřiďovací tabulka názvů a hodnot, kde **název** je název parametru a **hodnota** je jedna hodnota nebo pole hodnot, které se předávají během přiřazení.

V tomto příkladu parametru, instalaci aplikací _python_ a _powershellu_ se Audituje.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Pouze **deployIfNotExists** definice zásady používá parametry iniciativy.

### <a name="audit-definition"></a>definici audit

JSON definování pravidel **auditu** definice zásady.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>definice deployIfNotExists

JSON definování pravidel **deployIfNotExists** definice zásady.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**DeployIfNotExists** definice zásady definuje zásady byl ověřen v imagích Azure:

|Vydavatel |Nabídka |Skladová jednotka (SKU) |
|-|-|-|
|OpenLogic |CentOS\* |Všechny s výjimkou 6\* |
|RedHat |RHEL |Všechny s výjimkou 6\* |
|RedHat |osa | Vše |
|credativ |Debian | Všechny s výjimkou 7\* |
|SuSE |SLES\* |Všechny s výjimkou 11\* |
|Canonical| UbuntuServer |Všechny s výjimkou 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Vše |
|microsoft-dsvm |azureml |Vše |
|cloudera |cloudera-centos-os |Všechny s výjimkou 6\* |
|cloudera |cloudera-altus-centos-os |Vše |
|microsoft-ads |linux\* |Vše |
|microsoft-aks |Vše |Vše |
|AzureDatabricks |Vše |Vše |
|qubole-inc |Vše |Vše |
|datastax |Vše |Vše |
|Couchbase |Vše |Vše |
|scalegrid |Vše |Vše |
|Kontrolní bod |Vše |Vše |
|paloaltonetworks |Vše |Vše |

**Nasazení** předá část pravidla _installedApplication_ parametr konfigurace hostovaného agenta na virtuálním počítači. Tato konfigurace umožňuje agenta k provedení ověření a sestavu dodržování předpisů zpět **auditu** definice zásady.

## <a name="azure-portal"></a>portál Azure

Po **auditu** a **deployIfNotExists** definice jsou vytvořené na portálu, doporučuje se seskupit do [iniciativy](../concepts/definition-structure.md#initiatives) pro přiřazení.

### <a name="create-copy-of-audit-definition"></a>Vytvořit kopii definici audit

[![Nasazení ukázkové zásady Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![nasazení ukázkové zásady pro Azure Government](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Pomocí těchto tlačítek pro nasazení přes portál vytvoří kopii **auditu** definice zásady.
Bez spárovaném **deployIfNotExists** definice zásady, konfigurace hostovaného nebude fungovat správně.

### <a name="create-copy-of-deployifnotexists-definition"></a>Vytvoření kopie deployIfNotExists definice

[![Nasazení ukázkové zásady Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![nasazení ukázkové zásady pro Azure Government](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Pomocí těchto tlačítek pro nasazení přes portál vytvoří kopii **deployIfNotExists** definice zásady. Bez spárovaném **auditu** definice zásady, konfigurace hostovaného nebude fungovat správně.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

#### <a name="copy-and-assign-the-initiative"></a>Kopírování a přiřazení iniciativy

Tyto kroky vytvoří kopii k iniciativě, která zahrnuje integrované zásady pro oba **auditu** a **deployIfNotExists** a přiřadí iniciativa zaměřená na skupinu prostředků.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Zkopírujte a přiřadíte definici audit

Tyto kroky vytvoří kopii **auditu** definice a přiřaďte ho ke skupině prostředků. Tato definice nebude správně fungovat bez spárovaném **deployIfNotExists** definice také přiřazení.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Kopírování a přiřazení definice deployIfNotExists

Tyto kroky vytvoří kopii **deployIfNotExists** definice a přiřaďte ho ke skupině prostředků.
Tato definice nebude správně fungovat bez spárovaném **auditu** definice také přiřazení.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Pokud chcete odebrat předchozí přiřazení a definici, spusťte následující příkazy:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Vysvětlení Azure PowerShellu

Ve skriptech nasazení a odebrání se používají následující příkazy. Každý příkaz v následující tabulce odkazuje na příslušnou část dokumentace:

| Příkaz | Poznámky |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Vytvoří Azure Policy iniciativy. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Vytvoří definici zásady Azure. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Získá jednu skupinu prostředků. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Vytvoří nové přiřazení zásady Azure Policy pro iniciativy nebo definice. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Poskytuje existujícím přiřazení role na konkrétní instanční objekt. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Odebere stávající přiřazení Azure Policy. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Odebere iniciativy. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Odstraní definici. |

## <a name="next-steps"></a>Další postup

- Zkontrolujte další [ukázek Azure Policy](index.md).
- Další informace o [konfigurace hostovaného zásad Azure](../concepts/guest-configuration.md).
- Kontrola [struktura definic Azure Policy](../concepts/definition-structure.md).
