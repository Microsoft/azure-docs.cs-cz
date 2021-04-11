---
title: 'Kurz: Vytvoření vlastní definice zásady'
description: V tomto kurzu vytvoříte vlastní definici zásad pro Azure Policy, která vynutila vlastní obchodní pravidla pro vaše prostředky Azure.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: c6c4fbe05b6df8a6f8521a0551fe276c893bb6be
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092787"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Kurz: Vytvoření vlastní definice zásady

Vlastní definice zásad umožňuje zákazníkům definovat vlastní pravidla pro používání Azure. Tato pravidla se často vysazují:

- Postupy zabezpečení
- Správa nákladů
- Pravidla specifická pro organizaci (například pojmenování nebo umístění)

Bez ohledu na to, jaký má obchodní ovladač pro vytváření vlastních zásad, jsou tyto kroky stejné jako při definování nových vlastních zásad.

Než vytvoříte vlastní zásadu, podívejte se do [ukázek zásad](../samples/index.md) a podívejte se, jestli už existuje zásada, která vyhovuje vašim potřebám.

Přístup k vytváření vlastních zásad se řídí těmito kroky:

> [!div class="checklist"]
> - Určení podnikových požadavků
> - Namapujte všechny požadavky na vlastnost prostředku Azure.
> - Namapujte vlastnost na alias.
> - Určení, který efekt použít
> - Vytvoření definice zásady

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="identify-requirements"></a>Identifikace požadavků

Před vytvořením definice zásady je důležité pochopit záměr této zásady. V tomto kurzu použijeme běžný požadavek na podnikový zabezpečení jako cíl k ilustraci kroků, které jsou k diskrokování:

- U každého účtu úložiště musí být povolený protokol HTTPS.
- Každý účet úložiště musí být pro protokol HTTP zakázaný.

Vaše požadavky by měly jasně identifikovat jak "tak jak", tak do stavů prostředků "nebude být".

I když jsme definovali očekávaný stav tohoto prostředku, ještě jsme nedefinovali, co chceme udělat s nekompatibilními prostředky. Azure Policy podporuje několik [efektů](../concepts/effects.md). Pro účely tohoto kurzu definujeme obchodní požadavek, který znemožní vytváření prostředků, pokud nevyhovují obchodním pravidlům. Pro splnění tohoto cíle použijeme účinek [zamítnout](../concepts/effects.md#deny) . Chceme také možnost pozastavit zásadu pro konkrétní přiřazení. V takovém případě použijeme efekt [disabled](../concepts/effects.md#disabled) a v definici [zásad se uplatní](../concepts/definition-structure.md#parameters) efekt.

## <a name="determine-resource-properties"></a>Určení vlastností prostředku

Na základě obchodních požadavků je prostředek Azure, který se bude auditovat pomocí Azure Policy účet úložiště. Nevím ale vlastnosti, které se mají použít v definici zásady. Azure Policy vyhodnocuje na základě reprezentace prostředku ve formátu JSON, takže musíme pochopit vlastnosti, které jsou k dispozici na tomto prostředku.

Existuje mnoho způsobů, jak určit vlastnosti prostředku Azure. Podíváme se na každou z těchto kurzů:

- Rozšíření Azure Policy pro VS Code
- Šablony Azure Resource Manager (šablony ARM)
  - Exportovat existující prostředek
  - Prostředí pro vytváření
  - Šablony pro rychlý Start (GitHub)
  - Referenční dokumentace k šablonám
- Průzkumník prostředků Azure

### <a name="view-resources-in-vs-code-extension"></a>Zobrazit prostředky v rozšíření VS Code

[Rozšíření vs Code](../how-to/extension-for-vscode.md#search-for-and-view-resources) lze použít k procházení prostředků ve vašem prostředí a zobrazení vlastností Správce prostředků u každého prostředku.

### <a name="arm-templates"></a>Šablony ARM

Existuje několik způsobů, jak se podívat na [ARM](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) , který obsahuje vlastnost, kterou chcete spravovat.

#### <a name="existing-resource-in-the-portal"></a>Existující prostředek na portálu

Nejjednodušší způsob, jak najít vlastnosti, je podívat se na existující prostředek stejného typu. Prostředky, které jsou už nakonfigurované s nastavením, které chcete vyhodnotit, taky poskytují hodnotu pro porovnání.
Podívejte se na stránku **Exportovat šablonu** (v části **nastavení**) v Azure Portal pro tento konkrétní prostředek.

> [!WARNING]
> Šablona ARM exportovaná pomocí Azure Portal nemůže být přímo připojena do `deployment` vlastnosti pro šablonu ARM v definici zásady [deployIfNotExists](../concepts/effects.md#deployifnotexists) .

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Snímek stránky pro export šablony na existujícím prostředku v Azure Portal." border="false":::

Když to uděláte, účet úložiště odhalí šablonu podobnou tomuto příkladu:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

V části **vlastnosti** je hodnota s názvem **supportsHttpsTrafficOnly** nastavená na **hodnotu false**. Tato vlastnost vypadá jako vlastnost, kterou hledáte. Také **typ** prostředku je **Microsoft. Storage/storageAccounts**. Typ nám umožňuje omezit zásady jenom na prostředky tohoto typu.

#### <a name="create-a-resource-in-the-portal"></a>Vytvoření prostředku na portálu

Dalším způsobem prostřednictvím portálu je prostředí pro vytváření prostředků. Při vytváření účtu úložiště prostřednictvím portálu je u možnosti na kartě **Upřesnit** možnost **migrace zabezpečení vyžadována**. Tato vlastnost má _zakázané_ a _povolené_ možnosti. Ikona informace obsahuje další text, který potvrzuje, že je pravděpodobná vlastnost, kterou chceme. Portál ale na této obrazovce nám neřekne název vlastnosti.

Na kartě **Revize + vytvořit** je odkaz na konci stránky a **stáhne šablonu pro automatizaci**. Když vyberete odkaz, otevře se šablona vytvářející prostředek, který jsme nakonfigurovali. V tomto případě se zobrazí dvě klíčové informace:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Tyto informace nám sdělí typ vlastnosti a také potvrdí, že **supportsHttpsTrafficOnly** je vlastnost, kterou hledáte.

#### <a name="quickstart-templates-on-github"></a>Šablony pro rychlý Start na GitHubu

[Šablony pro rychlý Start Azure](https://github.com/Azure/azure-quickstart-templates) na GitHubu obsahují stovky šablon ARM vytvořených pro různé prostředky. Tyto šablony můžou být skvělým způsobem, jak najít vlastnost prostředku, kterou hledáte. Některé vlastnosti se můžou zdát, co hledáte, ale ovládají něco jiného.

#### <a name="resource-reference-docs"></a>Dokumentace k odkazům na prostředky

Pokud chcete ověřit vlastnost **supportsHttpsTrafficOnly** je správná, zkontrolujte odkaz na šablonu ARM pro [prostředek účtu úložiště](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) ve zprostředkovateli úložiště. Objekt Properties obsahuje seznam platných parametrů. Výběr odkazu [StorageAccountPropertiesCreateParameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) zobrazí tabulku přijatelných vlastností. k dispozici je **supportsHttpsTrafficOnly** a popis odpovídá tomu, co hledáte pro splnění obchodních požadavků.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure

Dalším způsobem, jak prozkoumat prostředky Azure, je prostřednictvím [Azure Resource Explorer](https://resources.azure.com) (Preview). Tento nástroj používá kontext předplatného, takže se na webu musíte ověřit pomocí vašich přihlašovacích údajů Azure. Po ověření můžete procházet podle zprostředkovatelů, předplatných, skupin prostředků a prostředků.

Vyhledejte prostředek účtu úložiště a podívejte se na vlastnosti. Tady se zobrazuje i vlastnost **supportsHttpsTrafficOnly** . Když vyberete kartu **dokumentace** , uvidíme, že popis vlastnosti se shoduje s tím, co jsme našli v referenční dokumentaci dříve.

## <a name="find-the-property-alias"></a>Najít alias vlastnosti

Identifikovali jsme vlastnost prostředku, ale musíme tuto vlastnost namapovat na [alias](../concepts/definition-structure.md#aliases).

Existuje několik způsobů, jak určit aliasy pro prostředek Azure. Podíváme se na každou z těchto kurzů:

- Rozšíření Azure Policy pro VS Code
- Azure CLI
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>Získat aliasy v rozšíření VS Code

Rozšíření Azure Policy pro rozšíření VS Code usnadňuje procházení prostředků a [zjišťování aliasů](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

> [!NOTE]
> Rozšíření VS Code zpřístupňuje pouze vlastnosti režimu Správce prostředků a nezobrazuje žádné vlastnosti [režimu poskytovatele prostředků](../concepts/definition-structure.md#mode) .

### <a name="azure-cli"></a>Azure CLI

V Azure CLI se `az provider` skupina příkazů používá k hledání aliasů prostředků. Vyfiltrujeme obor názvů **Microsoft. Storage** na základě detailů, které jsme o prostředku Azure dostali dřív.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Ve výsledcích se zobrazí alias podporovaný účty úložiště s názvem **supportsHttpsTrafficOnly**. Tento alias znamená, že můžeme napsat zásadu, abychom vynutili naše obchodní požadavky.

### <a name="azure-powershell"></a>Azure PowerShell

V Azure PowerShell `Get-AzPolicyAlias` rutina slouží k hledání aliasů prostředků. Vyfiltrujeme obor názvů **Microsoft. Storage** na základě detailů, které jsme o prostředku Azure dostali dřív.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Podobně jako Azure CLI zobrazuje výsledky aliasy podporované účty úložiště s názvem **supportsHttpsTrafficOnly**.

## <a name="determine-the-effect-to-use"></a>Určení efektu, který se má použít

Rozhodnutí o tom, co dělat s prostředky, které nedodržují předpisy, je skoro stejně důležité jako rozhodování, co vyhodnotit na prvním místě. Každá možná odpověď na prostředek, který nedodržuje předpisy, se nazývá [efekt](../concepts/effects.md). Tento efekt řídí, jestli je prostředek, který nedodržuje předpisy, přihlášen, je blokovaný, má připojená data, nebo má přidružené nasazení pro uvedení prostředku zpátky do stavu kompatibility.

V našem příkladu je odepřený efekt, protože nepotřebujeme, aby byly prostředky, které nedodržují předpisy, vytvořené v prostředí Azure. Audit je vhodný první volbou pro účinek zásad, která určuje, jaký dopad zásad je před tím, než je nastavování zamítnuto. Jedním ze způsobů, jak změnit účinek na přiřazení, je parametrizovat efekt. Podrobnosti o tom, jak najdete v níže uvedených [parametrech](#parameters) .

## <a name="compose-the-definition"></a>Vytvoření definice

Teď máme podrobnosti o vlastnosti a alias pro to, co plánujeme spravovat. V dalším kroku vytvoříte samotné pravidlo zásad. Pokud ještě neznáte jazyk zásad, [strukturu definice](../concepts/definition-structure.md) referenčních zásad pro strukturu definice zásad. Tady je prázdná šablona toho, co definice zásad vypadá takto:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadata

První tři komponenty jsou metadata zásad. Tyto komponenty se dají snadno zadat, protože víme, pro které pravidlo vytváříme. [Režim](../concepts/definition-structure.md#mode) je primárně o značkách a umístění prostředků. Vzhledem k tomu, že nepotřebujeme omezit vyhodnocení na prostředky, které podporují značky, použijeme pro **režim** hodnotu _All_ .

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametry

Přestože jsme nepoužili parametr pro změnu hodnocení, chceme použít parametr a povolit změnu **efektu** pro řešení potíží. Definujeme parametr **effectType** a omezíme ho jenom na **Deny** a **disabled**. Tyto dvě možnosti odpovídají našim obchodním požadavkům. Blok dokončených parametrů vypadá jako v tomto příkladu:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Pravidlo zásad

Sestavování [pravidla zásad](../concepts/definition-structure.md#policy-rule) je posledním krokem při sestavování vlastní definice zásad. Identifikovali jsme dva příkazy, které se mají testovat:

- Tento **typ** účtu úložiště je **Microsoft. Storage/storageAccounts**
- To, že účet úložiště **supportsHttpsTrafficOnly** nemá **hodnotu true**

Protože potřebujeme, aby oba tyto příkazy byly pravdivé, použijeme [logický operátor](../concepts/definition-structure.md#logical-operators) **allOf** . Parametr **effectType** předáte k tomuto efektu namísto provedení statické deklarace. Naše dokončené pravidlo vypadá jako v tomto příkladu:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Definice dokončena

Tady je naše dokončená definice se všemi třemi částmi definovaných zásad:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

K vytvoření nové zásady se dá použít dokončená definice. Portál a každá sada SDK (Azure CLI, Azure PowerShell a REST API) přijímají definici různými způsoby, proto si Projděte příkazy pro každý, abyste ověřili správné využití. Pak ji přiřaďte pomocí parametrizovaného efektu k odpovídajícím prostředkům pro správu zabezpečení účtů úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste dokončili práci s prostředky z tohoto kurzu, pomocí následujícího postupu odstraňte všechna přiřazení a definice, které jste vytvořili výše:

1. Vyberte **definice** (nebo **přiřazení** , pokud se pokoušíte odstranit přiřazení) v části **vytváření obsahu** v levé části stránky Azure Policy.

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="review"></a>Opakování

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Identifikujte vaše podnikové požadavky.
> - Namapovaný každý požadavek na vlastnost prostředku Azure
> - Namapována vlastnost na alias.
> - Zjistili efekt, který se má použít
> - Složená definice zásad

## <a name="next-steps"></a>Další kroky

V dalším kroku vytvořte a přiřaďte zásadu pomocí vlastní definice zásad:

> [!div class="nextstepaction"]
> [Vytvoření a přiřazení definice zásady](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
