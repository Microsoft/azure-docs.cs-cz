---
title: Změnit typ členství ve skupině statické na dynamickou ve službě Azure Active Directory | Dokumentace Microsoftu
description: Jak vytvořit pravidla členství pro skupiny a odkaz se automaticky vyplní.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/01/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 11175a091ad610470632b52b46270ed27c76e452
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150627"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Změnit členství ve skupině statické na dynamickou ve službě Azure Active Directory

Členství můžete změnit ze statické na dynamickou (nebo naopak) ve službě Azure Active Directory (Azure AD). Azure AD zajišťuje stejný název skupiny a ID systému, takže všechny existující odkazy na skupiny jsou stále platné. Pokud místo toho vytvořit novou skupinu, musíte k aktualizaci těchto odkazů. Členství v dynamické skupině eliminuje správu režie přidávání a odebírání uživatelů. Tento článek vysvětluje, jak převést existující skupiny ze statické na dynamické členství pomocí centra pro správu Azure AD nebo rutiny prostředí PowerShell.

> [!WARNING]
> Při změně existující skupinu statické na dynamickou skupinu, všech stávajících členů se odeberou ze skupiny, a pak zpracování pravidla členství pro přidání nových členů. Pokud skupině se používá k řízení přístup k aplikacím a prostředkům, mějte na paměti, že původní členy možná ztratíte přístup, dokud budou plně zpracovány pravidla členství.
>
> Doporučujeme, abyste otestovali pravidla členství v předem abyste měli jistotu, že je nový členství ve skupině podle očekávání.

## <a name="change-the-membership-type-for-a-group"></a>Změnit typ členství pro skupinu

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je globální správce nebo správce uživatelských účtů ve vašem tenantovi.
2. Vyberte **skupiny**.
3. Z **všechny skupiny** seznamu, otevřete skupinu, kterou chcete změnit.
4. Vyberte **vlastnosti**.
5. Na **vlastnosti** stránce pro skupinu, vyberte **typ členství** přiřazeno (statické), dynamické uživatele nebo dynamické zařízení, v závislosti na typu požadovaného členství. Pro dynamické členství můžete použít Tvůrce pravidlo a vyberte možnosti pro jednoduché pravidlo nebo pravidla členství napsat sami. 

Následující postup je příklad změny skupiny ze statické na dynamické členství ve skupině uživatelů.

1. Na **vlastnosti** stránky pro vybranou skupinu, vyberte **typ členství** z **dynamický uživatel**, vyberte Ano v dialogovém okně s vysvětlením změn do skupiny členství na pokračovat. 
  
   ![Vyberte typ členství v dynamické uživatele](./media/groups-change-type/select-group-to-convert.png)
  
2. Vyberte **Přidat dynamický dotaz**a pak zadejte pravidlo.
  
   ![Zadejte pravidla](./media/groups-change-type/enter-rule.png)
  
3. Po vytvoření pravidla, vyberte **přidat dotaz** v dolní části stránky.
4. Vyberte **Uložit** na **vlastnosti** stránce pro skupinu, kterou chcete uložit provedené změny. **Typ členství** skupiny okamžitě aktualizován v seznamu skupin.

> [!TIP]
> Převod skupin může selhat, pokud byl nesprávný pravidla členství, které jste zadali. Oznámení se zobrazí v pravém dolním rohu portálu, který obsahuje vysvětlení, proč nelze přijmout pravidlo v systému. Přečtěte si ho pečlivě, abyste pochopili, jak můžete nastavit pravidlo, aby byl platný. Příklady syntaxe pravidla a úplný seznam podporovaných vlastností, operátory a hodnoty pro pravidlo členství, naleznete v tématu [pravidla dynamického členství pro skupiny ve službě Azure Active Directory](groups-dynamic-membership.md).


## <a name="change-membership-type-for-a-group-powershell"></a>Změnit typ členství pro skupinu (PowerShell)

> [!NOTE]
> Chcete-li změnit vlastnosti dynamické skupiny, budete muset použít rutiny z **ve verzi preview** [Azure AD PowerShell verze 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0). Můžete nainstalovat verzi preview z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Tady je příklad funkce, které se přepnout na existující skupinu správy členství. V tomto příkladu je správně pracovat vlastnost GroupTypes a zachovat všechny hodnoty, které nesouvisí s dynamického členství věnovat pozornost.

```
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

```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Chcete-li dynamické skupiny:

```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Další postup

Tyto články poskytují další informace o skupinách ve službě Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
