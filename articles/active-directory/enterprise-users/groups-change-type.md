---
title: Změna členství ve statické skupině na dynamické – Azure AD | Microsoft Docs
description: Naučte se, jak převést existující skupiny ze statického na dynamické členství pomocí centra pro správu Azure AD nebo rutin PowerShellu.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860877"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Změňte členství v statických skupinách na dynamické v Azure Active Directory

Členství ve skupině můžete změnit ze statické na dynamickou (nebo naopak) v Azure Active Directory (Azure AD). Azure AD udržuje stejný název skupiny a ID v systému, takže všechny existující odkazy na tuto skupinu jsou pořád platné. Pokud místo toho vytvoříte novou skupinu, budete muset tyto odkazy aktualizovat. Dynamické členství ve skupinách eliminuje režii správy přidávání a odebírání uživatelů. V tomto článku se dozvíte, jak převést existující skupiny ze statického do dynamického členství pomocí centra pro správu Azure AD nebo rutin PowerShellu.

> [!WARNING]
> Když změníte stávající statickou skupinu na dynamickou skupinu, všechny existující členy se ze skupiny odeberou a pak se zpracuje pravidlo členství, aby se přidaly nové členy. Pokud se skupina používá k řízení přístupu k aplikacím nebo prostředkům, uvědomte si, že původní členové mohou ztratit přístup, dokud nebude pravidlo členství plně zpracováno.
>
> Doporučujeme, abyste nové pravidlo členství otestovali předem, abyste se ujistili, že nové členství ve skupině je podle očekávání.

## <a name="change-the-membership-type-for-a-group"></a>Změna typu členství pro skupinu

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem, správcem uživatelů nebo správci skupin ve vaší organizaci Azure AD.
2. Vyberte **skupiny**.
3. V seznamu **všechny skupiny** otevřete skupinu, kterou chcete změnit.
4. Vyberte **Vlastnosti**.
5. Na stránce **vlastnosti** pro skupinu vyberte **typ členství** přiřazený (statický), dynamický uživatel nebo dynamické zařízení v závislosti na požadovaném typu členství. Pro dynamické členství můžete použít Tvůrce pravidel a vybrat možnosti jednoduchého pravidla nebo zapsat pravidlo členství sami. 

Následující kroky jsou příkladem změny skupiny ze statického na dynamické členství pro skupinu uživatelů.

1. Na stránce **vlastnosti** pro vybranou skupinu vyberte **typ členství** **dynamického uživatele** a potom v dialogovém okně vyberte Ano, aby bylo možné pokračovat v členství ve skupině. 
  
   ![Vyberte typ členství dynamického uživatele.](./media/groups-change-type/select-group-to-convert.png)
  
2. Vyberte **Přidat dynamický dotaz** a pak zadejte pravidlo.
  
   ![zadejte pravidlo pro dynamickou skupinu.](./media/groups-change-type/enter-rule.png)
  
3. Po vytvoření pravidla vyberte v dolní části stránky **Přidat dotaz** .
4. Vyberte **Uložit** na stránce **vlastnosti** pro skupinu a uložte provedené změny. **Typ členství** v této skupině se hned aktualizuje v seznamu skupin.

> [!TIP]
> Převod skupiny může selhat, pokud zadané pravidlo členství není správné. V pravém horním rohu portálu se zobrazí oznámení, které obsahuje vysvětlení, proč se pravidlo nemůže přijmout v systému. Přečtěte si pečlivě, abyste zjistili, jak můžete pravidlo upravit, aby se zajistilo jeho platnost. Příklady syntaxe pravidla a úplný seznam podporovaných vlastností, operátorů a hodnot pro pravidlo členství najdete v tématu [dynamická pravidla členství pro skupiny v Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Změna typu členství pro skupinu (PowerShell)

> [!NOTE]
> Pokud chcete změnit vlastnosti dynamické skupiny, budete muset použít rutiny z **verze Preview** [služby Azure AD PowerShell verze 2](/powershell/azure/active-directory/install-adv2). Verzi Preview můžete nainstalovat z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Tady je příklad funkcí, které přepínají správu členství v existující skupině. V tomto příkladu je potřeba zajistit správnou manipulaci s vlastností GroupTypes a zachovat všechny hodnoty, které nesouvisí s dynamickým členstvím.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Chcete-li nastavit skupinu jako statickou:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Chcete-li nastavit skupinu jako dynamickou:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Další kroky

Tyto články poskytují další informace o skupinách v Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
