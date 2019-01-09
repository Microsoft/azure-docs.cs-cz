---
title: Zařízení připojená k tom, jak spravovat místní skupiny administrators na Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak přiřadit role Azure do místní skupiny administrators na zařízení Windows.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2019
ms.author: markvi
ms.reviewer: ravenn
ms.openlocfilehash: a4671d383c46bf0b932ce528f4eacb7255bad5e7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105402"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Zařízení připojená k tom, jak spravovat místní skupiny administrators na Azure AD

Pokud chcete spravovat zařízení s Windows, musíte být členem místní skupiny administrators. Jako součást procesu připojení k Azure Active Directory (Azure AD) Azure AD aktualizuje členství této skupiny na zařízení. Aktualizace členství splnit požadavky vaší společnosti můžete přizpůsobit. Členství je aktualizace součástí, například užitečné, pokud chcete povolit pracovníky helpdesku úlohy byla nutná oprávnění správce v zařízení.

Tento článek vysvětluje, jak funguje aktualizace členství a jak ho můžete přizpůsobit během Azure AD Join. Obsah tohoto článku se nevztahuje **hybridní** připojení ke službě Azure AD.


## <a name="how-it-works"></a>Jak to funguje

Při připojení zařízení s Windows s Azure AD pomocí Azure AD join, Azure AD následující bezpečnostní Principy přidá do místní skupiny administrators na zařízení:

- Role Globální správce Azure AD
- Roli správce Azure AD 
- Uživatel provádějící připojení k Azure AD   

Přidáním role Azure AD do místní skupiny administrators, můžete aktualizovat uživatele, které můžete spravovat zařízení ve službě Azure AD můžete kdykoli beze změny v zařízení. V současné době nelze přiřadit skupiny k roli správce.
Azure AD také přidá roli správce Azure AD do místní skupiny administrators pro podporu principu nejnižších možných oprávnění (PoLP). Kromě globálního správce, můžete také povolit uživatelům, které byly *pouze* přiřazenou roli Správce zařízení pro správu zařízení. 


## <a name="manage-the-global-administrators-role"></a>Správa rolí globální správce

Chcete-li zobrazit a aktualizovat členství v roli globálního správce, přečtěte si téma:

- [Zobrazit všechny členy s rolí správce v Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)

- [Přiřazení uživatele k rolí správce ve službě Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Správa rolí Správce zařízení 

Na webu Azure Portal, můžete spravovat roli Správce zařízení na **zařízení** stránky. Chcete-li otevřít **zařízení** stránky:

1. Přihlaste se k vaší [webu Azure portal](https://portal.azure.com) jako globální správce nebo Správce zařízení.
2. Na levém navigačním panelu klikněte na tlačítko **Azure Active Directory**. 
3. V **spravovat** klikněte na tlačítko **zařízení**.
4. Na **zařízení** klikněte na **nastavení zařízení**.

Chcete-li upravit roli Správce zařízení, nakonfigurovat **zařízení připojená k další místní správci na Azure AD**.  

![Další místní správci](./media/assign-local-admin/10.png)

>[!NOTE]
> Tato možnost vyžaduje tenanta služby Azure AD Premium. 


Správci zařízení přiřazených k zařízení připojených k všechny Azure AD. Nelze určit obor Správci zařízení na konkrétní sadu zařízení. Aktualizuje se role Správce zařízení nemá nutně bezprostřední dopad na ovlivněných uživatelů. Pro zařízení uživatel je již přihlášení, Probíhá aktualizace oprávnění:
     

- Když se uživatel přihlásí.
- Po 4 hodinách když je nový primární aktualizovat Token vystaven. 




## <a name="manage-regular-users"></a>Správa běžní uživatelé

Azure AD ve výchozím nastavení, přidá uživatel provádějící připojení k Azure AD do skupiny správců na zařízení. Pokud chcete zabránit běžní uživatelé stávají místními správci, máte následující možnosti:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows Autopilot vám poskytuje možnost zakázat primární uživatel provádějící spojení, nestala oprávnění místního správce. Toho lze dosáhnout pomocí [vytváří se profil Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
 
- [Hromadná registrace](https://docs.microsoft.com/intune/windows-bulk-enroll) – Azure AD join, který se provádí v rámci hromadné registrace probíhá v kontextu uživatele automaticky vytvořený. Uživatelé přihlášení po připojil zařízení nejsou přidány do skupiny administrators.   



## <a name="manually-elevate-a-user-on-a-device"></a>Ručně zvýšit oprávnění uživatele v zařízení 

Kromě použití proces připojení k Azure AD, můžete také ručně zvýšit běžného uživatele jako místní správce na jednu konkrétní zařízení. Tento krok vyžaduje, abyste už být členem místní skupiny administrators. 

Počínaje **Windows 10 1709** vydání, můžete tuto úlohu lze provést z **Nastavení -> účty -> ostatní uživatelé**. Vyberte **přidat pracovní nebo školní uživatele**, zadejte uživatele (UPN) v části **uživatelský účet** a vyberte *správce* pod **typ účtu**  
 
Kromě toho můžete také přidat uživatele pomocí příkazového řádku:

- Pokud váš tenant uživatelé používají synchronizaci z místní služby Active Directory, použijte `net localgroup administrators /add "Contoso\username"`.

- Pokud vaši uživatelé tenanta se vytvoří v Azure AD, použijte `net localgroup administrators /add "AzureAD\UserUpn"`


## <a name="considerations"></a>Požadavky 

Nelze přiřadit skupiny k roli Správce zařízení, jsou povoleny pouze jednotlivé uživatele.

Správci zařízení jsou přiřazené do všech zařízení připojeno k Azure AD. Nemůže být obor na konkrétní sadu zařízení.

Při odebrání uživatele z role Správce zařízení stále mají oprávnění místního správce v zařízení tak dlouho, dokud jsou přihlášení k němu. Oprávnění se zrušil během na další přihlašování, nebo po 4 hodinách, po vydání nové primární obnovovací token.



## <a name="next-steps"></a>Další postup

- Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).

- Další informace o podmíněném přístupu na základě zařízení najdete v tématu věnovaném [konfiguraci zásad podmíněného přístupu na základě zařízení v Azure Active Directory](../conditional-access/require-managed-devices.md).


