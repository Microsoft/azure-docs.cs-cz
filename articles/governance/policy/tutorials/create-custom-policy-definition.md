---
title: Vytvoření vlastní definice zásad
description: Vytvoření vlastní definice zásady Azure Policy pro vynucení vlastních obchodních pravidel.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/12/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: ddda2a8bf1fab4e4c48c647237617d8f705f0561
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112083"
---
# <a name="create-a-custom-policy-definition"></a>Vytvoření vlastní definice zásad

Vlastní definice zásady umožňuje zákazníkům definovat vlastní pravidla pro používání Azure. Často tato pravidla vynucují:

- Postupy zabezpečení
- Správa nákladů
- Pravidla specifická pro organizaci (například názvy nebo umístění)

Bez ohledu obchodní ovladače pro vytvoření vlastní zásady, postup je stejný pro definování nových vlastních zásad.

Před vytvořením vlastní zásadu, zkontrolujte [ukázky zásad](../samples/index.md) jestli zásadu, která odpovídá vašim potřebám již existuje.

Přístup k vytváření vlastních zásad zahrnuje následující kroky:

> [!div class="checklist"]
> - Určete vaše podnikové požadavky
> - Mapování každý požadavek na vlastnost prostředku Azure
> - Mapování vlastnosti, která má alias
> - Určit, které účinek použití
> - Vytvořit definici zásad

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="identify-requirements"></a>Identifikace požadavků

Před vytvořením definice zásady, je důležité k porozumění záměru zásad. V tomto kurzu použijeme běžné požadavky zabezpečení organizace jako cíl pro ilustraci potřebnými kroky:

- Každý účet úložiště musí být povolené pro protokol HTTPS
- Každý účet úložiště, musí se zakázat pro protokol HTTP

Vaše požadavky by se tak jasně identifikovat i "na" a stavy prostředku "nechcete mít".

Když jsme nadefinovali očekávaný stav prostředku, jsme ještě není definované, co chceme, aby provedli s předpisy. Zásady podporuje řadu [účinky](../concepts/effects.md). V tomto kurzu definujeme požadavek jako brání vytváření prostředků, pokud nejsou kompatibilní se obchodní pravidla. Aby splnila tento cíl, použijeme [Odepřít](../concepts/effects.md#deny) vliv. Chceme také mít možnost dočasně pozastavit zásady pro konkrétní úlohy. V důsledku toho použijeme [zakázané](../concepts/effects.md#disabled) projeví a ujistěte se, vliv [parametr](../concepts/definition-structure.md#parameters) v definici zásad.

## <a name="determine-resource-properties"></a>Určení vlastností prostředku

Založené na požadavcích, prostředků Azure k auditu pomocí zásad je účet úložiště.
Ale nevíme, vlastnosti, které chcete použít v definici zásad. Zásady vyhodnotí proti JSON reprezentaci prostředku, proto budeme potřebovat k pochopení vlastností dostupných na tento prostředek.

Existuje mnoho způsobů, jak určit vlastnosti pro prostředek Azure. Podíváme se na každý pro účely tohoto kurzu:

- Šablony Resource Manageru
  - Exportovat existující prostředek
  - Vytvoření prostředí
  - Šablony pro rychlý start (GitHub)
  - Referenční dokumentace šablony
- Průzkumník prostředků Azure

### <a name="resource-manager-templates"></a>Šablony Resource Manageru

Podívejte se na několika způsoby [šablony Resource Manageru](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) , který obsahuje vlastnosti, které pokud chcete spravovat.

#### <a name="existing-resource-in-the-portal"></a>Existující prostředek na portálu

Nejjednodušší způsob, jak najít vlastnosti je podívat se na existující prostředek stejného typu. Prostředky, které jsou už nakonfigurovaná s nastavením, které chcete vynutit poskytují také hodnota pro porovnání.
Podívejte se na **automatizační skript** stránky (v části **nastavení**) na webu Azure Portal pro tento konkrétní prostředek.

![Automatizační skript stránky](../media/create-custom-policy-definition/automation-script.png)

Tím pro účet úložiště zobrazí šablonu podobný tomuto příkladu:

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

V části **vlastnosti** je hodnota s názvem **supportsHttpsTrafficOnly** nastavena na **false**. Tato vlastnost pravděpodobně může být vlastnost, která hledáme. Také **typ** prostředku **Microsoft.Storage/storageAccounts.**. Typ umožňuje nám zásadu omezit na jedinou prostředky tohoto typu.

#### <a name="create-a-resource-in-the-portal"></a>Vytvořit prostředek na portálu

Jiný způsob, jak na portálu je prostředí pro vytváření prostředků. Při vytváření účtu úložiště pomocí portálu, existuje možnost **Upřesnit** karta je **zabezpečení přenosu**.
Tato vlastnost má _zakázané_ a _povoleno_ možnosti. Na informační ikonu má další text, který potvrzuje, že tato možnost je pravděpodobně vlastnost, kterou chceme. Ale na portálu nebude Řekněte nám, název vlastnosti na této obrazovce.

Na **revize + vytvořit** karta, odkaz je v dolní části stránky a **stáhnout šablonu pro automatizaci**. Vyberete odkaz otevře šablonu, která se vytvoří prostředek, který jsme nakonfigurovali. V tomto případě vidíme dvou důležitých informací:

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

Tyto informace nám řekne, typ vlastnosti a také potvrdí **supportsHttpsTrafficOnly** je vlastnost hledáme.

#### <a name="quickstart-templates-on-github"></a>Šablony QuickStart na Githubu

[Šablony rychlý start Azure](https://github.com/Azure/azure-quickstart-templates) na Githubu má stovky šablon Resource Manageru vytvořené pro různé prostředky. Šablony můžou být skvělý způsob, jak najít vlastnost prostředku, který hledáte. Některé vlastnosti může zdá se, co hledáte, ale řídí něco jiného.

#### <a name="resource-reference-docs"></a>Prostředek referenční dokumenty

K ověření **supportsHttpsTrafficOnly** je vlastnost opravit, zkontrolujte odkaz šablony Resource Manageru pro [prostředek účtu úložiště](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) na poskytovateli úložiště.
Objekt vlastností má seznam platných parametrů. Výběr [StorageAccountPropertiesCreateParameters objekt](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) odkazu zobrazuje tabulku přijatelné vlastností. **supportsHttpsTrafficOnly** je k dispozici a popis odpovídá co hledáme plnění obchodních požadavků.

### <a name="azure-resource-explorer"></a>Průzkumník prostředků Azure

Je další způsob, jak prozkoumat prostředky Azure prostřednictvím [Azure Resource Exploreru](https://resources.azure.com) (Preview). Tento nástroj používá kontext předplatného, takže je potřeba se přihlásit k webu pomocí přihlašovacích údajů Azure. Po ověření můžete procházet podle poskytovatelů, předplatná, skupiny prostředků a prostředky.

Vyhledejte prostředek účtu úložiště a podívejte se na vlastnosti. Vidíme **supportsHttpsTrafficOnly** vlastnost i zde. Výběr **dokumentaci** kartu, vidíme, že vlastnost description odpovídá naše poznatky zjištěné v referenční dokumentaci dříve.

## <a name="find-the-property-alias"></a>Najít vlastnost alias

Zjistili jsme vlastnost prostředku, ale potřebujeme k mapování na tuto vlastnost [alias](../concepts/definition-structure.md#aliases).

Existuje několik způsobů, jak určit aliasy pro prostředek Azure. Podíváme se na každý pro účely tohoto kurzu:

- Azure CLI
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>Azure CLI

V rozhraní příkazového řádku Azure `az provider` skupinu příkazů se používá k vyhledání prostředků aliasy. Budeme filtrovat pro **Microsoft.Storage** oboru názvů na základě informací jsme získali dříve o prostředků Azure.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Ve výsledcích zobrazí alias nepodporuje účty úložiště s názvem **supportsHttpsTrafficOnly**. Tato existenci tento alias znamená, že napíšeme zásadu vynucující naše obchodní požadavky!

### <a name="azure-powershell"></a>Azure PowerShell

V prostředí Azure PowerShell `Get-AzPolicyAlias` rutina se používá k vyhledání prostředků aliasy.
Budeme filtrovat pro **Microsoft.Storage** oboru názvů na základě informací jsme získali dříve o prostředků Azure.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Stejně jako Azure CLI ve výsledcích zobrazí alias nepodporuje účty úložiště s názvem **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Graph prostředků](../../resource-graph/overview.md) je nová služba ve verzi Preview. Umožňuje jiné metody k vyhledání vlastnosti prostředků Azure. Tady je ukázkový dotaz pro vyhledávání na jeden účet úložiště s grafem prostředků:

```Query
where type=~'microsoft.storage/storageaccounts' | limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Výsledky vypadat podobně jako My, co uvidí v šablonách Resource Manageru a pomocí Průzkumníka prostředků Azure. Však také obsahovat výsledky Azure Graph prostředků [alias](../concepts/definition-structure.md#aliases) podrobnosti. Tady je příklad výstupu z účtu úložiště pro aliasy:

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

Graf prostředků Azure (Preview) umožňuje použít [Cloud Shell](https://shell.azure.com), díky tomu je rychlý a snadný způsob, jak prozkoumat vlastnosti prostředků.

## <a name="determine-the-effect-to-use"></a>Ověření účinnosti zásad k použití

Rozhodování o tom, co dělat s nekompatibilní prostředky je skoro tak důležité jako rozhodování o tom, co se má vyhodnocovat na prvním místě. Každé možné odpovědi neodpovídajících prostředků je volána [efekt](../concepts/effects.md). Ovládací prvky vliv, pokud přihlášení zdrojů nekompatibilní blokovaný, má data připojí, nebo má nasazení přidružený k uvedení prostředku zpět do vyhovujícího stavu.

V našem příkladu je odepřít účinek, který chceme, aby jako tudy nekompatibilní prostředky vytvořené v našem prostředí Azure. Auditování je první vhodná pro účinku zásad k určení vlivu zásad je před nastavením na odepřít. Jedním ze způsobů, aby se změna vliv jednotlivé přiřazení jednodušší je parametrizovat účinek. Zobrazit [parametry](#parameters) níže podrobnosti o tom.

## <a name="compose-the-definition"></a>Vytvoření definice

Teď máme vlastnost podrobností a alias pro plánujeme ke správě. V dalším kroku jsme budete vytvořit pravidlo zásad samotný. Pokud ještě nejsou obeznámeni s jazyk zásad, odkazovat [struktura definic zásad](../concepts/definition-structure.md) informace o struktuře definice zásad. Tady je prázdnou šablonu z definic zásad, která bude vypadat jako:

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

První tři součásti jsou metadata zásad. Tyto součásti jsou snadno zadat hodnoty, protože budeme vědět, co vytváříme pravidlo pro. [Režim](../concepts/definition-structure.md#mode) je především o značky a umístění prostředků. Protože jsme se nemusíte omezit na prostředky, které podporují značky vyhodnocení, použijeme _všechny_ hodnota **režimu**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parametry

Když jsme nepoužili parametr pro změnu hodnocení, chceme použít parametr umožnit změnu **efekt** pro řešení potíží. Budeme definovat **effectType** parametr a omezit na jedinou **Odepřít** a **zakázané**. Tyto dvě možnosti odpovídají naše obchodní požadavky. Blok dokončení parametry vypadá jako v tomto příkladu:

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

Sestavování [pravidlo zásad](../concepts/definition-structure.md#policy-rule) je posledním krokem při sestavování našich vlastních zásad pro definici. Zjistili jsme dva příkazy pro testování:

- Že účet úložiště **typ** je **Microsoft.Storage/storageAccounts.**
- Že účet úložiště **supportsHttpsTrafficOnly** není **true**

Protože potřebujeme oba z těchto příkazů na hodnotu true, použijeme **allOf** [logického operátoru](../concepts/definition-structure.md#logical-operators). Budete předáme **effectType** parametr vliv místo provedení statických deklarací. Naše dokončení pravidlo bude vypadat jako v tomto příkladu:

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

### <a name="completed-definition"></a>Dokončené definice

S všechny tři části zásady definované tady je naše definice dokončení:

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

Dokončené definice slouží k vytvoření nové zásady. Portál a jednotlivých sadách SDK (rozhraní příkazového řádku Azure, Azure Powershellu a rozhraní REST API) přijměte definici různými způsoby, proto zkontrolujte příkazy pro každý z nich mohl ověřit správné použití. Pak přiřaďte, pomocí parametrizovaných efekt příslušných prostředků ke správě zabezpečení vašich účtů úložiště.

## <a name="review"></a>Revize

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> - Identifikuje vaše obchodní požadavky
> - Každý požadavek namapován na vlastnost prostředku Azure
> - Vlastnost namapované na alias
> - Určuje efekt použití
> - Složený definici zásad

## <a name="next-steps"></a>Další postup

Pak pomocí definice vlastních zásad pro vytvoření a přiřazení zásad:

> [!div class="nextstepaction"]
> [Vytvoření a přiřazení definice zásady](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)