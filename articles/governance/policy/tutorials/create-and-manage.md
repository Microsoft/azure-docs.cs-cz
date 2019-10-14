---
title: Vytváření a správa zásad pro vynucování dodržování předpisů
description: Pomocí služby Azure Policy můžete vynucovat standardy, plnit legislativní požadavky na audit a dodržování předpisů, řídit náklady, udržovat konzistentní zabezpečení a výkon a uplatňovat principy návrhu v rámci celého podniku.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 43d39039d01b22e7d918755e9557d6a1030ef7a8
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302860"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů

Pochopení způsobu, jakým se v Azure vytvářejí a spravují zásady, je důležité pro zajištění souladu s firemními standardy a smlouvami o úrovni služeb. V tomto kurzu se dozvíte, jak pomocí služby Azure Policy provádět některé běžné úlohy související s vytvářením, přiřazováním a správou zásad v rámci celé organizace, jako například:

> [!div class="checklist"]
> - Přiřazení zásady vynucující podmínku u prostředků, které vytvoříte v budoucnu
> - Vytvoření a přiřazení definice iniciativy pro sledování dodržování předpisů u několika prostředků
> - Řešení problému s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem
> - Implementace nové zásady v rámci celé organizace

Pokud chcete přiřadit zásadu pro identifikaci aktuálního stavu dodržování předpisů u vašich existujících prostředků, postup najdete v článcích Rychlý start. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="assign-a-policy"></a>Přiřazení zásady

Prvním krokem při vynucování dodržování předpisů pomocí služby Azure Policy je přiřazení definice zásady. Definice zásady definuje, za jakých podmínek se zásada vynucuje a jaký účinek se má projevit. V tomto příkladu přiřadíte předdefinovanou definici zásady *Vyžadovat SQL Server verze 12.0*, která bude vynucovat podmínku, že všechny databáze SQL Serveru musí být verze 12.0, aby dodržovaly předpisy.

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

   ![Vyhledat zásady ve všech službách](../media/create-and-manage/search-policy.png)

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   ![Vybrat přiřazení na stránce Přehled zásad](../media/create-and-manage/select-assignments.png)

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   ![Přiřazení definice zásady ze stránky přiřazení](../media/create-and-manage/select-assign-policy.png)

1. Na stránce **Přiřadit zásadu** vyberte **Obor** tak, že kliknete na tři tečky a vyberete skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat. Pak v dolní části stránky **Obor** klikněte na **Vybrat**.

   V tomto příkladu se používá předplatné **Contoso**. Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Můžete nastavit filtr pro **Typ** definic zásad na *Předdefinované* a zobrazit všechny definice zásad a přečíst si jejich popisy.

1. Vyberte zásadu **Vyžadovat SQL Server verze 12.0**. Pokud ji nemůžete hned najít, zadejte do vyhledávacího pole text **vyžaduje SQL Server** a potom stiskněte klávesu ENTER nebo klikněte na vyhledávací pole. Jakmile najdete a vyberete definici zásady, v dolní části stránky **Dostupné definice** klikněte na **Vybrat**.

   ![Vyhledání zásady pomocí vyhledávacího filtru](../media/create-and-manage/select-available-definition.png)

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. Pro účely tohoto příkladu ponechte název *Vyžadovat SQL Server verze 12.0*. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.
   **Přiřazeno uživatelem** je automaticky vyplněno na základě toho, kdo je přihlášen. Toto pole je volitelné, takže do něj můžete zadávat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud je přiřazena zásada nebo iniciativa, včetně zásad s [deployIfNotExists](../concepts/effects.md#deployifnotexists) efektem. Vzhledem k tomu, že zásady použité pro tento kurz neexistují, ponechte pole prázdné. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na **Přiřadit**.

## <a name="implement-a-new-custom-policy"></a>Implementace nové vlastní zásady

Teď, když jste přiřadili předdefinovanou definici zásady, můžete se službou Azure Policy provádět další akce. V dalším kroku vytvoříte novou vlastní zásadu, která šetří náklady tím, že se ověří, že virtuální počítače vytvořené ve vašem prostředí nemůžou být v řadě G. Díky tomu se zamítnou všechny žádosti uživatelů ve vaší organizaci o vytvoření virtuálního počítače v řadě G Series.

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   ![Stránka definice v části Authoring Group](../media/create-and-manage/definition-under-authoring.png)

1. V horní části stránky vyberte **+ Definice zásady**. Toto tlačítko se otevře na stránce **definice zásad** .

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

   Vlastnost *Field* v pravidle zásady musí být jedna z následujících hodnot: název, typ, umístění, značky nebo alias. Příkladem aliasu může být `"Microsoft.Compute/VirtualMachines/Size"`.

   Další ukázky zásad Azure najdete v [ukázkách pro Azure Policy](../samples/index.md).

1. Vyberte **Save** (Uložit).

## <a name="create-a-policy-definition-with-rest-api"></a>Vytvoření definice zásady pomocí rozhraní REST API

Můžete vytvořit zásadu s REST API pro Azure Policy definice. Toto rozhraní API umožňuje vytvářet a odstraňovat definice zásad a získávat informace o existujících definicích. Pokud chcete vytvořit definici zásady, použijte následující příklad:

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

Než budete pokračovat s příkladem PowerShellu, ujistěte se, že máte nainstalovanou nejnovější verzi Azure PowerShell AZ Module.

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

Pomocí definice iniciativy můžete seskupit několik definic zásad za účelem dosažení jednoho zastřešujícího cíle. Iniciativa vyhodnocuje prostředky v rámci rozsahu přiřazování dodržování předpisů pro zahrnuté zásady. Další informace o definicích iniciativ najdete v tématu [Přehled služby Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Vytvoření definice iniciativy

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   ![Výběr definice ze stránky definice](../media/create-and-manage/definition-under-authoring.png)

1. V horní části stránky vyberte **+ Definice iniciativy** a otevřete stránku **Definice iniciativy**.

   ![Stránka definice přezkoumání iniciativy](../media/create-and-manage/initiative-definition.png)

1. Pomocí tří teček **Umístění definice** vyberte skupinu pro správu nebo předplatné, kam se definice uloží. Pokud jste na předchozí stránce omezili obor na jednu skupinu pro správu nebo jedno předplatné, **Umístění definice** se vyplní automaticky.

1. Zadejte **Název** a **Popis** iniciativy.

   Tento příklad ověřuje, že prostředky jsou v souladu s definicemi zásad o zabezpečení. Pojmenujte iniciativu **Zajištění zabezpečení** a nastavte popis na: **Tato iniciativa byla vytvořená za účelem zpracování všech definic zásad souvisejících se zabezpečením prostředků**.

1. V části **Kategorie** zvolte některou z existujících možností nebo vytvořte novou kategorii.

1. Projděte seznam **Dostupné definice** (pravá polovina stránky **Definice iniciativy**) a vyberte definice zásad, které chcete přidat do této iniciativy. Pro iniciativu **Zajištění zabezpečení** přidejte následující předdefinované definice zásad kliknutím na **+** vedle informací o definici zásady nebo kliknutím na řádek definice zásady a pak na stránce s podrobnostmi na možnost **+ Přidat**:

   - Vyžadovat SQL Server verze 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Po výběru definice zásady ze seznamu se přidá do části **zásady a parametry**.

   ![Kontrola parametrů definice iniciativy](../media/create-and-manage/initiative-definition-2.png)

1. Pokud definice zásady, která se přidá k iniciativě, má parametry, zobrazí se pod názvem zásady v oblasti **zásady a parametry** . _Hodnotu_ je možné nastavit na možnost Nastavit hodnotu (pevně zakódovaná pro všechna přiřazení této iniciativy) nebo Použít parametr iniciativy (nastaví se při každém přiřazení iniciativy). Je-li vybrána možnost nastavit hodnotu, rozevírací seznam napravo od _hodnot_ umožňuje zadání nebo výběru hodnot (y). Pokud vyberete možnost Použít parametr iniciativy, zobrazí se část **Parametry iniciativy**, kde můžete definovat parametr, který se nastaví během přiřazení iniciativy. Povolené hodnoty pro tento parametr iniciativy můžou dále omezit možnosti nastavení během přiřazení iniciativy.

   ![Změnit parametry definice iniciativy z povolených hodnot](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > U některých parametrů `strongType` není možné automaticky určit seznam hodnot. V těchto případech se napravo od řádku parametru zobrazí tři tečky. Po kliknutí na tyto tři tečky se otevře stránka Obor parametru (&lt;název_parametru&gt;). Na této stránce vyberte předplatné, které chcete použít k zadání možností hodnot. Tento obor parametru se používá pouze během vytváření definice iniciativy a nemá žádný vliv na vyhodnocování zásad ani na obor iniciativy po přiřazení.

1. Klikněte na **Uložit**.

### <a name="assign-an-initiative-definition"></a>Přiřazení definice iniciativy

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

1. Vyhledejte definici iniciativy **Zajištění zabezpečení**, kterou jste vytvořili dříve, a klikněte na ni. V horní části stránky vyberte **Přiřadit** a otevřete stránku **Zajištění zabezpečení: Přiřadit iniciativu**.

   ![Přiřazení definice ze stránky definice iniciativy](../media/create-and-manage/assign-definition.png)

   Můžete také kliknout pravým tlačítkem myši na vybraný řádek nebo kliknout levým tlačítkem na tři tečky na konci řádku kontextové nabídky. Pak vyberte **Přiřadit**.

   ![Alternativní možnosti pro iniciativu](../media/create-and-manage/select-right-click.png)

1. Vyplňte stránku **Zajištění zabezpečení: Přiřadit iniciativu** zadáním následujících ukázkových údajů. Můžete použít vlastní údaje.

   - Obor: Výchozím oborem se stane skupina pro správu nebo předplatné, kam jste iniciativu uložili.
     Obor můžete změnit a přiřadit tak iniciativu k předplatnému nebo ke skupině prostředků v rámci umístění pro uložení.
   - Vyloučení: Můžete nakonfigurovat jakékoli prostředky v rámci oboru, na které se nebude přiřazení iniciativy vztahovat.
   - Název definice a přiřazení iniciativy: Zajištění zabezpečení (předem se vyplní název přiřazované iniciativy).
   - Popis: Toto přiřazení iniciativy je přizpůsobené k vynucování této skupiny definic zásad.
   - Přiřadil: Automaticky se vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadávat vlastní hodnoty.

1. Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud je přiřazena zásada nebo iniciativa, včetně zásad s [deployIfNotExists](../concepts/effects.md#deployifnotexists) efektem. Vzhledem k tomu, že zásady použité pro tento kurz neexistují, ponechte pole prázdné. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. Klikněte na **Přiřadit**.

## <a name="check-initial-compliance"></a>Kontrola počátečního dodržování předpisů

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů**.

1. Vyhledejte bezpečnostní iniciativu **Get** . Je nejspíš pořád ve _stavu dodržování předpisů_ **Nezahájeno**.
   Kliknutím na iniciativu zobrazte úplné podrobnosti o průběhu přiřazení.

   ![Stránka dodržování předpisů iniciativ – hodnocení Nezahájeno](../media/create-and-manage/compliance-status-not-started.png)

1. Po dokončení přiřazení iniciativy se na stránce Dodržování předpisů aktualizuje _Stav dodržování předpisů_ na **Vyhovuje**.

   ![Stránka dodržování předpisů iniciativ – kompatibilní zdroje](../media/create-and-manage/compliance-status-compliant.png)

1. Po kliknutím na jakoukoli zásadu na stránce Dodržování předpisů iniciativy se otevře stránka podrobností o dodržování předpisů pro příslušnou zásadu. Tato stránka obsahuje podrobnosti o dodržování předpisů na úrovni prostředku.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Vyloučení prostředku nedodržujícího předpisy nebo zamítnutého prostředku s využitím vyloučení

Ve výše uvedeném příkladu se po přiřazení definice zásady vyžadující SQL Server verze 12.0 zamítne vytvoření SQL Serveru s jinou verzí než 12.0. V této části se dozvíte, jak vyřešit Zamítnuté žádosti o vytvoření SQL serveru tím, že vytvoříte vyloučení pro jednu skupinu prostředků. Vyloučení zabrání vynucování zásady (nebo iniciativy) pro příslušný prostředek.
V následujícím příkladu je v jedné skupině prostředků povolená jakákoli verze SQL Serveru. Vyloučení se může vztahovat na předplatné nebo skupinu prostředků nebo ho můžete zúžit na jednotlivé prostředky.

Nasazení zabránilo přiřazeným zásadám nebo iniciativám lze zobrazit ve dvou umístěních:

- Ve skupině prostředků, která je cílem nasazení: vyberte **nasazení** v levé části stránky a potom klikněte na **název nasazení** neúspěšného nasazení. U zamítnutého prostředku je uvedený stav _Zakázáno_. Chcete-li určit zásadu nebo iniciativu a přiřazení, které prostředek odepřel, klikněte na tlačítko **neúspěšné. Kliknutím sem zobrazíte podrobnosti – >** na stránce Přehled nasazení. Na pravé straně stránky se otevře okno s informacemi o chybě. V části **Podrobnosti o chybě** jsou identifikátory GUID souvisejících objektů zásad.

  ![Nasazení zamítnuté přiřazením zásady](../media/create-and-manage/rg-deployment-denied.png)

- Na stránce služby Azure Policy. Na levé straně stránky vyberte **Dodržování předpisů** a klikněte na zásadu **Vyžadovat SQL Server verze 12.0**. Na stránce, která se otevře, se zobrazí zvýšení počtu **Zamítnutí**. Na kartě **události** vidíte také, kdo se pokusil nasazení, které zásady zamítlo.

  ![Přehled dodržování předpisů přiřazené zásady](../media/create-and-manage/compliance-overview.png)

V tomto příkladu Trent pekař, One z specialisty na řešení SR. Virtualization společnosti Contoso, jednalo se o požadovanou práci. Musíme udělit Trent výjimku, ale nechceme, aby servery SQL, které nejsou verze 12,0, nemusely být v žádné skupině prostředků. Vytvořili jsme novou skupinu prostředků **SQLServers_Excluded** a teď jí udělíme výjimku pro toto přiřazení zásady.

### <a name="update-assignment-with-exclusion"></a>Aktualizace přiřazení o vyloučení

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Přiřazení**.

1. Projděte všechna přiřazení zásad a otevřete přiřazení *Vyžadovat SQL Server verze 12.0*.

1. Nastavte **Vyloučení** tak, že kliknete na tři tečky a vyberete skupinu prostředků, kterou chcete vyloučit, v tomto příkladu *SQLServers_Excluded*.

   ![Přidat vyloučenou skupinu prostředků do přiřazení zásad](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > V závislosti na zásadě a jejím účinku je možné udělit vyloučení také konkrétním prostředkům v rámci skupiny prostředků v oboru přiřazení. Vzhledem k tomu, že se v tomto kurzu použil účinek **Zamítnutí**, nedávalo by smysl nastavit vyloučení pro konkrétní prostředek, který již existuje.

1. Klikněte na **Vybrat** a potom klikněte na **Uložit**.

V této části jste si vyžádali zamítnutí žádosti vytvořením vyloučení pro jednu skupinu prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste dokončili práci s prostředky z tohoto kurzu, pomocí následujícího postupu odstraňte všechna přiřazení a definice, které jste vytvořili výše:

1. Vyberte **definice** (nebo **přiřazení** , pokud se pokoušíte odstranit přiřazení) v části **vytváření obsahu** v levé části stránky Azure Policy.

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