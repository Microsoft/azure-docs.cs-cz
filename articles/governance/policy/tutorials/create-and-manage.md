---
title: 'Kurz: Vytváření zásad pro vynucení dodržování předpisů'
description: V tomto kurzu pomocí zásad vynucujete standardy, řídíte náklady, udržujete zabezpečení a ukládáte principy návrhu v rámci celého podniku.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: 15a6c3df26938332d42ecbcfff43f958577062c4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239965"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Kurz: Vytváření a správa zásad pro vynucení dodržování předpisů

Pochopení způsobu, jakým se v Azure vytvářejí a spravují zásady, je důležité pro zajištění souladu s firemními standardy a smlouvami o úrovni služeb. V tomto kurzu se dozvíte, jak pomocí služby Azure Policy provádět některé běžné úlohy související s vytvářením, přiřazováním a správou zásad v rámci celé organizace, jako například:

> [!div class="checklist"]
> - Přiřazení zásady vynucující podmínku u prostředků, které vytvoříte v budoucnu
> - Vytvoření a přiřazení definice iniciativy pro sledování dodržování předpisů u několika prostředků
> - Řešení problému s prostředkem nedodržujícím předpisy nebo zamítnutým prostředkem
> - Implementace nové zásady v rámci celé organizace

Pokud chcete přiřadit zásadu pro identifikaci aktuálního stavu dodržování předpisů u vašich existujících prostředků, postup najdete v článcích Rychlý start.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="assign-a-policy"></a>Přiřazení zásady

Prvním krokem při vynucování dodržování předpisů pomocí služby Azure Policy je přiřazení definice zásady. Definice zásady definuje, za jakých podmínek se zásada vynucuje a jaký účinek se má projevit. V tomto příkladu přiřaďte předdefinovanou definici zásadu _nazvanou Zdědit značku ze skupiny prostředků, pokud chybí, chcete-li_ přidat zadanou značku s její hodnotou z nadřazené skupiny prostředků do nových nebo aktualizovaných prostředků, které značku postrádají.

1. Přejděte na portál Azure a přiřaďte zásady. Vyhledejte a vyberte **zásady**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Hledání zásad na panelu hledání" border="false":::

1. Na levé straně stránky služby Azure Policy vyberte **Přiřazení**. Přiřazení je zásada, která byla přiřazena, aby proběhla v rámci zadaného oboru.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Vybrat přiřazení na stránce Přehled zásad" border="false":::

1. V horní části stránky **Zásady – Přiřazení** vyberte **Přiřadit zásadu**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Přiřazení definice zásady ze stránky Přiřazení" border="false":::

1. Na kartě **Přiřadit zásady** a **základy** vyberte **obor** tak, že vyberete tři tečky a vyberete skupinu pro správu nebo předplatné. Volitelně můžete vybrat skupinu prostředků. Obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude vynucovat.
   Pak v dolní části stránky **Obor** vyberte **Vybrat.**

   Tento příklad používá předplatné **Contoso.** Vaše předplatné se bude lišit.

1. Prostředky je možné vyloučit na základě **oboru**. **Vyloučení** začínají na úrovni o jednu nižší, než je úroveň **oboru**. **Vyloučení** jsou volitelná, takže toto pole prozatím ponechte prázdné.

1. Výběrem tří teček **Definice zásady** otevřete seznam dostupných definic. Můžete nastavit filtr pro **Typ** definic zásad na _Předdefinované_ a zobrazit všechny definice zásad a přečíst si jejich popisy.

1. Vyberte **Zdědit značku ze skupiny prostředků, pokud chybí**. Pokud ji nemůžete najít hned, zadejte do vyhledávacího pole **značku a** stiskněte klávesu ENTER nebo vyberte mimo vyhledávací pole.
   Jakmile **najdete** a vyberete definici zásad, vyberte v dolní části stránky **Dostupné definice** možnost Vybrat.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Použití vyhledávacího filtru k vyhledání zásady":::

1. Do pole **Název přiřazení** se automaticky vyplní název vybrané zásady, který však můžete změnit. V tomto příkladu ponechte _ponechat dědit značku ze skupiny prostředků, pokud chybí_. Volitelně můžete přidat také **Popis**. Popis obsahuje podrobnosti o tomto přiřazení zásady.

1. Ponechat **vynucení zásad** jako _povoleno_. Pokud _je zakázáno_, toto nastavení umožňuje testování výsledku zásady bez spuštění efektu. Další informace naleznete v [režimu vynucení](../concepts/assignment-structure.md#enforcement-mode).

1. **Přiřazeno podle** je automaticky vyplněno podle toho, kdo je přihlášen. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. V horní části průvodce vyberte kartu **Parametry.**

1. Do **pole Název značky**zadejte _prostředí_.

1. V horní části průvodce vyberte kartu **Náprava.**

1. Ponechat **Ponechat Nezaškrtnuté políčko Vytvořit nápravný úkol.** Toto pole umožňuje vytvořit úkol pro změnu stávajících zdrojů kromě nových nebo aktualizovaných zdrojů. Další informace naleznete v [tématu Náprava prostředků](../how-to/remediate-resources.md).

1. **Vytvoření spravované identity** se automaticky kontroluje, protože tato definice zásad používá [efekt úpravy.](../concepts/effects.md#modify) **Oprávnění** je _nastavena_ na přispěvatele automaticky na základě definice zásady. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. V horní části průvodce vyberte kartu **Revize + vytvoření.**

1. Zkontrolujte výběry a v dolní části stránky vyberte **Vytvořit.**

## <a name="implement-a-new-custom-policy"></a>Implementace nové vlastní zásady

Teď, když jste přiřadili předdefinovanou definici zásady, můžete se službou Azure Policy provádět další akce. Dále vytvořte novou vlastní zásadu, která ušetří náklady ověřením, že virtuální počítače vytvořené ve vašem prostředí nemohou být v řadě G. Díky tomu se zamítnou všechny žádosti uživatelů ve vaší organizaci o vytvoření virtuálního počítače v řadě G Series.

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Stránka Definice v části Skupina pro vytváření" border="false":::

1. V horní části stránky vyberte **+ Definice zásady**. Toto tlačítko se otevře na stránce **Definice zásad.**

1. Zadejte následující informace:

   - Skupina pro správu nebo předplatné, ve kterém je definice zásady uložená. Výběr proveďte pomocí tří teček v části **Umístění definice**.

     > [!NOTE]
     > Pokud se chystáte tuto definici zásady použít pro více předplatných, umístěním musí být skupina pro správu obsahující předplatná, ke kterým zásadu přiřadíte. Totéž platí i pro definici iniciativy.

   - Název definice zásady _ _*_Vyžadovat skuténky virtuálních zařízení menší než řada G_
   - Popis účelu definice zásady – _Tato definice zásady za účelem snížení nákladů vynucuje, aby všechny virtuální počítače vytvořené v tomto oboru měly skladové položky nižší než G Series._
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

   Vlastnost _pole_ v pravidle zásadmusí být podporovaná hodnota. Úplný seznam hodnot naleznete v [polích struktury definice zásad](../concepts/definition-structure.md#fields). Příkladem aliasu může být `"Microsoft.Compute/VirtualMachines/Size"`.

   Další ukázky zásad Azure najdete v [ukázkách pro Azure Policy](../samples/index.md).

1. Vyberte **Uložit**.

## <a name="create-a-policy-definition-with-rest-api"></a>Vytvoření definice zásady pomocí rozhraní REST API

Můžete vytvořit zásady s rozhraním REST API pro definice zásad Azure. Toto rozhraní API umožňuje vytvářet a odstraňovat definice zásad a získávat informace o existujících definicích. Pokud chcete vytvořit definici zásady, použijte následující příklad:

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

Než budete pokračovat v příkladu PowerShellu, ujistěte se, že jste nainstalovali nejnovější verzi modulu Azure PowerShell Az.

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

Definici zásad můžete vytvořit pomocí příkazu Azure CLI s příkazem. `az policy definition` Pokud chcete vytvořit definici zásady s vloženým pravidlem, použijte následující příklad:

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

Pomocí definice iniciativy můžete seskupit několik definic zásad za účelem dosažení jednoho zastřešujícího cíle. Iniciativa vyhodnotí prostředky v rozsahu přiřazení pro dodržování zahrnuté zásady. Další informace o definicích iniciativ najdete v tématu [Přehled služby Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Vytvoření definice iniciativy

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Definice**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Výběr definice ze stránky Definice" border="false":::

1. V horní části stránky vyberte **+ Definice iniciativy** a otevřete stránku **Definice iniciativy**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Zkontrolovat stránku definice iniciativy" border="false":::

1. Pomocí tří teček **Umístění definice** vyberte skupinu pro správu nebo předplatné, kam se definice uloží. Pokud jste na předchozí stránce omezili obor na jednu skupinu pro správu nebo jedno předplatné, **Umístění definice** se vyplní automaticky. Po výběru je **vyplněno dostupné definice.**

1. Zadejte **Název** a **Popis** iniciativy.

   Tento příklad ověřuje, že prostředky jsou v souladu s definicemi zásad o získání zabezpečení. Pojmenujte iniciativu **Zajištění zabezpečení** a nastavte popis na: **Tato iniciativa byla vytvořená za účelem zpracování všech definic zásad souvisejících se zabezpečením prostředků**.

1. V části **Kategorie** zvolte některou z existujících možností nebo vytvořte novou kategorii.

1. Projděte seznam **Dostupné definice** (pravá polovina stránky **Definice iniciativy**) a vyberte definice zásad, které chcete přidat do této iniciativy. Pro iniciativu **Získat zabezpečení** přidejte následující předdefinované definice **+** zásad tak, že vyberete další položku vedle informací o definici zásad nebo vyberete řádek definice zásad a potom na stránce podrobností **možnost + Přidat:**

   - Povolená umístění
   - Sledování chybějící ochrany koncového bodu v Azure Security Center
   - Pravidla skupiny zabezpečení sítě pro virtuální počítače orientované na Internet by měla být posílena
   - Azure Backup by měla být povolená pro virtuální počítače
   - Šifrování disku by mělo být použito na virtuálních počítačích.

   Po výběru definice zásadze ze seznamu je každá z nich **přidána**pod kategorii .

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Zkontrolovat parametry definice iniciativy" border="false":::

1. Pokud definice zásady přidávaná do iniciativy obsahuje parametry, zobrazí se pod názvem zásad v oblasti v oblasti **Kategorie.** _Hodnotu_ je možné nastavit na možnost Nastavit hodnotu (pevně zakódovaná pro všechna přiřazení této iniciativy) nebo Použít parametr iniciativy (nastaví se při každém přiřazení iniciativy). Pokud je vybrána možnost Nastavit hodnotu, rozevírací rozevírací vpravo od _hodnot_ umožňuje zadat nebo vybrat hodnotu(y). Pokud vyberete možnost Použít parametr iniciativy, zobrazí se část **Parametry iniciativy**, kde můžete definovat parametr, který se nastaví během přiřazení iniciativy. Povolené hodnoty pro tento parametr iniciativy můžou dále omezit možnosti nastavení během přiřazení iniciativy.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Změnit parametry definice iniciativy z povolených hodnot" border="false":::

   > [!NOTE]
   > U některých parametrů `strongType` není možné automaticky určit seznam hodnot. V těchto případech se napravo od řádku parametru zobrazí tři tečky. Výběrem otevřete stránku&lt;"Obor&gt;parametru ( název parametru ). Na této stránce vyberte předplatné, které chcete použít k zadání možností hodnot. Tento obor parametru se používá pouze během vytváření definice iniciativy a nemá žádný vliv na vyhodnocování zásad ani na obor iniciativy po přiřazení.

   Nastavte parametr Povolená umístění na hodnotu Východní US 2 a ostatní ponechte jako výchozí hodnotu AuditifNotExists.

1. Vyberte **Uložit**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Vytvoření definice iniciativy zásad pomocí azure cli

Definici iniciativy zásad můžete vytvořit pomocí `az policy set-definition` příkazu Azure CLI s příkazem. Chcete-li vytvořit definici iniciativy zásad s existující definicí zásad, použijte následující příklad:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Vytvoření definice iniciativy zásad pomocí Azure PowerShellu

Definici iniciativy zásad můžete vytvořit pomocí `New-AzPolicySetDefinition` Azure PowerShellu s rutinou. Chcete-li vytvořit definici iniciativy zásad s existující definicí `VMPolicySet.json`zásad, použijte následující soubor definice iniciativy zásad jako:

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

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Přiřazení definice ze stránky definice iniciativy" border="false":::

   Můžete také kliknout pravým tlačítkem myši na vybraný řádek nebo vybrat tři tečky na konci řádku pro kontextovou nabídku. Pak vyberte **Přiřadit**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Alternativní možnosti iniciativy" border="false":::

1. Vyplňte stránku **Zajištění zabezpečení: Přiřadit iniciativu** zadáním následujících ukázkových údajů. Můžete použít vlastní údaje.

   - Obor: Výchozím oborem se stane skupina pro správu nebo předplatné, kam jste iniciativu uložili.
     Obor můžete změnit a přiřadit tak iniciativu k předplatnému nebo ke skupině prostředků v rámci umístění pro uložení.
   - Vyloučení: Můžete nakonfigurovat jakékoli prostředky v rámci oboru, na které se nebude přiřazení iniciativy vztahovat.
   - Název definice a přiřazení iniciativy: Zajištění zabezpečení (předem se vyplní název přiřazované iniciativy).
   - Popis: Toto přiřazení iniciativy je přizpůsobené k vynucování této skupiny definic zásad.
   - Vynucení zásad: Ponechte jako výchozí _povoleno_.
   - Přiřadil: Automaticky se vyplní podle toho, kdo je přihlášený. Toto pole je volitelné, takže do něj můžete zadat vlastní hodnoty.

1. V horní části průvodce vyberte kartu **Parametry.** Pokud jste v předchozích krocích nakonfigurovali parametr iniciativy, nastavte zde hodnotu.

1. V horní části průvodce vyberte kartu **Náprava.** Políčko **Vytvořit spravovanou identitu** ponechte nezaškrtnuté. Toto políčko _musí_ být zaškrtnuto, pokud přiřazená zásada nebo iniciativa obsahuje zásadu s [deployIfNotExists](../concepts/effects.md#deployifnotexists) nebo [upravit](../concepts/effects.md#modify) účinky. Vzhledem k tomu, že zásady použité pro tento kurz nejsou, ponechte ji prázdnou. Další informace najdete v tématech věnovaných [spravovaným identitám](../../../active-directory/managed-identities-azure-resources/overview.md) a [principu fungování zabezpečení náprav](../how-to/remediate-resources.md#how-remediation-security-works).

1. V horní části průvodce vyberte kartu **Revize + vytvoření.**

1. Zkontrolujte výběry a v dolní části stránky vyberte **Vytvořit.**

## <a name="check-initial-compliance"></a>Kontrola počátečního dodržování předpisů

1. Na levé straně stránky služby Azure Policy vyberte **Dodržování předpisů**.

1. Vyhledejte iniciativu **Získat zabezpečení.** Je pravděpodobné, že je stále ve _stavu dodržování_ **není spuštěn**.
   Vyberte iniciativu, chcete-li získat podrobné informace o průběhu přiřazení.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Stránka dodržování předpisů iniciativy – hodnocení nebyla zahájena" border="false":::

1. Po dokončení přiřazení iniciativy se na stránce Dodržování předpisů aktualizuje _Stav dodržování předpisů_ na **Vyhovuje**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Stránka dodržování předpisů iniciativy – kompatibilní s prostředky" border="false":::

1. Výběrem všech zásad na stránce dodržování předpisů iniciativy se otevře stránka podrobností o dodržování předpisů pro tuto zásadu. Tato stránka obsahuje podrobnosti o dodržování předpisů na úrovni prostředku.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Vyloučení prostředku nedodržujícího předpisy nebo zamítnutého prostředku s využitím vyloučení

Po přiřazení iniciativy zásad k vyžadování určitého umístění je odepřen jakýkoli prostředek vytvořený v jiném umístění. V této části můžete projít řešení zamítnutý požadavek na vytvoření prostředku vytvořením vyloučení na jednu skupinu prostředků. Vyloučení brání vynucení zásady (nebo iniciativy) v této skupině prostředků. V následujícím příkladu je povoleno libovolné umístění ve skupině vyloučených prostředků. Vyloučení se může vztahovat na předplatné, skupinu prostředků nebo jednotlivé prostředky.

Nasazení, kterým přiřazená zásada nebo iniciativa brání, lze zobrazit ve skupině prostředků, na kterou je nasazení zaměřeno: Vyberte **nasazení** na levé straně stránky a pak vyberte **název nasazení** neúspěšného nasazení. U zamítnutého prostředku je uvedený stav _Zakázáno_. Chcete-li určit zásadu nebo iniciativu a přiřazení, které byl zdroj odepřen, vyberte **možnost Nezdařilo se. Kliknutím sem zobrazíte podrobnosti ->** na stránce Přehled nasazení. Na pravé straně stránky se otevře okno s informacemi o chybě. V části **Podrobnosti o chybě** jsou identifikátory GUID souvisejících objektů zásad.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Nasazení zamítnuté přiřazením zásady" border="false":::

Na stránce Zásady Azure: Vyberte **dodržování předpisů** na levé straně stránky a vyberte iniciativu **Zásady získat zabezpečení.** Na této stránce je zvýšení **odepřít** počet blokovaných prostředků. Na kartě **Události** jsou podrobnosti o tom, kdo se pokusil vytvořit nebo nasadit prostředek, který byl odmítnut definicí zásad.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Přehled dodržování předpisů přiřazené zásady" border="false":::

V tomto příkladu, Trent Baker, jeden z contoso sr virtualizace specialistů, dělal požadovanou práci. Musíme Trentovi poskytnout prostor pro výjimku. Vytvořil a novou skupinu prostředků **LocationsExcluded**a dále udělit výjimku pro toto přiřazení zásad.

### <a name="update-assignment-with-exclusion"></a>Aktualizace přiřazení o vyloučení

1. Na levé straně stránky služby Azure Policy v části **Vytváření obsahu** vyberte **Přiřazení**.

1. Projděte si všechna přiřazení zásad a otevřete přiřazení zásad _Získat zabezpečení._

1. Nastavte **vyloučení** výběrem tři tečky a výběrem skupiny prostředků vyloučit _LocationsExcluded_ v tomto příkladu. Vyberte **Přidat do vybraného oboru** a pak vyberte **Uložit**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Přidání vyloučené skupiny zdrojů do přiřazení zásad" border="false":::

   > [!NOTE]
   > V závislosti na definici zásady a jejím účinku může být vyloučení uděleno také určitým zdrojům v rámci skupiny prostředků v rámci přiřazení. Jako **Deny** efekt byl použit v tomto kurzu, by nemělo smysl nastavit vyloučení na konkrétní prostředek, který již existuje.

1. Vyberte **Zkontrolovat + uložit** a pak vyberte **Uložit**.

V této části jste vyřešili zamítnutý požadavek vytvořením vyloučení pro jednu skupinu prostředků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s prostředky z tohoto kurzu skončili, odstraňte všechna přiřazení nebo definice zásad vytvořené výše pomocí následujících kroků:

1. V části **Vytváření** na levé straně stránky Zásad Azure vyberte **Definice** (nebo **přiřazení,** pokud se pokoušíte odstranit přiřazení).

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="review"></a>Revize

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