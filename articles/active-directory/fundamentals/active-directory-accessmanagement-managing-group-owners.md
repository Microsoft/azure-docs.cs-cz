---
title: Přidat nebo odebrat vlastníky skupiny – Azure Active Directory | Microsoft Docs
description: Pokyny, jak přidat nebo odebrat vlastníky skupin pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30b547ecd4ff76e650d7be837fa5bce03046da50
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318586"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Přidat nebo odebrat vlastníky skupiny v Azure Active Directory
Skupiny Azure Active Directory (Azure AD) jsou vlastněné a spravované vlastníky skupin. Vlastníci skupiny můžou být uživatelé nebo instanční objekty a můžou spravovat skupinu včetně členství. Vlastníci skupiny můžou přiřadit jenom existující vlastníci skupiny nebo skupiny – Správa správců. Vlastníci skupiny nemusí být členy skupiny.

Pokud skupina nemá žádného vlastníka, skupina – Správa správců stále dokáže spravovat skupinu. Doporučuje se, aby každá skupina měla aspoň jednoho vlastníka. Jakmile se vlastníci přiřadit do skupiny, nepůjde odebrat posledního vlastníka skupiny. Než odeberete posledního vlastníka ze skupiny, ujistěte se prosím, že jste vybrali jiného vlastníka.

## <a name="add-an-owner-to-a-group"></a>Přidat vlastníka do skupiny
Níže jsou uvedeny pokyny pro přidání uživatele jako vlastníka do skupiny pomocí portálu Azure AD. Pokud chcete přidat instanční objekt jako vlastníka skupiny, postupujte podle pokynů v tématu použití [PowerShellu](/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Přidání vlastníka skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, vyberte **skupiny**a pak vyberte skupinu, pro kterou chcete přidat vlastníka (v tomto příkladu *zásady MDM – západ*).

3. Na stránce **Přehled zásad MDM – západ** vyberte **vlastníci**.

    ![Zásady MDM – Stránka s přehledem s vybranými vlastníky – západ](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na stránce **zásady MDM – západ – vlastníci** vyberte **Přidat vlastníky**a pak vyhledejte a vyberte uživatele, který bude novým vlastníkem skupiny, a pak zvolte **Vybrat**.

    ![Zásada MDM – stránka – západ – vlastníci s zvýrazněnou možností přidat vlastníky](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po výběru nového vlastníka můžete aktualizovat stránku **vlastníci** a zobrazit název přidaný do seznamu vlastníků.

## <a name="remove-an-owner-from-a-group"></a>Odebrání vlastníka ze skupiny
Odebrání vlastníka ze skupiny pomocí Azure AD

### <a name="to-remove-an-owner"></a>Odebrání vlastníka
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, vyberte **skupiny**a pak vyberte skupinu, pro kterou chcete odebrat vlastníka (v tomto příkladu *zásady MDM – západ*).

3. Na stránce **Přehled zásad MDM – západ** vyberte **vlastníci**.

    ![Zásady MDM – Stránka s přehledem s vybranými vlastníky – západ](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na stránce **zásady MDM – západ – vlastníci** vyberte uživatele, kterého chcete odebrat jako vlastníka skupiny, zvolte ze stránky informace o uživateli možnost **Odebrat** a potvrďte své rozhodnutí kliknutím na **Ano** .

    ![Stránka informace o uživateli s zvýrazněnou možností odebrat](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po odebrání vlastníka se můžete vrátit na stránku **vlastníci** a podívat se, že je název odebraný ze seznamu vlastníků.

## <a name="next-steps"></a>Další kroky
- [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)

- [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md)

- [Přiřazení přístupu k integrované aplikaci SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)

- [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md)