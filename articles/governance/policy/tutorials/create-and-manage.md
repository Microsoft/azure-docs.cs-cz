---
title: 'Tutorial: Build policies to enforce compliance'
description: In this tutorial, you use policies to enforce standards, control costs, maintain security, and impose enterprise wide design principles.
ms.date: 02/04/2019
ms.topic: tutorial
ms.openlocfilehash: 38c41c94600da1f028c7857febc7de1eef2436ae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216899"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Create and manage policies to enforce compliance

Pochopení způsobu, jakým se v Azure vytvářejí a spravují zásady, je důležité pro zajištění souladu s firemními standardy a smlouvami o úrovni služeb. V tomto kurzu se dozvíte, jak pomocí služby Azure Policy provádět některé běžné úlohy související s vytvářením, přiřazováním a správou zásad v rámci celé organizace, jako například:

> [!div class="checklist"]
> - Přiřazení zásady vynucující podmínku u prostředků, které vytvoříte v budoucnu
> - Vytvoření a přiřazení definice iniciativy pro sledování dodržování předpisů u několika prostředků
> - Řešení problému s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem
> - Implementace nové zásady v rámci celé organizace

Pokud chcete přiřadit zásadu pro identifikaci aktuálního stavu dodržování předpisů u vašich existujících prostředků, postup najdete v článcích Rychlý start. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="assign-a-policy"></a>Přiřazení zásady

Prvním krokem při vynucování dodržování předpisů pomocí služby Azure Policy je přiřazení definice zásady. Definice zásady definuje, za jakých podmínek se zásada vynucuje a jaký účinek se má projevit. V tomto příkladu přiřadíte předdefinovanou definici zásady *Vyžadovat SQL Server verze 12.0*, která bude vynucovat podmínku, že všechny databáze SQL Serveru musí být verze 12.0, aby dodržovaly předpisy.

1. Go to the Azure portal to assign policies. Search for and select **Policy**.

   ![Search for Policy in the search bar](../media/create-and-manage/search-policy.png)

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   ![Select Assignments from Policy Overview page](../media/create-and-manage/select-assignments.png)

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   ![Assign a policy definition from Assignments page](../media/create-and-manage/select-assign-policy.png)

1. On the **Assign Policy** page, select the **Scope** by selecting the ellipsis and selecting either a management group or subscription. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Then select **Select** at the bottom of the **Scope** page.

   V tomto příkladu se používá předplatné **Contoso**. Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Můžete nastavit filtr pro **Typ** definic zásad na *Předdefinované* a zobrazit všechny definice zásad a přečíst si jejich popisy.

1. Vyberte zásadu **Vyžadovat SQL Server verze 12.0**. If you can't find it right away, type **require sql server** into the search box and then press ENTER or select out of the search box. Select **Select** at the bottom of the **Available Definitions** page once you have found and selected the policy definition.

   ![Use search filter to locate a policy](../media/create-and-manage/select-available-definition.png)

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. Pro účely tohoto příkladu ponechte název *Vyžadovat SQL Server verze 12.0*. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   **Assigned by** is automatically filled based on who is logged in. Toto pole je volitelné, takže do něj můžete zadávat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. This box _must_ be checked when the policy or initiative being assigned includes a policy with the [deployIfNotExists](../concepts/effects.md#deployifnotexists) effect. As the policy used for this tutorial doesn't, leave it blank. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. Vyberte **Přiřadit**.

## <a name="implement-a-new-custom-policy"></a>Implementace nové vlastní zásady

Teď, když jste přiřadili předdefinovanou definici zásady, můžete se službou Azure Policy provádět další akce. Next, create a new custom policy to save costs by validating that VMs created in your environment can't be in the G series. Díky tomu se zamítnou všechny žádosti uživatelů ve vaší organizaci o vytvoření virtuálního počítače v řadě G Series.

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   ![Definition page under Authoring group](../media/create-and-manage/definition-under-authoring.png)

1. V horní části stránky vyberte **+ Definice zásady**. This button opens to the **Policy definition** page.

1. Zadejte následující informace:

   - Skupina pro správu nebo předplatné, ve kterém je definice zásady uložená. Výběr proveďte pomocí tří teček v části **Umístění definice**.

     > [!NOTE]
     > Pokud se chystáte tuto definici zásady použít pro více předplatných, umístěním musí být skupina pro správu obsahující předplatná, ke kterým zásadu přiřadíte. Totéž platí i pro definici iniciativy.

   - Název definice zásady – *Vyžadovat nižší skladové položky virtuálních počítačů než G Series*.
   - Popis účelu definice zásady – *Tato definice zásady za účelem snížení nákladů vynucuje, aby všechny virtuální počítače vytvořené v tomto oboru měly skladové položky nižší než G Series.*
   - Zvolte některou z existujících možností (například _Compute_) nebo pro tuto definici zásady vytvořte novou kategorii.
   - Zkopírujte následující kód JSON a pak v něm podle potřeby aktualizujte:
      - Parametry zásady.
      - Pravidla a podmínky zásady, v tomto případě – velikost skladové položky virtuálního počítače se rovná řadě G Series.
      - Účinek zásady, v tomto případě – **Deny** (Zamítnutí).

   Tady je ukázka kódu JSON. Vložte svůj upravený kód na web Azure Portal.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   The *field* property in the policy rule must be one of the following values: Name, Type, Location, Tags, or an alias. Příkladem aliasu může být `"Microsoft.Compute/VirtualMachines/Size"`.

   Další ukázky zásad Azure najdete v [ukázkách pro Azure Policy](../samples/index.md).

1. Vyberte **Save** (Uložit).

## <a name="create-a-policy-definition-with-rest-api"></a>Vytvoření definice zásady pomocí rozhraní REST API

You can create a policy with the REST API for Azure Policy Definitions. Toto rozhraní API umožňuje vytvářet a odstraňovat definice zásad a získávat informace o existujících definicích. Pokud chcete vytvořit definici zásady, použijte následující příklad:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Přiložte podobný text žádosti jako v následujícím příkladu:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Vytvoření definice zásady pomocí PowerShellu

Before proceeding with the PowerShell example, make sure you've installed the latest version of the Azure PowerShell Az module.

Definici zásady můžete vytvořit pomocí rutiny `New-AzPolicyDefinition`.

Pokud chcete vytvořit definici zásady ze souboru, předejte cestu k souboru. Pro externí soubor použijte následující příklad:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Pro místní soubor použijte následující příklad:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Pokud chcete vytvořit definici zásady s vloženým pravidlem, použijte následující příklad:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Výstup se ukládá v objektu `$definition`, který se používá při přiřazování zásady. Následující příklad vytvoří definici zásady zahrnující parametry:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Zobrazení definic zásad pomocí PowerShellu

Pokud chcete zobrazit všechny definice zásad ve svém předplatném, použijte následující příkaz:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Příkaz vrátí všechny dostupné definice zásad, včetně předdefinovaných zásad. Všechny zásady se vrátí v následujícím formátu:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Vytvoření definice zásady pomocí Azure CLI

V Azure CLI můžete vytvořit definici zásady pomocí příkazu policy definition. Pokud chcete vytvořit definici zásady s vloženým pravidlem, použijte následující příklad:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Zobrazení definic zásad pomocí Azure CLI

Pokud chcete zobrazit všechny definice zásad ve svém předplatném, použijte následující příkaz:

```azurecli-interactive
az policy definition list
```

Příkaz vrátí všechny dostupné definice zásad, včetně předdefinovaných zásad. Všechny zásady se vrátí v následujícím formátu:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Vytvoření a přiřazení definice iniciativy

Pomocí definice iniciativy můžete seskupit několik definic zásad za účelem dosažení jednoho zastřešujícího cíle. An initiative evaluates resources within scope of the assignment for compliance to the included policies. Další informace o definicích iniciativ najdete v tématu [Přehled služby Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Vytvoření definice iniciativy

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   ![Select definition from the Definitions page](../media/create-and-manage/definition-under-authoring.png)

1. V horní části stránky vyberte **+ Definice iniciativy** a otevřete stránku **Definice iniciativy**.

   ![Review initiative definition page](../media/create-and-manage/initiative-definition.png)

1. Pomocí tří teček **Umístění definice** vyberte skupinu pro správu nebo předplatné, kam se definice uloží. Pokud jste na předchozí stránce omezili obor na jednu skupinu pro správu nebo jedno předplatné, **Umístění definice** se vyplní automaticky.

1. Zadejte **Název** a **Popis** iniciativy.

   This example validates that resources are in compliance with policy definitions about getting secure. Pojmenujte iniciativu **Zajištění zabezpečení** a nastavte popis na: **Tato iniciativa byla vytvořená za účelem zpracování všech definic zásad souvisejících se zabezpečením prostředků**.

1. V části **Kategorie** zvolte některou z existujících možností nebo vytvořte novou kategorii.

1. Projděte seznam **Dostupné definice** (pravá polovina stránky **Definice iniciativy**) a vyberte definice zásad, které chcete přidat do této iniciativy. For the **Get secure** initiative, add the following built-in policy definitions by selecting the **+** next to the policy definition information or selecting a policy definition row and then the **+ Add** option in the details page:

   - Vyžadovat SQL Server verze 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   After selecting the policy definition from the list, it's added under **Policies and Parameters**.

   ![Review initiative definition parameters](../media/create-and-manage/initiative-definition-2.png)

1. If a policy definition being added to the initiative has parameters, they're shown under the policy name in the **Policies and Parameters** area. _Hodnotu_ je možné nastavit na možnost Nastavit hodnotu (pevně zakódovaná pro všechna přiřazení této iniciativy) nebo Použít parametr iniciativy (nastaví se při každém přiřazení iniciativy). If 'Set value' is selected, the drop-down to the right of _Values_ allows entering or selecting the value(s). Pokud vyberete možnost Použít parametr iniciativy, zobrazí se část **Parametry iniciativy**, kde můžete definovat parametr, který se nastaví během přiřazení iniciativy. Povolené hodnoty pro tento parametr iniciativy můžou dále omezit možnosti nastavení během přiřazení iniciativy.

   ![Change initiative definition parameters from allowed values](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > U některých parametrů `strongType` není možné automaticky určit seznam hodnot. V těchto případech se napravo od řádku parametru zobrazí tři tečky. Selecting it opens the 'Parameter scope (&lt;parameter name&gt;)' page. Na této stránce vyberte předplatné, které chcete použít k zadání možností hodnot. Tento obor parametru se používá pouze během vytváření definice iniciativy a nemá žádný vliv na vyhodnocování zásad ani na obor iniciativy po přiřazení.

1. Vyberte **Save** (Uložit).

### <a name="assign-an-initiative-definition"></a>Přiřazení definice iniciativy

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

1. Vyhledejte definici iniciativy **Zajištění zabezpečení**, kterou jste vytvořili dříve, a vyberte ji. V horní části stránky vyberte **Přiřadit** a otevřete stránku **Zajištění zabezpečení: Přiřadit iniciativu**.

   ![Assign a definition from Initiative definition page](../media/create-and-manage/assign-definition.png)

   You can also right-click on the selected row or select the ellipsis at the end of the row for a contextual menu. Pak vyberte **Přiřadit**.

   ![Alternative options for an initiative](../media/create-and-manage/select-right-click.png)

1. Vyplňte stránku **Zajištění zabezpečení: Přiřadit iniciativu** zadáním následujících ukázkových údajů. Můžete použít vlastní údaje.

   - Obor: Výchozím oborem se stane skupina pro správu nebo předplatné, kam jste iniciativu uložili.
     Obor můžete změnit a přiřadit tak iniciativu k předplatnému nebo ke skupině prostředků v rámci umístění pro uložení.
   - Vyloučení: Můžete nakonfigurovat jakékoli prostředky v rámci oboru, na které se nebude přiřazení iniciativy vztahovat.
   - Název definice a přiřazení iniciativy: Zajištění zabezpečení (předem se vyplní název přiřazované iniciativy).
   - Popis: Toto přiřazení iniciativy je přizpůsobené k vynucování této skupiny definic zásad.
   - Přiřadil: Automaticky se vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadávat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. This box _must_ be checked when the policy or initiative being assigned includes a policy with the [deployIfNotExists](../concepts/effects.md#deployifnotexists) effect. As the policy used for this tutorial doesn't, leave it blank. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. Vyberte **Přiřadit**.

## <a name="check-initial-compliance"></a>Kontrola počátečního dodržování předpisů

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů**.

1. Locate the **Get Secure** initiative. It's likely still in _Compliance state_ of **Not started**.
   Select the initiative to get full details on the progress of the assignment.

   ![Initiative compliance page - evaluations not started](../media/create-and-manage/compliance-status-not-started.png)

1. Po dokončení přiřazení iniciativy se na stránce Dodržování předpisů aktualizuje _Stav dodržování předpisů_ na **Vyhovuje**.

   ![Initiative compliance page- resources compliant](../media/create-and-manage/compliance-status-compliant.png)

1. Selecting on any policy on the initiative compliance page opens the compliance details page for the policy. Tato stránka obsahuje podrobnosti o dodržování předpisů na úrovni prostředku.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Vyloučení prostředku nedodržujícího předpisy nebo zamítnutého prostředku s využitím vyloučení

Ve výše uvedeném příkladu se po přiřazení definice zásady vyžadující SQL Server verze 12.0 zamítne vytvoření SQL Serveru s jinou verzí než 12.0. In this section, you walk through resolving a denied request to create a SQL server by creating an exclusion on a single resource group. Vyloučení zabrání vynucování zásady (nebo iniciativy) pro příslušný prostředek.
V následujícím příkladu je v jedné skupině prostředků povolená jakákoli verze SQL Serveru. Vyloučení se může vztahovat na předplatné nebo skupinu prostředků nebo ho můžete zúžit na jednotlivé prostředky.

A deployment prevented by an assigned policy or initiative can be viewed in two locations:

- On the resource group targeted by the deployment: Select **Deployments** in the left side of the page, then select the **Deployment Name** of the failed deployment. U zamítnutého prostředku je uvedený stav _Zakázáno_. To determine the policy or initiative and assignment that denied the resource, select **Failed. Click here for details ->** on the Deployment Overview page.
  Na pravé straně stránky se otevře okno s informacemi o chybě. Under **Error Details** are the GUIDs of the related policy objects.

  ![Nasazení zamítnuté přiřazením zásady](../media/create-and-manage/rg-deployment-denied.png)

- On the Azure Policy page: Select **Compliance** in the left side of the page and select the **Require SQL Server version 12.0** policy. Na stránce, která se otevře, se zobrazí zvýšení počtu **Zamítnutí**. Under the **Events** tab, you would also see who tried the deployment that was denied by the policy.

  ![Přehled dodržování předpisů přiřazené zásady](../media/create-and-manage/compliance-overview.png)

In this example, Trent Baker, one of Contoso's Sr. Virtualization specialists, was doing required work. We need to grant Trent an exception, but we don't want the non-version 12.0 SQL servers in just any resource group. Vytvořili jsme novou skupinu prostředků **SQLServers_Excluded** a teď jí udělíme výjimku pro toto přiřazení zásady.

### <a name="update-assignment-with-exclusion"></a>Aktualizace přiřazení o vyloučení

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Přiřazení**.

1. Projděte všechna přiřazení zásad a otevřete přiřazení *Vyžadovat SQL Server verze 12.0*.

1. Set the **Exclusion** by selecting the ellipsis and selecting the resource group to exclude, *SQLServers_Excluded* in this example.

   ![Add an excluded resource group to the policy assignment](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > V závislosti na zásadě a jejím účinku je možné udělit vyloučení také konkrétním prostředkům v rámci skupiny prostředků v oboru přiřazení. Vzhledem k tomu, že se v tomto kurzu použil účinek **Zamítnutí**, nedávalo by smysl nastavit vyloučení pro konkrétní prostředek, který již existuje.

1. Select **Select** and then select **Save**.

In this section, you resolved the denied request by creating an exclusion on a single resource group.

## <a name="clean-up-resources"></a>Vyčištění prostředků

If you're done working with resources from this tutorial, use the following steps to delete any of the assignments or definitions created above:

1. Select **Definitions** (or **Assignments** if you're trying to delete an assignment) under **Authoring** in the left side of the Azure Policy page.

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Přiřadili jste zásadu vynucující podmínku u prostředků, které vytvoříte v budoucnu.
> - Vytvořili a přiřadili jste definici iniciativy pro sledování dodržování předpisů u několika prostředků.
> - Vyřešili jste problém s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem.
> - Implementovali jste novou zásadu v rámci celé organizace.

Další informace o strukturách definic zásad najdete v tomto článku:

> [!div class="nextstepaction"]
> [Struktura definic Azure Policy](../concepts/definition-structure.md)