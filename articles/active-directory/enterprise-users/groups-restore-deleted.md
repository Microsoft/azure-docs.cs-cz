---
title: Obnovení odstraněné skupiny Microsoft 365 – Azure AD | Microsoft Docs
description: Postup obnovení odstraněné skupiny, zobrazení skupin obnovitelné a trvalé odstranění skupiny v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1eacba47e68310dd45976bcd9ddff052fa193436
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090133"
---
# <a name="restore-a-deleted-microsoft-365-group-in-azure-active-directory"></a>Obnovení odstraněné skupiny Microsoft 365 v Azure Active Directory

Když odstraníte skupinu Microsoft 365 v Azure Active Directory (Azure AD), Odstraněná skupina se zachová, ale po dobu 30 dnů od data odstranění se nezobrazuje. Je to kvůli tomu, abyste v případě potřeby mohli skupinu, včetně jejího obsahu, obnovit. Tato funkce je omezená výhradně na Microsoft 365 skupiny v Azure AD. Není dostupná pro skupiny zabezpečení a distribuční skupiny. Počítejte s tím, že doba obnovení skupiny není přizpůsobená na 30 dní.

> [!NOTE]
> Nepoužívejte rutinu `Remove-MsolGroup`, protože tato rutina danou skupinu trvale vymaže. Vždy použijte `Remove-AzureADMSGroup` k odstranění Microsoft 365 skupiny.

K obnovení skupiny jsou potřebná některá z následujících oprávnění:

Role | Oprávnění
--------- | ---------
Globální správce, správce skupin, Podpora partnerů 2 a správce Intune | Může obnovit všechny odstraněné Microsoft 365 skupiny.
Podpora správců uživatelů a partnerů Tier1 | Může obnovit všechny odstraněné Microsoft 365 skupiny s výjimkou těchto skupin přiřazených k roli globálního správce.
User | Může obnovit všechny odstraněné Microsoft 365 skupiny, které vlastní.

## <a name="view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore"></a>Umožňuje zobrazit a spravovat odstraněné Microsoft 365 skupiny, které jsou k dispozici pro obnovení.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele.

2. Vyberte **skupiny** a pak vyberte **Odstraněné skupiny** a zobrazte odstraněné skupiny, které jsou k obnovení k dispozici.

    ![Zobrazit skupiny, které jsou k dispozici pro obnovení](./media/groups-restore-deleted/deleted-groups3.png)

3. V okně **Odstraněné skupiny** můžete:

   - Obnovte odstraněnou skupinu a její obsah výběrem možnosti **obnovit skupinu**.
   - Odstraněnou skupinu trvale odeberte výběrem možnosti **odstranit trvale**. Pokud chcete skupinu trvale odebrat, musíte mít oprávnění správce.

## <a name="view-the-deleted-microsoft-365-groups-that-are-available-to-restore-using-powershell"></a>Zobrazit odstraněné Microsoft 365 skupiny, které jsou k dispozici pro obnovení pomocí prostředí PowerShell

K zobrazení odstraněných skupin a ověření, že ty, které vás zajímají, ještě nejsou trvale vymazané, můžete použít následující rutiny. Tyto rutiny jsou součástí [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Další informace o tomto modulu najdete v článku [Azure Active Directory PowerShell verze 2](/powershell/azure/active-directory/install-adv2).

1.  Spuštěním následující rutiny zobrazte všechny odstraněné Microsoft 365 skupiny ve vaší organizaci Azure AD, které jsou pořád dostupné k obnovení.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Pokud znáte identifikátor objectID konkrétní skupiny (a můžete ho získat z rutiny v kroku 1), můžete ověřit, že daná konkrétní rutina ještě není trvale vymazaná, také spuštěním následující rutiny.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-microsoft-365-group-using-powershell"></a>Jak obnovit odstraněnou Microsoft 365 skupinu pomocí PowerShellu

Když jste ověřili, že příslušnou skupinu je ještě možné obnovit, obnovte tuto odstraněnou skupinu některým z následujících postupů. Pokud daná skupina obsahuje dokumenty, weby SP nebo jiné trvalé objekty, může úplné obnovení této skupiny a jejího obsahu trvat až 24 hodin.

1. Obnovte danou skupinu a její obsah spuštěním následující rutiny.
 

   ```
    Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
    ``` 

2. Odstraněnou skupinu lze případně trvale odebrat spuštěním následující rutiny.
    

    ```
    Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
    ```

## <a name="how-do-you-know-this-worked"></a>Jak zjistíte, že obnovení bylo úspěšné?

Pokud chcete ověřit, že jste úspěšně obnovili Microsoft 365 skupinu, spusťte `Get-AzureADGroup –ObjectId <objectId>` rutinu, aby se zobrazily informace o této skupině. Po dokončení žádosti o obnovení:

- Skupina se zobrazí v levém navigačním panelu na Exchangi
- Plán pro danou skupinu se zobrazí v Planneru.
- Budou k dispozici všechny weby SharePointu a veškerý jejich obsah.
- Skupina je dostupná z libovolného koncového bodu Exchange a dalších úloh Microsoft365, které podporují skupiny Microsoft 365.

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o skupinách Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
