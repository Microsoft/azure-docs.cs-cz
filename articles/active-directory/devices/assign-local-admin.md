---
title: Jak spravovat místní správce na zařízeních připojených k Azure AD
description: Přečtěte si, jak přiřadit role Azure do místní skupiny Správci zařízení s Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d482f21955b76e6b90523afe3b4933378c91d36e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107357"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Jak spravovat místní skupinu Administrators na zařízeních připojených k Azure AD

Abyste mohli spravovat zařízení s Windows, musíte být členem místní skupiny Administrators. V rámci procesu připojení Azure Active Directory (Azure AD) aktualizuje Azure AD členství této skupiny na zařízení. Aktualizaci členství můžete přizpůsobit tak, aby vyhovovala vašim obchodním požadavkům. Aktualizace členství je například užitečná, pokud chcete pracovníkům technické podpory povolit úkoly vyžadující práva správce na zařízení.

Tento článek vysvětluje, jak funguje členství v místních správcích a jak je můžete přizpůsobit během připojení k Azure AD. Obsah tohoto článku se nevztahuje na zařízení **připojená k hybridní službě Azure AD** .

## <a name="how-it-works"></a>Jak to funguje

Když připojíte zařízení s Windows ke službě Azure AD pomocí služby Azure AD JOIN, Azure AD přidá do místní skupiny Administrators na zařízení tyto objekty zabezpečení:

- Role globálního správce služby Azure AD
- Role Správce zařízení Azure AD 
- Uživatel, který provádí službu Azure AD JOIN   

Když do místní skupiny Administrators přidáte role Azure AD, můžete aktualizovat uživatele, kteří můžou spravovat zařízení kdykoli ve službě Azure AD, aniž by to mělo na zařízení žádné změny. Azure AD taky přidá roli Správce zařízení Azure AD do místní skupiny Administrators, aby podporovala princip nejnižších oprávnění (PoLP). Kromě globálních správců můžete také povolit uživatelům, kterým byla přiřazena *pouze* role Správce zařízení ke správě zařízení. 

## <a name="manage-the-global-administrators-role"></a>Správa role globální správci

Chcete-li zobrazit a aktualizovat členství v roli globálního správce, přečtěte si téma:

- [Zobrazit všechny členy role správce v Azure Active Directory](../roles/manage-roles-portal.md)
- [Přiřazení uživatele k rolím správce v Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Správa role Správce zařízení 

V Azure Portal můžete spravovat roli Správce zařízení na stránce **zařízení** . Otevření stránky **zařízení** :

1. Přihlaste se k vašemu [Azure Portal](https://portal.azure.com) jako globální správce.
1. Vyhledejte a vyberte *Azure Active Directory*.
1. V části **Spravovat** klikněte na **zařízení**.
1. Na stránce **zařízení** klikněte na **nastavení zařízení**.

Pokud chcete upravit roli Správce zařízení, nakonfigurujte **Další místní správce na zařízeních připojených k Azure AD**.  

![Další místní správci](./media/assign-local-admin/10.png)

>[!NOTE]
> Tato možnost vyžaduje klienta Azure AD Premium. 

Správci zařízení mají přiřazená všechna zařízení připojená k Azure AD. Nemůžete nastavit rozsah správců zařízení na určitou sadu zařízení. Aktualizace role Správce zařízení nutně nemá bezprostřední dopad na ovlivněné uživatele. Na zařízeních, ve kterých je uživatel už přihlášený, se zvýšení oprávnění provede, *když dojde k následujícím* akcím:

- Až 4 hodiny uplynulo pro Azure AD za účelem vydání nového primárního obnovovacího tokenu s příslušnými oprávněními. 
- Pokud chcete aktualizovat svůj profil, odhlaste se a přihlaste se k němu, ne zamknout nebo odemknout.

>[!NOTE]
> Výše uvedené akce se nevztahují na uživatele, kteří předtím nebyli přihlášení k příslušnému zařízení. V takovém případě se oprávnění správce uplatní hned po prvním přihlášení k zařízení. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Správa oprávnění správce pomocí skupin Azure AD (Preview)

>[!NOTE]
> Tato funkce je aktuálně ve verzi Preview.


Počínaje aktualizací Windows 10 2004 můžete pomocí skupin Azure AD spravovat oprávnění správce na zařízeních připojených k Azure AD pomocí zásad [skupiny s omezeným přístupem](/windows/client-management/mdm/policy-csp-restrictedgroups) (MDM). Tato zásada vám umožní přiřadit jednotlivé uživatele nebo skupiny Azure AD k místní skupině Administrators na zařízení připojeném k Azure AD a poskytnout tak členitost pro konfiguraci samostatných správců pro různé skupiny zařízení. 

>[!NOTE]
> Spouští se aktualizace Windows 10 20H2, doporučujeme místo zásad skupin s omezenými oprávněními používat zásady [místních uživatelů a skupin](/windows/client-management/mdm/policy-csp-localusersandgroups) .


V současné době není v Intune žádné uživatelské rozhraní pro správu těchto zásad a je potřeba je nakonfigurovat pomocí [vlastního nastavení OMA-URI](/mem/intune/configuration/custom-settings-windows-10). Několik důležitých informací pro použití některé z těchto zásad: 

- Přidání skupin Azure AD prostřednictvím zásad vyžaduje, aby bylo možné získat identifikátor SID skupiny spuštěním [rozhraní Microsoft Graph API pro skupiny](/graph/api/resources/group?view=graph-rest-beta). Identifikátor SID je definován vlastností `securityIdentifier` v odpovědi rozhraní API.
- Když se vynutila zásada skupin s omezeným přístupem, všechny aktuální členy skupiny, které nejsou v seznamu členů, se odeberou. Aby tyto zásady vynutily nové členy nebo skupiny, odstraní stávající správce konkrétně uživatele, který se připojil k zařízení, roli Správce zařízení a roli globálního správce ze zařízení. Chcete-li se vyhnout odebrání stávajících členů, je třeba je nakonfigurovat jako součást seznamu členů v zásadě skupiny s omezeným přístupem. Toto omezení se řeší, pokud používáte zásady místních uživatelů a skupin, které umožňují přírůstkové aktualizace členství ve skupinách.
- Oprávnění správce pomocí obou zásad se vyhodnocují jenom pro následující známé skupiny na zařízeních s Windows 10 – správci, uživatelé, hosty, Power Users, Uživatelé vzdálené plochy a uživatelé vzdálené správy. 
- Správa místních správců pomocí skupin Azure AD není platná pro připojení k hybridní službě Azure AD nebo zařízením registrovaným v Azure AD.
- Zásady skupin s omezeným přístupem existovaly před aktualizací Windows 10 2004, ale nepodporují skupiny Azure AD jako členy místní skupiny správců zařízení. 

## <a name="manage-regular-users"></a>Správa běžných uživatelů

Ve výchozím nastavení Azure AD přidá uživatele, který provádí službu Azure AD JOIN, do skupiny správců v zařízení. Pokud chcete běžným uživatelům zabránit v tom, aby se stali místními správci, máte následující možnosti:

- Automatický [pilot pro Windows](/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows autopilot vám nabízí možnost zabránit primárnímu uživateli, aby se mohl připojit od místního správce. To můžete provést [vytvořením profilu autopilotu](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Hromadná registrace](/intune/windows-bulk-enroll) – připojení k Azure AD, které se provádí v kontextu hromadné registrace, probíhá v kontextu automaticky vytvořeného uživatele. Uživatelé, kteří se přihlašují po přihlášení k zařízení, se do skupiny Administrators nepřipojí.   

## <a name="manually-elevate-a-user-on-a-device"></a>Ruční zvýšení oprávnění uživatele na zařízení 

Kromě použití procesu připojení ke službě Azure AD můžete také ručně zvýšit normálního uživatele tak, aby se stal místním správcem na jednom konkrétním zařízení. Tento krok vyžaduje, abyste již byli členem místní skupiny Administrators. 

Od verze **Windows 10 1709** můžete tuto úlohu provést z **Nastavení-> účty – > dalších uživatelů**. Vyberte **Přidat pracovního nebo školního uživatele**, do pole **uživatelský účet** zadejte hlavní název uživatele (UPN) a v části **typ účtu** vyberte *správce* .  
 
Kromě toho můžete přidat uživatele také pomocí příkazového řádku:

- Pokud jsou vaši uživatelé klienta synchronizováni z místní služby Active Directory, použijte `net localgroup administrators /add "Contoso\username"` .
- Pokud jsou vaši uživatelé tenanta vytvořeni ve službě Azure AD, použijte `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Požadavky 

Nelze přiřadit skupiny k roli Správce zařízení, pouze jednotliví uživatelé jsou povoleni.

Správci zařízení mají přiřazená všechna zařízení připojená k Azure AD. Nemůžou být vymezené na konkrétní sadu zařízení.

Když odeberete uživatele z role Správce zařízení, pořád mají oprávnění místního správce na zařízení, pokud se k němu přihlásí. Oprávnění se odvolá při příštím přihlášení při vydání nového primárního obnovovacího tokenu. Toto odvolání, podobně jako zvýšení oprávnění, může trvat až 4 hodiny.

## <a name="next-steps"></a>Další kroky

- Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).
- Další informace o podmíněném přístupu na základě zařízení najdete v tématu [Konfigurace zásad podmíněného přístupu na základě zařízení Azure Active Directory](../conditional-access/require-managed-devices.md).
