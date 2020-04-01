---
title: 'Kurz: Vytvoření vlastní definice zásad'
description: V tomto kurzu vytváříte vlastní definici zásad pro zásady Azure k vynucení vlastních obchodních pravidel ve vašich prostředcích Azure.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: f7c303956b209b88ce3c697b5b66243e37071c83
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238941"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Kurz: Vytvoření vlastní definice zásad

Vlastní definice zásad umožňuje zákazníkům definovat vlastní pravidla pro používání Azure. Tato pravidla často vynucují:

- Postupy zabezpečení
- Správa nákladů
- Pravidla specifická pro organizaci (například pojmenování nebo umístění)

Bez ohledu na obchodní ovladač pro vytvoření vlastní zásady, kroky jsou stejné pro definování nové vlastní zásady.

Před vytvořením vlastní zásady zkontrolujte [ukázky zásad](../samples/index.md) a zjistěte, zda zásada, která odpovídá vašim potřebám, již existuje.

Přístup k vytvoření vlastní zásady se řídí následujícími kroky:

> [!div class="checklist"]
> - Identifikujte své obchodní požadavky
> - Mapování každého požadavku na vlastnost prostředku Azure
> - Mapování vlastnosti na alias
> - Určení efektu, který se má použít
> - Sestavit definici zásad

## <a name="prerequisites"></a>Požadavky

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="identify-requirements"></a>Identifikovat požadavky

Před vytvořením definice zásady je důležité pochopit záměr zásady. V tomto kurzu použijeme jako cíl společný požadavek na zabezpečení podniku, který ilustruje související kroky:

- Každý účet úložiště musí být povolen pro protokol HTTPS.
- Každý účet úložiště musí být pro protokol HTTP zakázán.

Vaše požadavky by měly jasně identifikovat jak "být" a "nesmí být" zdroje států.

I když jsme definovali očekávaný stav prostředku, ještě jsme nedefinovali, co chceme udělat s nekompatibilními prostředky. Azure Policy podporuje řadu [efektů](../concepts/effects.md). V tomto kurzu definujeme obchodní požadavek jako zabránění vytváření prostředků, pokud nejsou v souladu s obchodními pravidly. K splnění tohoto cíle použijeme efekt [Odepřít.](../concepts/effects.md#deny) Chceme také možnost pozastavit zásady pro konkrétní přiřazení. Jako takový použijeme efekt [Zakázáno](../concepts/effects.md#disabled) a efekt [parametr](../concepts/definition-structure.md#parameters) v definici zásady.

## <a name="determine-resource-properties"></a>Určení vlastností prostředků

Na základě obchodních požadavků je prostředek Azure pro auditování pomocí zásad Azure účet úložiště. Neznáme však vlastnosti, které chcete použít v definici zásad. Azure Policy vyhodnocuje podle json reprezentace prostředku, takže budeme muset pochopit vlastnosti, které jsou k dispozici na tomto prostředku.

Existuje mnoho způsobů, jak určit vlastnosti pro prostředek Azure. Podíváme se na každý pro tento kurz:

- Rozšíření Azure Policy pro VS Code
- Šablony Resource Manageru
  - Export existujícího prostředku
  - Prostředí vytváření
  - Šablony rychlých startů (GitHub)
  - Dokumenty odkazů na šablonu
- Průzkumník prostředků Azure

### <a name="view-resources-in-vs-code-extension"></a>Zobrazit prostředky v rozšíření VS Code

[Rozšíření VS Code](../how-to/extension-for-vscode.md#search-for-and-view-resources) lze použít k procházení prostředků ve vašem prostředí a zobrazení vlastností Správce prostředků na každém prostředku.

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Existuje několik způsobů, jak se podívat na [šablonu Správce prostředků,](../../../azure-resource-manager/templates/template-tutorial-create-encrypted-storage-accounts.md) která obsahuje vlastnost, kterou chcete spravovat.

#### <a name="existing-resource-in-the-portal"></a>Existující zdroj na portálu

Nejjednodušší způsob, jak najít vlastnosti je podívat se na existující prostředek stejného typu. Prostředky již nakonfigurované s nastavením, které chcete vynutit, také poskytují hodnotu, proti které chcete porovnat.
Podívejte se na stránku **šablony exportu** (v části **Nastavení)** na portálu Azure pro daný prostředek.

![Exportovat stránku šablony na existující prostředek](../media/create-custom-policy-definition/export-template.png)

Pokud tak učiníte pro účet úložiště, zobrazí se šablona podobná tomuto příkladu:

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

Pod **vlastnosti** je hodnota s názvem **supportsHttpsTrafficOnly** nastavena na **false**. Tato nemovitost vypadá, že to může být nemovitost, kterou hledáme. **Typ** prostředku je také **Microsoft.Storage/storageAccounts**. Typ nám umožňuje omezit zásady pouze prostředky tohoto typu.

#### <a name="create-a-resource-in-the-portal"></a>Vytvoření prostředku na portálu

Dalším způsobem prostřednictvím portálu je prostředí vytváření prostředků. Při vytváření účtu úložiště prostřednictvím portálu je vyžadován možnost na kartě **Upřesnit** **.** Tato vlastnost má _možnosti Zakázáno_ _a Povoleno._ Ikona informace má další text, který potvrzuje, že tato možnost je pravděpodobně vlastnost, kterou chceme. Portál nám však neříká název vlastnosti na této obrazovce.

Na kartě **Revize + vytvořit** je v dolní části stránky odkaz na stažení šablony pro **automatizaci**. Výběrem odkazu se otevře šablona, která vytvoří prostředek, který jsme nakonfigurovali. V tomto případě vidíme dvě klíčové informace:

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

Tyto informace nám říká typ vlastnosti a také potvrzuje **supportsHttpsTrafficOnly** je vlastnost, kterou hledáme.

#### <a name="quickstart-templates-on-github"></a>Šablony rychlých startů na GitHubu

[Šablony rychlého startu Azure](https://github.com/Azure/azure-quickstart-templates) na GitHubu mají stovky šablon Správce prostředků vytvořených pro různé prostředky. Tyto šablony mohou být skvělým způsobem, jak najít vlastnost prostředku, kterou hledáte. Některé vlastnosti se mohou jevit jako to, co hledáte, ale ovládat něco jiného.

#### <a name="resource-reference-docs"></a>Dokumenty pro odkazy na zdroje

Chcete-li ověřit **podporujeHttpsTrafficOnly** je správná vlastnost, zkontrolujte odkaz na šablonu Správce prostředků pro [prostředek účtu úložiště](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) na zprostředkovatele úložiště.
Objekt vlastností má seznam platných parametrů. Výběrem odkazu [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) se zobrazí tabulka přijatelných vlastností. **supportsHttpsTrafficOnly** je k dispozici a popis odpovídá tomu, co hledáme ke splnění obchodních požadavků.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure

Dalším způsobem, jak prozkoumat prostředky Azure, je prostřednictvím [Průzkumníka prostředků Azure](https://resources.azure.com) (Preview). Tento nástroj používá kontext vašeho předplatného, takže je potřeba ověřit na webu pomocí přihlašovacích údajů Azure. Po ověření můžete procházet podle zprostředkovatelů, předplatných, skupin prostředků a prostředků.

Vyhledejte prostředek účtu úložiště a podívejte se na vlastnosti. Vidíme **podporujeHttpsTrafficOnly** vlastnost zde také. Když vyberete kartu **Dokumentace,** zjistíme, že popis vlastnosti odpovídá tomu, co jsme našli v předchozích referenčních dokumentech.

## <a name="find-the-property-alias"></a>Najít alias vlastnosti

Identifikovali jsme vlastnost prostředku, ale musíme ji namapovat na [alias](../concepts/definition-structure.md#aliases).

Existuje několik způsobů, jak určit aliasy pro prostředek Azure. Podíváme se na každý pro tento kurz:

- Rozšíření Azure Policy pro VS Code
- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="get-aliases-in-vs-code-extension"></a>Získat aliasy v rozšíření VS Code

Rozšíření zásad Azure pro rozšíření VS Code usnadňuje procházení prostředků a [zjišťování aliasů](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

### <a name="azure-cli"></a>Azure CLI

V Azure CLI `az provider` skupina příkazů se používá k hledání aliasů prostředků. Vyfiltrujeme obor názvů **Microsoft.Storage** na základě podrobností, které jsme získali o prostředku Azure dříve.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Ve výsledcích vidíme alias podporovaný účty úložiště s názvem **supportsHttpsTrafficOnly**. Tato existence tohoto aliasu znamená, že můžeme napsat zásady k prosazení našich obchodních požadavků!

### <a name="azure-powershell"></a>Azure PowerShell

V Prostředí Azure `Get-AzPolicyAlias` PowerShell se rutina používá k vyhledávání aliasů prostředků. Vyfiltrujeme obor názvů **Microsoft.Storage** na základě podrobností, které jsme získali o prostředku Azure dříve.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Stejně jako Azure CLI, výsledky zobrazit alias podporovaný účty úložiště s názvem **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) je služba, která poskytuje jinou metodu k vyhledání vlastností prostředků Azure. Tady je ukázkový dotaz pro zobrazení jednoho účtu úložiště pomocí grafu prostředků:

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Výsledky vypadají podobně jako to, co vidíme v šablonách Správce prostředků a prostřednictvím Průzkumníka prostředků Azure. Výsledky Azure Resource Graph však mohou také obsahovat podrobnosti [aliasu](../concepts/definition-structure.md#aliases) _promítnutím_ pole _aliasů:_

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Zde je příklad výstupu z účtu úložiště pro aliasy:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

Azure Resource Graph lze použít prostřednictvím [cloudového prostředí](https://shell.azure.com), takže je rychlý a snadný způsob, jak prozkoumat vlastnosti vašich prostředků.

## <a name="determine-the-effect-to-use"></a>Určení efektu, který se má použít

Rozhodování o tom, co dělat s nekompatibilními prostředky, je téměř stejně důležité jako rozhodnutí, co vyhodnotit na prvním místě. Každá možná odpověď na nekompatibilní prostředek se nazývá [efekt](../concepts/effects.md). Efekt řídí, pokud je nekompatibilní prostředek protokolován, blokován, má připojená data nebo má k němu přidružené nasazení pro uvedení prostředku zpět do kompatibilního stavu.

V našem příkladu Deny je efekt, který chceme, protože nechceme nekompatibilní prostředky vytvořené v našem prostředí Azure. Audit je dobrou první volbou pro efekt zásad k určení, jaký je dopad zásady před nastavením na hodnotu Odepřít. Jedním ze způsobů, jak usnadnit změnu efektu na přiřazení, je parametrizovat efekt. [Podrobnosti](#parameters) o tom, jak.

## <a name="compose-the-definition"></a>Sestavit definici

Nyní máme údaje o nemovitosti a alias pro to, co máme v plánu spravovat. Dále spotřebujeme samotné pravidlo zásad. Pokud ještě nejste obeznámeni s jazykem zásad, odkaz [na strukturu definice zásad,](../concepts/definition-structure.md) jak strukturovat definici zásad. Zde je prázdná šablona, jak definice zásad vypadá:

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

První tři součásti jsou metadata zásad. Tyto komponenty jsou snadno zadat hodnoty, protože víme, co vytváříme pravidlo. [Režim](../concepts/definition-structure.md#mode) je především o značky a umístění prostředků. Vzhledem k tomu, že nepotřebujeme omezit hodnocení na prostředky, které podporují značky, použijeme _hodnotu all_ pro **režim**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametry

I když jsme nepoužili parametr pro změnu hodnocení, chceme použít parametr, který umožní změnu **efektu** řešení potíží. Definujeme parametr **effectType** a omezíme jej pouze **na Odepřít** a **Zakázáno**. Tyto dvě možnosti odpovídají našim obchodním požadavkům. Blok dokončených parametrů vypadá takto:

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

Vytvoření [pravidla zásad](../concepts/definition-structure.md#policy-rule) je posledním krokem při vytváření naší vlastní definice zásad. Identifikovali jsme dva příkazy, které je třeba otestovat:

- Že **typ** účtu úložiště je **Microsoft.Storage/storageAccounts**
- Že účet úložiště **podporujeHttpsTrafficOnly** není **true**

Vzhledem k tomu, že potřebujeme oba tyto příkazy, aby byla pravdivá, budeme používat **allOf** [logický operátor](../concepts/definition-structure.md#logical-operators). Předáme **effectType** parametr efekt namísto provádění statické deklarace. Naše hotové pravidlo vypadá takto:

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

### <a name="completed-definition"></a>Dokončená definice

Se všemi třemi částmi definované zásady, zde je naše dokončená definice:

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

Dokončenou definici lze použít k vytvoření nové zásady. Portál a každá sada SDK (Azure CLI, Azure PowerShell a ROZHRANÍ REST API) přijímají definici různými způsoby, proto zkontrolujte příkazy pro každý z nich a ověřte správné využití. Pak ji přiřaďte pomocí parametrizovaného efektu příslušným prostředkům pro správu zabezpečení účtů úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste práci s prostředky z tohoto kurzu skončili, odstraňte některá z výše vytvořených přiřazení nebo definic pomocí následujících kroků:

1. V části **Vytváření** na levé straně stránky Zásad Azure vyberte **Definice** (nebo **přiřazení,** pokud se pokoušíte odstranit přiřazení).

1. Vyhledejte novou definici iniciativy nebo zásady (nebo přiřazení), kterou chcete odebrat.

1. Klikněte na řádek pravým tlačítkem nebo vyberte tři tečky na konci definice (nebo přiřazení) a pak vyberte **Odstranit definici** (nebo **Odstranit přiřazení**).

## <a name="review"></a>Revize

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Identifikovali jste své obchodní požadavky
> - Mapována každý požadavek na vlastnost prostředku Azure
> - Namapována vlastnost na alias
> - Byl určen účinek, který se má použít
> - Skládá se z definice politiky

## <a name="next-steps"></a>Další kroky

Dále použijte vlastní definici zásad k vytvoření a přiřazení zásady:

> [!div class="nextstepaction"]
> [Vytvoření a přiřazení definice zásady](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)