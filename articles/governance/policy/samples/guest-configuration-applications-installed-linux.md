---
title: Ukázka – audit chybějících aplikací na virtuálních počítačích se systémem Linux
description: Tento ukázkový konfigurační iniciativu a definice hosta zásad, pokud zadané aplikace nejsou nainstalovány v rámci virtuálních počítačů se systémem Linux.
ms.date: 05/02/2019
ms.topic: sample
ms.openlocfilehash: 0789b7f408c1f3eea000bfb2fc21ddf5feff790c
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076215"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Ukázka – audit, pokud zadané aplikace nejsou nainstalované v rámci virtuálních počítačů se systémem Linux

Tato iniciativa konfigurace hosta zásady vytvoří událost auditu, když zadané aplikace nejsou nainstalovány v rámci virtuálních počítačů se systémem Linux. ID této integrované iniciativy je `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Všechny iniciativy konfigurace hosta se skládají z definic zásad **auditu** a **deployIfNotExists** . Když se přiřadí jenom jedna z definic zásad, nefunguje správně konfigurace hostů.

Tuto ukázku můžete přiřadit pomocí:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Součásti iniciativy

Tato iniciativa [Konfigurace hosta](../concepts/guest-configuration.md) se skládá z následujících zásad:

- [audit](#audit-definition) -audit při instalaci aplikací do virtuálních počítačů se systémem Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) – nasazení rozšíření virtuálního počítače pro audit, pokud se aplikace neinstalují do virtuálních počítačů se systémem Linux
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Definice iniciativy

Iniciativa je vytvořena připojením k definicím **audit** a **DeployIfNotExists** společně a [parametry iniciativy](#initiative-parameters). Toto je JSON definice.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Parametry iniciativ

|Název |Typ |Popis |
|---|---|---|
|ApplicationName |Řetězec |Názvy aplikací. Příklad: "Python", "PowerShell" nebo čárkami oddělený seznam, jako je například Python, PowerShell. Použijte \* pro spárování se zástupnými znaky, jako je Power\*. |

Při vytváření přiřazení přes PowerShell nebo Azure CLI je možné předat hodnoty parametrů ve formátu JSON buď v řetězci, nebo prostřednictvím souboru pomocí parametru `-PolicyParameter` (PowerShell) nebo `--params` (Azure CLI).
PowerShell podporuje také parametr `-PolicyParameterObject`, který vyžaduje, aby se rutině předala zatřiďovací tabulka názvů a hodnot, kde **název** je název parametru a **hodnota** je jedna hodnota nebo pole hodnot, které se předávají během přiřazení.

V tomto příkladu parametru je instalace aplikací _Python_ a _PowerShell_ auditována.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Jenom definice zásad **deployIfNotExists** využívá parametry iniciativy.

### <a name="audit-definition"></a>definice auditu

JSON definující pravidla definice zásad **auditu** .

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>definice deployIfNotExists

JSON definující pravidla definice zásad **deployIfNotExists**

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

Definice zásad **deployIfNotExists** definuje image Azure, na kterých byla zásada ověřena:

|Vydavatel |Nabídka |Skladová položka |
|-|-|-|
|OpenLogic |CentOS\* |Vše kromě 6\* |
|RedHat |RHEL |Vše kromě 6\* |
|RedHat |osa | Vše |
|credativ |Debian | Vše kromě 7\* |
|SuSE |SLES\* |Vše kromě 11\* |
|Canonical| UbuntuServer |Vše kromě 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Vše |
|microsoft-dsvm |azureml |Vše |
|Cloudera |cloudera-centos-os |Vše kromě 6\* |
|Cloudera |cloudera-altus-centos-os |Vše |
|microsoft-ads |linux\* |Vše |
|microsoft-aks |Vše |Vše |
|AzureDatabricks |Vše |Vše |
|qubole – Inc |Vše |Vše |
|DataStax |Vše |Vše |
|Couchbase |Vše |Vše |
|scalegrid |Vše |Vše |
|kontrolní bod |Vše |Vše |
|paloaltonetworks |Vše |Vše |

Část **nasazení** pravidla předá parametr _installedApplication_ k agentovi konfigurace hosta na virtuálním počítači. Tato konfigurace umožňuje agentovi provádět ověřování a nahlásit dodržování předpisů zpět pomocí definice zásad **auditu** .

## <a name="azure-portal"></a>portál Azure

Po vytvoření **auditu** a definice **deployIfNotExists** na portálu je doporučujeme seskupit je do [iniciativy](../concepts/definition-structure.md#initiatives) pro přiřazení.

### <a name="create-copy-of-audit-definition"></a>Vytvořit kopii definice auditu

[![nasazení ukázky zásad do azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![nasazení ukázky zásad do Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Použití těchto tlačítek k nasazení prostřednictvím portálu vytvoří kopii definice zásad **auditu** .
Bez párové definice zásad **deployIfNotExists** nebude konfigurace hostů správně fungovat.

### <a name="create-copy-of-deployifnotexists-definition"></a>Vytvořit kopii definice deployIfNotExists

[![nasazení ukázky zásad do azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![nasazení ukázky zásad do Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Použití těchto tlačítek k nasazení prostřednictvím portálu vytvoří kopii definice zásady **deployIfNotExists** . Bez definice spárované zásady **auditu** nebude konfigurace hostů správně fungovat.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Nasazení pomocí Azure PowerShellu

#### <a name="copy-and-assign-the-initiative"></a>Kopírovat a přiřadit iniciativu

Tyto kroky vytvoří kopii iniciativy zahrnující předdefinované zásady pro **audit** i **deployIfNotExists** a přiřadí iniciativu ke skupině prostředků.

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

#### <a name="copy-and-assign-the-audit-definition"></a>Zkopírování a přiřazení definice auditu

Tyto kroky vytvoří kopii definice **auditu** a přiřadí ji do skupiny prostředků. Tato definice nebude správně fungovat, aniž by se přidělila i spárovaná definice **deployIfNotExists** .

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

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Zkopírování a přiřazení definice deployIfNotExists

Tyto kroky vytvoří kopii definice **deployIfNotExists** a přiřadí ji do skupiny prostředků.
Tato definice nebude fungovat správně, aniž by se přidělila i spárovaná definice **auditu** .

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

| Příkaz | Poznámky: |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Vytvoří Azure Policy iniciativu. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Vytvoří definici Azure Policy. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Získá jednu skupinu prostředků. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Vytvoří nové přiřazení Azure Policy pro podnět nebo definici. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Poskytuje existující přiřazení role ke konkrétnímu objektu zabezpečení. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Odebere stávající přiřazení Azure Policy. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Odstraní iniciativu. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Odebere definici. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další [ukázky Azure Policy](index.md).
- Přečtěte si další informace o [Azure Policy konfiguraci hostů](../concepts/guest-configuration.md).
- Kontrola [Azure Policy struktury definice](../concepts/definition-structure.md).
