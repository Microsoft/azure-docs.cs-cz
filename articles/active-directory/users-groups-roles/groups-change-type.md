---
title: Změna statického členství ve skupině na dynamické – Azure AD | Dokumenty společnosti Microsoft
description: Jak vytvořit pravidla členství pro automatické vyplnění skupin a odkaz na pravidlo.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34451fcc4597f77464e5e9566613e21e9fecdbc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74027313"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Změna členství ve statické skupině na dynamické ve službě Azure Active Directory

Členství skupiny můžete změnit ze statického na dynamické (nebo naopak) ve službě Azure Active Directory (Azure AD). Azure AD udržuje stejný název skupiny a ID v systému, takže všechny existující odkazy na skupinu jsou stále platné. Pokud místo toho vytvoříte novou skupinu, budete muset tyto odkazy aktualizovat. Dynamické členství ve skupinách eliminuje režii správy při přidávání a odebírání uživatelů. Tento článek vám řekne, jak převést existující skupiny ze statického na dynamické členství pomocí Centra pro správu Azure AD nebo rutin prostředí PowerShell.

> [!WARNING]
> Při změně existující statické skupiny na dynamickou skupinu jsou ze skupiny odebráni všichni existující členové a pak je zpracováno pravidlo členství pro přidání nových členů. Pokud se skupina používá k řízení přístupu k aplikacím nebo prostředkům, uvědomte si, že původní členové mohou ztratit přístup, dokud nebude pravidlo členství plně zpracováno.
>
> Doporučujeme předem otestovat nové pravidlo členství a ujistěte se, že nové členství ve skupině je podle očekávání.

## <a name="change-the-membership-type-for-a-group"></a>Změna typu členství pro skupinu

1. Přihlaste se k [Centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globálním správcem nebo správcem uživatelů ve vašem tenantovi.
2. Vyberte **skupiny**.
3. V seznamu **Všechny skupiny** otevřete skupinu, kterou chcete změnit.
4. Vyberte **vlastnosti**.
5. Na stránce **Vlastnosti** skupiny vyberte **typ členství** přiřazeného (statického), dynamického uživatele nebo dynamického zařízení v závislosti na požadovaném typu členství. Pro dynamické členství můžete pomocí tvůrce pravidel vybrat možnosti pro jednoduché pravidlo nebo napsat pravidlo členství sami. 

Následující kroky jsou příkladem změny skupiny ze statického na dynamické členství pro skupinu uživatelů.

1. Na stránce **Vlastnosti** pro vybranou skupinu vyberte **typ členství** **dynamického uživatele**a v dialogovém okně, ve kterém vysvětlíte změny členství ve skupině, vyberte ano. 
  
   ![vybrat typ členství dynamického uživatele](./media/groups-change-type/select-group-to-convert.png)
  
2. Vyberte **Přidat dynamický dotaz**a zadejte pravidlo.
  
   ![zadejte pravidlo pro dynamickou skupinu](./media/groups-change-type/enter-rule.png)
  
3. Po vytvoření pravidla vyberte **Přidat dotaz** v dolní části stránky.
4. Vyberte **Uložit** na stránce **Vlastnosti** pro skupinu, chcete-li uložit změny. **Typ členství** skupiny je okamžitě aktualizován v seznamu skupin.

> [!TIP]
> Převod skupiny může selhat, pokud bylo zadané pravidlo členství nesprávné. V pravém horním rohu portálu se zobrazí oznámení, že obsahuje vysvětlení, proč pravidlo nemůže být systémem přijato. Přečtěte si ji pozorně, abyste pochopili, jak můžete upravit pravidlo tak, aby bylo platné. Příklady syntaxe pravidla a úplný seznam podporovaných vlastností, operátorů a hodnot pro pravidlo členství naleznete [v tématu Dynamic káretka členství pro skupiny ve službě Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Změna typu členství pro skupinu (PowerShell)

> [!NOTE]
> Chcete-li změnit vlastnosti dynamické skupiny, budete muset použít rutiny **z verze preview** prostředí Azure [AD PowerShell verze 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Náhled můžete nainstalovat z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Zde je příklad funkcí, které přepínají správu členství v existující skupině. V tomto příkladu je třeba dbát na správné manipulaci GroupTypes vlastnost a zachovat všechny hodnoty, které nesouvisejí s dynamické členství.

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
Chcete-li vytvořit statickou skupinu:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Jak vytvořit skupinu jako dynamickou:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Další kroky

Tyto články obsahují další informace o skupinách ve službě Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
