---
title: Přidání nebo odebrání vlastníků skupin – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přidat nebo odebrat vlastníky skupin pomocí služby Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149843"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Přidání nebo odebrání vlastníků skupin ve službě Azure Active Directory
Skupiny Azure Active Directory (Azure AD) vlastní a spravují vlastníci skupin. Vlastníci skupiny mohou být uživatelé nebo instanční objekty a mohou spravovat skupinu včetně členství. Vlastníkům skupin mohou přiřadit pouze stávající vlastníci skupin nebo správci spravující skupiny. Vlastníci skupiny nemusí být členy skupiny.

Pokud skupina nemá žádného vlastníka, správci spravující skupinu mohou skupinu stále spravovat. Doporučuje se, aby každá skupina měla alespoň jednoho vlastníka. Jakmile jsou vlastníci zasazeni do skupiny, nelze posledního vlastníka skupiny odebrat. Před odebráním posledního vlastníka ze skupiny nezapomeňte vybrat jiného vlastníka.

## <a name="add-an-owner-to-a-group"></a>Přidání vlastníka do skupiny
Níže jsou uvedeny pokyny pro přidání uživatele jako vlastníka do skupiny pomocí portálu Azure AD. Chcete-li přidat instanční objekt jako vlastníka skupiny, postupujte podle pokynů, jak to udělat pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

### <a name="to-add-a-group-owner"></a>Přidání vlastníka skupiny
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, vyberte **Skupiny**a vyberte skupinu, pro kterou chcete přidat vlastníka (v tomto příkladu *zásady MDM – západ).*

3. Na stránce **Zásady MDM – Západní přehled** vyberte **položku Vlastníci**.

    ![Zásady MDM – stránka Západní přehled se zvýrazněnou možností Vlastníci](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na **stránce Zásady MDM – Západ – Vlastníci** vyberte **Přidat vlastníky**a pak vyhledejte a vyberte uživatele, který bude novým vlastníkem skupiny, a pak zvolte **Vybrat**.

    ![Zásady MDM – západ – stránka Vlastníci se zvýrazněnou možností Přidat vlastníky](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po výběru nového vlastníka můžete aktualizovat stránku **Vlastníci** a zobrazit název přidaný do seznamu vlastníků.

## <a name="remove-an-owner-from-a-group"></a>Odebrání vlastníka ze skupiny
Odeberte vlastníka ze skupiny pomocí Azure AD.

### <a name="to-remove-an-owner"></a>Odebrání vlastníka
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**, vyberte **Skupiny**a vyberte skupinu, pro kterou chcete odebrat vlastníka (v tomto příkladu *zásady MDM - Západ).*

3. Na stránce **Zásady MDM – Západní přehled** vyberte **položku Vlastníci**.

    ![Zásady MDM – stránka Západní přehled se zvýrazněnou možností Vlastníci](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na **stránce Zásady MDM – Západ – Vlastníci** vyberte uživatele, kterého chcete odebrat jako vlastníka skupiny, zvolte **Odebrat** na stránce s informacemi o uživateli a kliknutím na **ano** potvrďte své rozhodnutí.

    ![Stránka s informacemi o uživateli se zvýrazněnou možností Odebrat](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po odebrání vlastníka se můžete vrátit na stránku **Vlastníci** a zobrazit název byl odebrán ze seznamu vlastníků.

## <a name="next-steps"></a>Další kroky
- [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](active-directory-manage-groups.md)

- [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md)

- [Přiřazení přístupu k integrované aplikaci SaaS pomocí skupin](../users-groups-roles/groups-saasapps.md)

- [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-v2-cmdlets.md)
