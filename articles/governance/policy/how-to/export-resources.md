---
title: Export prostředků Azure Policy
description: Naučte se exportovat Azure Policy prostředky do GitHubu, jako jsou definice zásad a přiřazení zásad.
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c16ceed755cab3228b8f9e401f486a0629f3a60d
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93025710"
---
# <a name="export-azure-policy-resources"></a>Export prostředků Azure Policy

Tento článek poskytuje informace o tom, jak exportovat stávající prostředky Azure Policy. Export vašich prostředků je užitečný a doporučený pro zálohování, ale je také důležitým krokem ve vaší cestě ke správě cloudu a zpracovávání [zásad jako kódu](../concepts/policy-as-code.md). Prostředky Azure Policy můžete exportovat prostřednictvím [Azure Portal](#export-with-azure-portal), [Azure CLI](#export-with-azure-cli), [Azure PowerShell](#export-with-azure-powershell)a každé z podporovaných sad SDK.

## <a name="export-with-azure-portal"></a>Exportovat pomocí Azure Portal

Pokud chcete exportovat definici zásady z Azure Portal, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady** .

1. Na levé straně stránky Azure Policy vyberte **definice** .

1. Použijte tlačítko **exportovat definice** nebo vyberte tři tečky na řádku definice zásady a pak vyberte **Exportovat definici** .

1. Vyberte tlačítko **Přihlásit se pomocí GitHubu** . Pokud jste se ještě neověřili pomocí GitHubu k autorizaci Azure Policy k exportu prostředku, přečtěte si téma přístup k [akci GitHubu](https://github.com/features/actions) v novém okně, které se otevře, a vyberte **autorizovat AzureGitHubActions** , aby bylo možné pokračovat v procesu exportu. Po dokončení se nové okno automaticky zavře.

1. Na kartě **základy** nastavte následující možnosti a potom v dolní části stránky vyberte kartu **zásady** nebo tlačítko **Další: zásady** .

   - **Filtr úložiště** : nastavte na _Moje úložiště_ , abyste viděli jenom úložiště, která vlastníte, nebo _všechna úložiště_ , abyste viděli všechna oprávnění k přístupu k akci GitHubu.
   - **Úložiště** : nastavte na úložiště, do kterého chcete exportovat prostředky Azure Policy.
   - **Větev** : nastavte větev v úložišti. Použití jiné než výchozí větve je dobrým způsobem, jak ověřit aktualizace před tím, než se sloučí do zdrojového kódu.
   - **Adresář** : _Složka kořenové úrovně_ , do které se mají exportovat prostředky Azure Policy. V závislosti na tom, jaké prostředky se exportují, se vytvoří podsložky v tomto adresáři.

1. Na kartě **zásady** nastavte obor pro hledání tak, že vyberete tři tečky a vybíráte kombinaci skupin pro správu, předplatných nebo skupin prostředků.
   
1. Pomocí tlačítka **přidat definice zásad** můžete vyhledat obor, pro který chcete exportovat objekty. V okně, které se otevře, vyberte každý objekt, který chcete exportovat. Vyfiltruje výběr pomocí vyhledávacího pole nebo typu. Po výběru všech objektů k exportu použijte tlačítko **Přidat** v dolní části stránky.

1. U každého vybraného objektu vyberte požadované možnosti exportu, jako je například _jenom definice_ nebo _definice a přiřazení_ pro definici zásady. Pak vyberte kartu **recenze + export** nebo další: v dolní části stránky klikněte na tlačítko **Revize + exportovat** .

   > [!NOTE]
   > Pokud je zvolená _definice a přiřazení_ možnosti, exportují se jenom přiřazení zásad v oboru nastaveném filtrem při přidání definice zásady.

1. Na kartě **Revize + export** Zkontrolujte shodu podrobností a pak použijte tlačítko **exportovat** v dolní části stránky.

1. Zkontrolujte, zda jsou vybrané prostředky nyní exportovány do vašeho zdrojového kódu, ve složce vašeho úložiště GitHub, větve a _kořenové úrovně_ .

Prostředky Azure Policy jsou exportovány do následující struktury v rámci vybraného úložiště GitHub a _složky na kořenové úrovni_ :

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Export pomocí Azure CLI

Azure Policy definice, iniciativy a přiřazení se dají exportovat jako JSON pomocí [Azure CLI](/cli/azure/install-azure-cli). Každý z těchto příkazů používá parametr **Name** k určení, který objekt má získat JSON pro. Vlastnost **Name** je často _identifikátor GUID_ a není **DisplayName** objektu.

- Definice – [AZ Policy definition show](/cli/azure/policy/definition#az-policy-definition-show)
- Iniciativa- [AZ Policy set-definition show](/cli/azure/policy/set-definition#az-policy-set-definition-show)
- Přiřazení – [AZ Policy Assignment show](/cli/azure/policy/assignment#az-policy-assignment-show)

Tady je příklad získání formátu JSON pro definici zásady s **názvem** _VirtualMachineStorage_ :

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportovat pomocí Azure PowerShell

Azure Policy definice, iniciativy a přiřazení lze exportovat jako JSON s [Azure PowerShell](/powershell/azure/). Každá z těchto rutin používá parametr **Name** k určení objektu, pro který se má načíst JSON. Vlastnost **Name** je často _identifikátor GUID_ a není **DisplayName** objektu.

- Definice- [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Iniciativa- [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Přiřazení – [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Tady je příklad získání formátu JSON pro definici zásady s **názvem** _VirtualMachineStorage_ :

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
