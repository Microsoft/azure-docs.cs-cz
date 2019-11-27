---
title: Obnovení odstraněné skupiny Office 365 – Azure AD | Microsoft Docs
description: Postup obnovení odstraněné skupiny, zobrazení skupin obnovitelné a trvalé odstranění skupiny v Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d212df51a58125e3b959a18f5cf2ac9d391d30
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422372"
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Obnovení odstraněné skupiny Office 365 v Azure Active Directory

Když odstraníte skupinu Office 365 v Azure Active Directory (Azure AD), uchovává se tato odstraněná skupina po dobu 30 dnů od data odstranění, ale není viditelná. Je to kvůli tomu, abyste v případě potřeby mohli skupinu, včetně jejího obsahu, obnovit. Tato funkce je omezená výhradně pro skupiny Office 365 v Azure AD. Není dostupná pro skupiny zabezpečení a distribuční skupiny. Počítejte s tím, že doba obnovení skupiny není přizpůsobená na 30 dní.

> [!NOTE]
> Nepoužívejte rutinu `Remove-MsolGroup`, protože tato rutina danou skupinu trvale vymaže. K odstranění skupiny Office 365 vždy použijte `Remove-AzureADMSGroup`.

K obnovení skupiny jsou potřebná některá z následujících oprávnění:

Role | Oprávnění
--------- | ---------
Globální správce, správce skupin, Podpora partnerů 2 a správce Intune | Můžou obnovit jakoukoli odstraněnou skupinu Office 365.
Podpora správců uživatelů a partnerů Tier1 | Může obnovit všechny odstraněné skupiny Office 365 s výjimkou těchto skupin přiřazených k roli správce společnosti.
Uživatel | Může obnovit všechny odstraněné skupiny Office 365, které vlastní.

## <a name="view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore"></a>Umožňuje zobrazit a spravovat odstraněné skupiny Office 365, které jsou k dispozici pro obnovení.

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele.

2. Vyberte **skupiny**a pak vyberte **Odstraněné skupiny** a zobrazte odstraněné skupiny, které jsou k obnovení k dispozici.

    ![Zobrazit skupiny, které jsou k dispozici pro obnovení](media/groups-lifecycle/deleted-groups3.png)

3. V okně **Odstraněné skupiny** můžete:

   - Obnovte odstraněnou skupinu a její obsah výběrem možnosti **obnovit skupinu**.
   - Odstraněnou skupinu trvale odeberte výběrem možnosti **odstranit trvale**. Pokud chcete skupinu trvale odebrat, musíte mít oprávnění správce.

## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore-using-powershell"></a>Zobrazit odstraněné skupiny Office 365, které jsou k dispozici pro obnovení pomocí prostředí PowerShell

K zobrazení odstraněných skupin a ověření, že ty, které vás zajímají, ještě nejsou trvale vymazané, můžete použít následující rutiny. Tyto rutiny jsou součástí [modulu Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/). Další informace o tomto modulu najdete v článku [Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0).

1.  Spuštěním následující rutiny zobrazíte všechny odstraněné skupiny Office 365 ve vašem tenantovi, které je ještě možné obnovit.
   

    ```powershell
    Get-AzureADMSDeletedGroup
    ```

2.  Pokud znáte identifikátor objectID konkrétní skupiny (a můžete ho získat z rutiny v kroku 1), můžete ověřit, že daná konkrétní rutina ještě není trvale vymazaná, také spuštěním následující rutiny.

    ```
    Get-AzureADMSDeletedGroup –Id <objectId>
    ```

## <a name="how-to-restore-your-deleted-office-365-group-using-powershell"></a>Postup obnovení odstraněné skupiny Office 365 pomocí prostředí PowerShell

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

Pokud chcete ověřit, že jste skupinu Office 365 úspěšně obnovili, spusťte rutinu `Get-AzureADGroup –ObjectId <objectId>`, která zobrazí informace o dané skupině. Po dokončení žádosti o obnovení:

- Skupina se zobrazí v levém navigačním panelu na Exchangi
- Plán pro danou skupinu se zobrazí v Planneru.
- Budou k dispozici všechny weby SharePointu a veškerý jejich obsah.
- Daná skupina bude přístupná ze všech koncových bodů Exchange a jiných úloh Office 365, které podporují skupiny Office 365.

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o skupinách Azure Active Directory.

* [Zobrazení existujících skupin](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Správa nastavení skupiny](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Správa členství ve skupině](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](groups-dynamic-membership.md)
