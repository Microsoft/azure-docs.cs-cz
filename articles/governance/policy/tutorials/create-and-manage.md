---
title: 'Kurz: Vytvoření zásad pro vymáhání dodržování předpisů'
description: V tomto kurzu použijete zásady k vymáhání standardů, řízení nákladů, údržbě zabezpečení a zavedení zásad pro návrh na podnikové požadavky.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: bf3da82abcdcada1fc38df29efc988a1805c3020
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876356"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Kurz: vytvoření a Správa zásad pro vymáhání dodržování předpisů

Pochopení způsobu, jakým se v Azure vytvářejí a spravují zásady, je důležité pro zajištění souladu s firemními standardy a smlouvami o úrovni služeb. V tomto kurzu se dozvíte, jak pomocí služby Azure Policy provádět některé běžné úlohy související s vytvářením, přiřazováním a správou zásad v rámci celé organizace, jako například:

> [!div class="checklist"]
> - Přiřazení zásady vynucující podmínku u prostředků, které vytvoříte v budoucnu
> - Vytvoření a přiřazení definice iniciativy pro sledování dodržování předpisů u několika prostředků
> - Řešení problému s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem
> - Implementace nové zásady v rámci celé organizace

Pokud chcete přiřadit zásadu pro identifikaci aktuálního stavu dodržování předpisů u vašich existujících prostředků, postup najdete v článcích Rychlý start.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="assign-a-policy"></a>Přiřazení zásady

Prvním krokem při vynucování dodržování předpisů pomocí služby Azure Policy je přiřazení definice zásady. Definice zásady definuje, za jakých podmínek se zásada vynucuje a jaký účinek se má projevit. V tomto příkladu přiřaďte definici předdefinované zásady s názvem _Zdědit značku ze skupiny prostředků, pokud chybí_ , aby se přidala Zadaná značka s hodnotou z nadřazené skupiny prostředků do nových nebo aktualizovaných prostředků, ve kterých chybí značka.

1. Přiřaďte zásady tak, že přejdete na Azure Portal. Vyhledejte a vyberte **zásady**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. Na stránce **přiřadit zásady** a na kartě **základy** vyberte **obor** tak, že vyberete tři tečky a vyberete buď skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo seskupení prostředků se toto přiřazení zásady bude vynucovat.
   Pak vyberte **Vybrat** v dolní části stránky **Rozsah** .

   V tomto příkladu se používá předplatné **Contoso** . Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Můžete nastavit filtr pro **Typ** definic zásad na _Předdefinované_ a zobrazit všechny definice zásad a přečíst si jejich popisy.

1. Pokud chybí, vyberte možnost **Zdědit značku ze skupiny prostředků**. Pokud ho nemůžete hned najít, zadejte do vyhledávacího pole **značku** a pak stiskněte klávesu ENTER nebo vyberte mimo vyhledávací pole.
   Po nalezení a výběru definice zásady vyberte **Vybrat** v dolní části stránky **dostupné definice** .

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání":::

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. V tomto příkladu ponechte _zděděnou značku ze skupiny prostředků, pokud chybí_. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.

1. Nechte **vynucení zásad** _Povolit_. Když je toto nastavení _zakázané_, povolí testování výsledku zásady bez aktivace tohoto efektu. Další informace najdete v tématu [režim vynucení](../concepts/assignment-structure.md#enforcement-mode).

1. **Přiřazeno uživatelem** je automaticky vyplněno na základě toho, kdo je přihlášen. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. V horní části průvodce vyberte kartu **parametry** .

1. Jako **název značky**zadejte _prostředí_.

1. V horní části průvodce vyberte kartu **náprava** .

1. Ponechte položku **vytvořit úlohu nápravy** nezaškrtnutou. V tomto poli můžete vytvořit úlohu pro změnu existujících prostředků kromě nových nebo aktualizovaných prostředků. Další informace najdete v tématu o [nápravě prostředků](../how-to/remediate-resources.md).

1. Možnost **vytvořit spravovanou identitu** se automaticky kontroluje, protože tato definice zásady používá efekt [změny](../concepts/effects.md#modify) . **Oprávnění** se automaticky nastaví na _Přispěvatel_ na základě definice zásady. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. V horní části průvodce vyberte kartu **Revize + vytvořit** .

1. Zkontrolujte výběr a potom v dolní části stránky vyberte **vytvořit** .

## <a name="implement-a-new-custom-policy"></a>Implementace nové vlastní zásady

Teď, když jste přiřadili předdefinovanou definici zásady, můžete se službou Azure Policy provádět další akce. V dalším kroku vytvoříte novou vlastní zásadu, která šetří náklady tím, že ověří, že virtuální počítače vytvořené ve vašem prostředí nemůžou být v řadě G. To znamená, že pokaždé, když se uživatel ve vaší organizaci pokusí vytvořit virtuální počítač v řadě G, je žádost zamítnutá.

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. V horní části stránky vyberte **+ Definice zásady**. Toto tlačítko se otevře na stránce **definice zásad** .

1. Zadejte následující informace:

   - Skupina pro správu nebo předplatné, ve kterém je definice zásady uložená. Výběr proveďte pomocí tří teček v části **Umístění definice**.

     > [!NOTE]
     > Pokud se chystáte tuto definici zásady použít pro více předplatných, umístěním musí být skupina pro správu obsahující předplatná, ke kterým zásadu přiřadíte. Totéž platí i pro definici iniciativy.

   - Název definice zásady – _vyžaduje SKU virtuálních počítačů, které nejsou v řadě G_ .
   - Popis toho, co definice zásad má dělat – _Tato definice zásady vynutila, že všechny virtuální počítače vytvořené v tomto oboru mají jiné skladové položky než G series, aby se snížily náklady._
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

   Vlastnost _Field_ v pravidle zásad musí být podporovaná hodnota. V [polích struktury definice zásad](../concepts/definition-structure.md#fields)se našel úplný seznam hodnot. Příkladem aliasu může být `"Microsoft.Compute/VirtualMachines/Size"`.

   Pokud chcete zobrazit další ukázky Azure Policy, přečtěte si téma [Azure Policy Samples](../samples/index.md).

1. Vyberte **Uložit**.

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

Definici zásady můžete vytvořit pomocí rozhraní příkazového řádku Azure pomocí `az policy definition` příkazu. Pokud chcete vytvořit definici zásady s vloženým pravidlem, použijte následující příklad:

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

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání":::

1. V horní části stránky vyberte **+ definice iniciativy** a otevřete průvodce **definice iniciativy** .

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání":::

1. Pro výběr skupiny pro správu nebo předplatného pro uložení definice použijte tři tečky **umístění iniciativy** . Pokud byla předchozí stránka vymezena na jednu skupinu pro správu nebo předplatné, bude automaticky vyplněno **umístění iniciativy** .

1. Zadejte **Název** a **Popis** iniciativy.

   Tento příklad ověřuje, že prostředky jsou v souladu s definicemi zásad o zabezpečení. Pojmenujte iniciativu **Zajištění zabezpečení** a nastavte popis na: **Tato iniciativa byla vytvořená za účelem zpracování všech definic zásad souvisejících se zabezpečením prostředků**.

1. V části **Kategorie** zvolte některou z existujících možností nebo vytvořte novou kategorii.

1. Nastavte **verzi** iniciativy, například _1,0_.

   > [!NOTE]
   > Hodnota verze je výhradně metadata a nepoužívá se pro aktualizace nebo žádné procesy služby Azure Policy.

1. V dolní části stránky nebo na kartě **zásady** v horní části průvodce vyberte **Další** .

1. Vyberte tlačítko **přidat definice zásad** a procházejte seznamem. Vyberte definice zásad, které chcete přidat k této iniciativě. V části **získat zabezpečený** podnět přidejte následující předdefinované definice zásad zaškrtnutím políčka vedle definice zásady:

   - Povolená umístění
   - Monitorovat chybějící Endpoint Protection v Azure Security Center
   - Virtuální počítače, které nejsou přístupné z Internetu, by měly být chráněné pomocí skupin zabezpečení sítě
   - Azure Backup by měla být povolená Virtual Machines
   - Na virtuálních počítačích by se mělo použít šifrování disku
   - Přidejte nebo nahraďte značku v prostředcích (přidejte tuto definici zásady dvakrát).

   Po výběru jednotlivých definic zásad ze seznamu vyberte **Přidat** v dolní části seznamu.
   Vzhledem k tomu, že je přidaný dvakrát, je pro _Přidání nebo nahrazení značky na základě_ definicí prostředků k disdílnému _identifikátoru reference_.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" nebo "použít parametr iniciativy". Je-li vybrána možnost nastavit hodnotu, je související hodnota zadána pod _hodnotou (Values)_. Pokud parametr v definici zásady obsahuje seznam povolených hodnot, pole pro zadání je rozevírací selektor. Pokud je vybrána možnost použít parametr iniciativy, je k dispozici rozevírací seznam s názvy parametrů iniciativy vytvořených na kartě **parametry iniciativy** .

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání":::

   > [!NOTE]
   > U některých parametrů `strongType` není možné automaticky určit seznam hodnot. V těchto případech se napravo od řádku parametru zobrazí tři tečky. Při výběru se otevře stránka obor parametru ( &lt; název parametru &gt; ). Na této stránce vyberte předplatné, které chcete použít k zadání možností hodnot. Tento obor parametru se používá pouze během vytváření definice iniciativy a nemá žádný vliv na vyhodnocování zásad ani na obor iniciativy po přiřazení.

   V rozevíracím seznamu nastavte _hodnotu_ ' Povolit umístění ' na hodnotu ' nastavit hodnotu ' a vyberte ' východní USA 2 '. Pro dvě instance definic zásad pro _Přidání nebo nahrazení značky u prostředků_ nastavte parametry **názvu značky** na ' ENV ' a ' CostCenter ' a parametry **hodnoty značky** na ' test ' a ' Lab ', jak je uvedeno níže. Ostatní ponechte jako výchozí hodnotu. Použití stejné definice dvakrát v iniciativě, ale s různými parametry, tato konfigurace přidá nebo nahradí značku ENV hodnotou test a značkou CostCenter s hodnotou Lab u prostředků v rozsahu přiřazení.

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání":::

1. V dolní části stránky nebo v horní části průvodce vyberte **zkontrolovat + vytvořit** .

1. Zkontrolujte nastavení a vyberte **vytvořit**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Vytvoření definice iniciativy zásad pomocí Azure CLI

Definici iniciativy zásad můžete vytvořit pomocí rozhraní příkazového řádku Azure pomocí `az policy set-definition` příkazu. Pokud chcete vytvořit definici iniciativy zásad s existující definicí zásad, použijte následující příklad:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Vytvoření definice iniciativy zásad pomocí Azure PowerShell

Definici iniciativy zásad můžete vytvořit pomocí Azure PowerShell `New-AzPolicySetDefinition` rutinou. Pokud chcete vytvořit definici iniciativy zásad s existující definicí zásad, použijte následující definiční soubor iniciativy zásad `VMPolicySet.json` :

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Přiřazení definice iniciativy

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

1. Vyhledejte definici iniciativy **Zajištění zabezpečení**, kterou jste vytvořili dříve, a vyberte ji. V horní části stránky vyberte **Přiřadit** a otevřete stránku **Zajištění zabezpečení: Přiřadit iniciativu**.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

   Můžete také kliknout pravým tlačítkem myši na vybraný řádek nebo vybrat tři tečky na konci řádku kontextové nabídky. Pak vyberte **přiřadit**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. Vyplňte stránku **Zajištění zabezpečení: Přiřadit iniciativu** zadáním následujících ukázkových údajů. Můžete použít vlastní údaje.

   - Obor: Výchozím oborem se stane skupina pro správu nebo předplatné, kam jste iniciativu uložili.
     Obor můžete změnit a přiřadit tak iniciativu k předplatnému nebo ke skupině prostředků v rámci umístění pro uložení.
   - Vyloučení: Můžete nakonfigurovat jakékoli prostředky v rámci oboru, na které se nebude přiřazení iniciativy vztahovat.
   - Název definice a přiřazení iniciativy: Zajištění zabezpečení (předem se vyplní název přiřazované iniciativy).
   - Popis: Toto přiřazení iniciativy je přizpůsobené k vynucování této skupiny definic zásad.
   - Vynucení zásad: ponechat jako výchozí _povolenou_.
   - Přiřadil: Automaticky se vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. V horní části průvodce vyberte kartu **parametry** . Pokud jste v předchozích krocích nakonfigurovali parametr iniciativy, nastavte sem hodnotu.

1. V horní části průvodce vyberte kartu **náprava** . Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud je přiřazena zásada nebo podnět, včetně zásad s [deployIfNotExists](../concepts/effects.md#deployifnotexists) nebo [úpravou](../concepts/effects.md#modify) efektů. Vzhledem k tomu, že zásady použité pro tento kurz neexistují, ponechte pole prázdné. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. V horní části průvodce vyberte kartu **Revize + vytvořit** .

1. Zkontrolujte výběr a potom v dolní části stránky vyberte **vytvořit** .

## <a name="check-initial-compliance"></a>Kontrola počátečního dodržování předpisů

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů**.

1. Vyhledejte bezpečnostní iniciativu **Get** . Je nejspíš pořád ve _stavu dodržování předpisů_ **Nezahájeno**.
   Pokud chcete získat úplné podrobnosti o přiřazení, vyberte iniciativu.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. Po dokončení přiřazení iniciativy se na stránce Dodržování předpisů aktualizuje _Stav dodržování předpisů_ na **Vyhovuje**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

1. Výběrem jakékoli zásady na stránce dodržování předpisů v iniciativě se otevře stránka s podrobnostmi o dodržování předpisů pro tyto zásady. Tato stránka obsahuje podrobnosti o dodržování předpisů na úrovni prostředku.

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>Odebrání nekompatibilního nebo odepřeného prostředku z oboru s vyloučením

Po přiřazení iniciativy zásad pro vyžadování konkrétního umístění dojde k odepření veškerého prostředku vytvořeného v jiném umístění. V této části se dozvíte, jak vyřešit zamítnutou žádost o vytvoření prostředku vytvořením vyloučení pro jednu skupinu prostředků. Vyloučení brání vynucení zásady (nebo iniciativy) v této skupině prostředků. V následujícím příkladu je libovolné umístění ve vyloučené skupině prostředků povolené. Vyloučení se může vztahovat na předplatné, skupinu prostředků nebo na jednotlivé prostředky.

> [!NOTE]
> [Výjimku za zásadu](../concepts/exemption-structure.md) lze také použít k přeskočení vyhodnocení prostředku. Další informace najdete v tématu věnovaném [oboru v Azure Policy](../concepts/scope.md).

Nasazení zabraňující přiřazeným zásadám nebo iniciativě můžete zobrazit ve skupině prostředků, která je cílem nasazení: vyberte **nasazení** v levé straně stránky a potom vyberte **název nasazení** neúspěšného nasazení. U zamítnutého prostředku je uvedený stav _Zakázáno_. Chcete-li určit zásadu nebo iniciativu a přiřazení, které prostředek odepřel, vyberte možnost **neúspěšné. Kliknutím sem zobrazíte podrobnosti – >** na stránce Přehled nasazení. Na pravé straně stránky se otevře okno s informacemi o chybě. V části **Podrobnosti o chybě** jsou identifikátory GUID souvisejících objektů zásad.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

Na stránce Azure Policy: na levé straně stránky vyberte **dodržování předpisů** a vyberte iniciativu **získat zabezpečenou** zásadu. Na této stránce se zvyšuje počet **odepření** blokovaných prostředků. Na kartě **události** najdete podrobné informace o tom, kdo se pokusil vytvořit nebo nasadit prostředek, který byl zakázán definicí zásad.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

V tomto příkladu Trent pekař, One z specialisty na řešení SR. Virtualization společnosti Contoso, jednalo se o požadovanou práci. Musíme pro výjimku udělit Trent prostor. Vytvořili jste novou skupinu prostředků, **LocationsExcluded**a další jí přidělíte výjimku tomuto přiřazení zásady.

### <a name="update-assignment-with-exclusion"></a>Aktualizace přiřazení o vyloučení

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Přiřazení**.

1. Procházejte všemi přiřazeními zásad a otevřete přiřazení zásady _získat zabezpečené_ .

1. Nastavte **vyloučení** tak, že vyberete tři tečky a vyberete skupinu prostředků, kterou chcete vyloučit, _LocationsExcluded_ v tomto příkladu. Vyberte **Přidat do vybraného oboru** a pak vyberte **Uložit**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Snímek obrazovky s hledáním zásad na panelu hledání" border="false":::

   > [!NOTE]
   > V závislosti na definici zásad a jejím účinku by bylo možné vyloučení taky udělit konkrétním prostředkům v rámci skupiny prostředků v rozsahu přiřazení. V tomto kurzu byl použit efekt **odepření** , protože by nebylo vhodné nastavit vyloučení u konkrétního prostředku, který již existuje.

1. Vyberte **zkontrolovat + Uložit** a pak vyberte **Uložit**.

V této části jste si vyžádali zamítnutí žádosti vytvořením vyloučení pro jednu skupinu prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste dokončili práci s prostředky z tohoto kurzu, pomocí následujícího postupu odstraňte všechna přiřazení a definice zásad, které jste vytvořili výše:

1. Vyberte **definice** (nebo **přiřazení** , pokud se pokoušíte odstranit přiřazení) v části **vytváření obsahu** v levé části stránky Azure Policy.

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="review"></a>Opakování

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Přiřadili jste zásadu vynucující podmínku u prostředků, které vytvoříte v budoucnu.
> - Vytvořili a přiřadili jste definici iniciativy pro sledování dodržování předpisů u několika prostředků.
> - Vyřešili jste problém s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem.
> - Implementovali jste novou zásadu v rámci celé organizace.

## <a name="next-steps"></a>Další kroky

Další informace o strukturách definic zásad najdete v tomto článku:

> [!div class="nextstepaction"]
> [Struktura definic Azure Policy](../concepts/definition-structure.md)