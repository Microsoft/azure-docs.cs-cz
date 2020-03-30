---
title: Jak spravovat místní správce na zařízeních spojených s Azure AD
description: Přečtěte si, jak přiřadit role Azure místní skupině správců zařízení s Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc1812d955590ec0c7372e1311c9d69f93b9957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128884"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Jak spravovat skupinu místních správců na zařízeních spojených s Azure AD

Chcete-li spravovat zařízení se systémem Windows, musíte být členem místní skupiny administrators. Jako součást procesu připojení k azure active directory (Azure AD) Azure AD aktualizuje členství v této skupině na zařízení. Aktualizaci členství můžete přizpůsobit tak, aby splňovala vaše obchodní požadavky. Aktualizace členství je například užitečná, pokud chcete zaměstnancům technické podpory povolit provádění úloh vyžadujících práva správce v zařízení.

Tento článek vysvětluje, jak funguje aktualizace členství a jak ji můžete přizpůsobit během připojení Azure AD. Obsah tohoto článku se nevztahuje na **hybridní** připojení Azure AD.

## <a name="how-it-works"></a>Jak to funguje

Když připojíte zařízení s Windows s Azure AD pomocí spojení Azure AD, Azure AD přidá následující principy zabezpečení do místní skupiny správců na zařízení:

- Role globálního správce azure ad
- Role správce zařízení Azure AD 
- Uživatel provádějící připojení Azure AD   

Přidáním rolí Azure AD do místní skupiny správců můžete aktualizovat uživatele, kteří můžou spravovat zařízení kdykoli ve službě Azure AD, aniž by cokoli na zařízení měnili. V současné době nelze přiřadit skupiny k roli správce.
Azure AD také přidá roli správce zařízení Azure AD do skupiny místních správců pro podporu principu nejnižší oprávnění (PoLP). Kromě globálních správců můžete také povolit uživatelům, kterým byla přiřazena *pouze* role správce zařízení, spravovat zařízení. 

## <a name="manage-the-global-administrators-role"></a>Správa role globálních správců

Chcete-li zobrazit a aktualizovat členství v roli globálního správce, přečtěte si následující informace:

- [Zobrazení všech členů role správce ve službě Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Přiřazení uživatele k rolím správce v Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Správa role správce zařízení 

Na webu Azure Portal můžete spravovat roli správce zařízení na stránce **Zařízení.** Otevření stránky **Zařízení:**

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako globální správce.
1. Vyhledejte a vyberte *Azure Active Directory*.
1. V části **Spravovat** klikněte na **Položku Zařízení**.
1. Na stránce **Zařízení** klikněte na **Nastavení zařízení**.

Chcete-li upravit roli správce zařízení, nakonfigurujte **další místní správce na zařízeních spojených s Azure AD**.  

![Další místní správci](./media/assign-local-admin/10.png)

>[!NOTE]
> Tato možnost vyžaduje klienta Azure AD Premium. 

Správci zařízení jsou přiřazeni ke všem zařízením spojeným s Azure AD. Správce zařízení nelze určit na určitou sadu zařízení. Aktualizace role správce zařízení nemusí mít nutně okamžitý dopad na ovlivněné uživatele. Na zařízeních, kde je uživatel již přihlášen, dojde k aktualizaci oprávnění, když dojde k *oběma* níže uvedeným akcím:

- 4 hodiny uplynuly pro Azure AD vydat nový token primární aktualizace s příslušnými oprávněními. 
- Uživatel se odhlásí a přihlásí zpět, ne zamkne/ odemkne, aby si aktualizoval svůj profil.

## <a name="manage-regular-users"></a>Správa běžných uživatelů

Ve výchozím nastavení Azure AD přidá uživatele provádějící připojení Azure AD do skupiny správců na zařízení. Chcete-li zabránit tomu, aby se běžní uživatelé stali místními správci, máte následující možnosti:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot poskytuje možnost zabránit primární uživatel provádění spojení se stává místním správcem. Toho lze dosáhnout [vytvořením profilu autopilota](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Hromadná registrace](/intune/windows-bulk-enroll) – připojení Azure AD, které se provádí v kontextu hromadné registrace se stane v kontextu automaticky vytvořeného uživatele. Uživatelé, kteří se přihlašují po připojení zařízení, nebudou přidáni do skupiny administrators.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ruční zvýšení oprávnění uživatele na zařízení 

Kromě použití procesu připojení Azure AD můžete také ručně zvýšit úroveň běžného uživatele, aby se stal místním správcem na jednom konkrétním zařízení. Tento krok vyžaduje, abyste již byli členem místní skupiny administrators. 

Počínaje verzí **Windows 10 1709** můžete tento úkol provést z **nastavení -> účty -> Ostatní uživatelé**. Vyberte **Přidat pracovního nebo školního uživatele**, zadejte hlavní číslo uživatele v části **Uživatelský účet** a v části Typ účtu vyberte *Správce.* **Account type**  
 
Kromě toho můžete také přidat uživatele pomocí příkazového řádku:

- Pokud jsou uživatelé klienta synchronizováni z `net localgroup administrators /add "Contoso\username"`místní služby Active Directory, použijte .
- Pokud se uživatelé vašeho tenanta vytvářejí ve službě Azure AD, použijte`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Požadavky 

K roli správce zařízení nelze přiřadit skupiny, jsou povoleni pouze jednotliví uživatelé.

Správci zařízení jsou přiřazeni ke všem zařízením s připojením k Azure AD. Nelze je určit na určitou sadu zařízení.

Když odeberete uživatele z role správce zařízení, budou mít na zařízení stále oprávnění místního správce, dokud jsou k němu přihlášeni. Oprávnění je odvoláno během dalšího přihlášení nebo po 4 hodinách při vydání nového primárního obnovovacího tokenu.

## <a name="next-steps"></a>Další kroky

- Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).
- Další informace o podmíněném přístupu založeném na zařízení najdete v [tématu Konfigurace zásad podmíněného přístupu služby Azure Active Directory .](../conditional-access/require-managed-devices.md)
