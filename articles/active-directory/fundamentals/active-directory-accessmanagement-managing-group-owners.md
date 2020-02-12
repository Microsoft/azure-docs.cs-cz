---
title: Přidání nebo odebrání vlastníků skupiny – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat nebo odebrat vlastníkům pomocí Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f83a48ad13fe4ae217319b2aa85adf976aa6a7ae
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149843"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Přidání nebo odebrání vlastníků skupin v Azure Active Directory
Skupiny Azure Active Directory (Azure AD) je vlastněna a řízena vlastníky skupiny. Vlastníci skupiny můžou být uživatelé nebo instanční objekty a můžou spravovat skupinu včetně členství. Vlastníci skupiny můžou přiřadit jenom existující vlastníci skupiny nebo skupiny – Správa správců. Vlastníci skupiny nemusejí být členy skupiny.

Pokud skupina nemá žádného vlastníka, skupina – Správa správců stále dokáže spravovat skupinu. Doporučuje se, aby každá skupina měla aspoň jednoho vlastníka. Jakmile se vlastníci přiřadit do skupiny, nepůjde odebrat posledního vlastníka skupiny. Než odeberete posledního vlastníka ze skupiny, ujistěte se prosím, že jste vybrali jiného vlastníka.

## <a name="add-an-owner-to-a-group"></a>Přidání vlastníka do skupiny
Níže jsou uvedeny pokyny pro přidání uživatele jako vlastníka do skupiny pomocí portálu Azure AD. Pokud chcete přidat instanční objekt jako vlastníka skupiny, postupujte podle pokynů v tématu použití [PowerShellu](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Chcete-li přidat vlastníka skupiny.
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, vyberte **skupiny**a pak vyberte skupinu, pro kterou chcete přidat vlastníka (v tomto příkladu *zásady MDM – západ*).

3. Na stránce **Přehled zásad MDM – západ** vyberte **vlastníci**.

    ![Zásady MDM – stránka s přehledem – západ se zvýrazněnou možností vlastníky](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na stránce **zásady MDM – západ – vlastníci** vyberte **Přidat vlastníky**a pak vyhledejte a vyberte uživatele, který bude novým vlastníkem skupiny, a pak zvolte **Vybrat**.

    ![Stránky vlastníky - západ - zásad MDM se zvýrazněnou možností přidat vlastníky](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po výběru nového vlastníka můžete aktualizovat stránku **vlastníci** a zobrazit název přidaný do seznamu vlastníků.

## <a name="remove-an-owner-from-a-group"></a>Odebrání vlastníka ze skupiny
Odebrání vlastníka ze skupiny pomocí služby Azure AD.

### <a name="to-remove-an-owner"></a>Chcete-li odebrat jako vlastníka
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, vyberte **skupiny**a pak vyberte skupinu, pro kterou chcete odebrat vlastníka (v tomto příkladu *zásady MDM – západ*).

3. Na stránce **Přehled zásad MDM – západ** vyberte **vlastníci**.

    ![Zásady MDM – stránka s přehledem – západ se zvýrazněnou možností vlastníky](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na stránce **zásady MDM – západ – vlastníci** vyberte uživatele, kterého chcete odebrat jako vlastníka skupiny, zvolte ze stránky informace o uživateli možnost **Odebrat** a potvrďte své rozhodnutí kliknutím na **Ano** .

    ![Stránka informace o uživatele se zvýrazněnou možností odstranit](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po odebrání vlastníka se můžete vrátit na stránku **vlastníci** a podívat se, že je název odebraný ze seznamu vlastníků.

## <a name="next-steps"></a>Další kroky
- [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)

- [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md)

- [Přiřazení přístupu k integrované aplikaci SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)

- [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md)
