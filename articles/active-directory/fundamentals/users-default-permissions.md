---
title: Co jsou výchozí oprávnění uživatelů ve službě Azure Active Directory? | Dokumenty Microsoft
description: Další informace o různých uživatelských oprávnění k dispozici v Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: lizross
ms.reviewer: vincesm
custom: it-pro
ms.openlocfilehash: 9a9bd33dce4b2be9e15df2745dd25338cdb42702
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45730475"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Co jsou výchozí oprávnění uživatelů ve službě Azure Active Directory?

V Azure Active Directory (Azure AD) mají všichni uživatelé udělenou sadu výchozích oprávnění. Přístup uživatele se skládá z typu uživatele, jeho [členství v rolích](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal) a vlastnictví jednotlivých objektů. Tento článek popisuje tato výchozí oprávnění a obsahuje porovnání výchozích nastavení člena a uživatele typu host.

## <a name="member-and-guest-users"></a>Členové a uživatelé typu host
Sada udělených výchozích oprávnění závisí na tom, jestli je uživatel nativním členem tenanta (člen) nebo jestli je hostem pro spolupráci B2B (uživatel typu host). Další informace o spolupráci B2B a uživatelích typu host najdete v tématu [Co je spolupráce Azure AD s B2B?](../b2b/what-is-b2b.md). 
* Členové můžou registrovat aplikace, spravovat vlastní profilovou fotku a číslo mobilního telefonu, změnit vlastní heslo a zvát hosty B2B. Kromě toho můžou uživatelé číst všechny informace v adresáři (s několika výjimkami). 
* Uživatelé Azure AD B2B typu host mají omezená oprávnění k adresáři. Uživatelé typu host například nemůžou procházet informace z tenanta nad rámec informací o vlastním profilu. Uživatel typu host však může načíst informace o jiném uživateli tím, že zadá hlavní název uživatele (UPN) nebo identifikátor objectID. Host nemůže zobrazit žádné informace o ostatních objektech v tenantovi, jako jsou skupiny a aplikace.

Výchozí oprávnění pro hosty jsou ve výchozím nastavení omezující. Hosty je možné přidat do rolí správce, které jim udělí úplná oprávnění ke čtení a zápisu obsažená v dané roli. K dispozici je ještě jedno omezení – možnost hostů zvát jiné hosty. Nastavením možnosti **Hosté můžou posílat pozvánky** na hodnotu **Ne** zabráníte hostům zvát jiné hosty. Informace o postupu najdete v tématu [Delegování pozvánek pro spolupráci B2B](../b2b/delegate-invitations.md). Pokud chcete uživatelům typu host udělit stejná oprávnění, jako mají členové ve výchozím nastavení, nastavte možnost **Oprávnění uživatelů typu host jsou omezená** na hodnotu **Ne**. Toto nastavení standardně udělí uživatelům typu host všechna uživatelská oprávnění členů a také povolí přidání hostů do rolí pro správu.

## <a name="compare-member-and-guest-default-permissions"></a>Porovnání výchozích oprávnění pro členy a hosty

**Oblast** | **Uživatelská oprávnění člena** | **Uživatelská oprávnění hosta**
------------ | --------- | ----------
Uživatelé a kontakty | Čtení všech veřejných vlastností uživatelů a kontaktů<br>Zvaní hostů<br>Změna vlastního hesla<br>Správa vlastního čísla mobilního telefonu<br>Správa vlastní fotky<br>Zneplatnění vlastních obnovovacích tokenů | Čtení vlastních vlastností<br>Čtení vlastností zobrazovaného jména, e-mailu, přihlašovacího jména, fotky, hlavního názvu uživatele (UPN) a typu uživatele ostatních uživatelů a kontaktů<br>Změna vlastního hesla
Skupiny | Vytváření skupin zabezpečení<br>Vytváření skupin Office 365<br>Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojené skupiny<br>Správa vlastností, vlastnictví a členství vlastněných skupin<br>Přidávání hostů do vlastněných skupin<br>Správa nastavení dynamického členství<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365 | Čtení všech vlastností skupin<br>Čtení neskrytých členství ve skupinách<br>Čtení skrytých členství ve skupinách Office 365 u připojených skupin<br>Správa vlastněných skupin<br>Přidávání hostů do vlastněných skupin (pokud je to povoleno)<br>Odstranění vlastněných skupin<br>Obnovení vlastněných skupin Office 365 
Aplikace | Registrace (vytvoření) nové aplikace<br>Čtení vlastností zaregistrovaných a podnikových aplikací<br>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<br>Vytvoření nebo odstranění hesla aplikace pro uživatele<br>Odstranění vlastněných aplikací<br>Obnovení vlastněných aplikací | Čtení vlastností zaregistrovaných a podnikových aplikací<br>Správa vlastností aplikací, jejich přiřazení a přihlašovacích údajů u vlastněných aplikací<br>Odstranění vlastněných aplikací<br>Obnovení vlastněných aplikací
Zařízení | Čtení všech vlastností zařízení<br>Správa všech vlastností vlastněných zařízení<br> | Žádná oprávnění<br>Odstranění vlastněných zařízení<br>
Adresář | Čtení všech informací o společnosti<br>Čtení všech domén<br>Čtení všech partnerských kontraktů | Čtení zobrazovaného názvu a ověřených domén
Role a obory | Čtení všech rolí pro správu a členství v nich<br>Čtení všech vlastností a členství jednotek pro správu | Žádná oprávnění 
Předplatná | Čtení všech předplatných<br>Povolení člena plánu služby | Žádná oprávnění
Zásady | Čtení všech vlastností zásad<br>Správa všech vlastností vlastněných zásad | Žádná oprávnění

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Omezení výchozích oprávnění pro členy

Výchozí oprávnění pro členy je možné omezit následujícími způsoby.

Oprávnění | Vysvětlení nastavení
---------- | ------------
Možnost vytvářet skupiny zabezpečení | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny zabezpečení. Globální správci a správci uživatelských účtů budou moct skupiny zabezpečení vytvářet i nadále. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Možnost vytvářet skupiny Office 365 | Nastavení této možnosti na hodnotu Ne zabrání uživatelům vytvářet skupiny Office 365. Nastavení této možnosti na hodnotu Někteří umožní vytvářet skupiny Office 365 vybrané skupině uživatelů. Globální správci a správci uživatelských účtů budou moct skupiny Office 365 vytvářet i nadále. Informace o postupu najdete v tématu [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../users-groups-roles/groups-settings-cmdlets.md).
Omezení přístupu k portálu pro správu Azure AD | Nastavení této možnosti na Ne zabraňuje uživatelům v přístupu k Azure Active Directory.
Možnost číst ostatní uživatele | Toto nastavení je k dispozici pouze v PowerShellu. Nastavení této možnosti na hodnotu $false zabrání všem uživatelům, kteří nejsou správci, číst z adresáře informace o uživatelích. Toto nastavení nebrání čtení informací o uživatelích v ostatních službách Microsoftu, jako je například Exchange Online. Toto nastavení je určené pro zvláštní okolnosti a jeho nastavení na hodnotu $false se nedoporučuje.

## <a name="object-ownership"></a>Vlastnictví objektů

### <a name="application-registration-owner-permissions"></a>Oprávnění vlastníka registrace aplikace
Když uživatel zaregistruje aplikaci, automaticky se přidá jako vlastník této aplikace. Jako vlastník může spravovat metadata aplikace, například její název a oprávnění, která aplikace vyžaduje. Může spravovat také konfiguraci aplikace specifickou pro tenanta, jako je konfigurace jednotného přihlašování a přiřazení uživatelů. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců můžou vlastníci spravovat pouze aplikace, které vlastní.

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Oprávnění vlastníka skupiny

Když uživatel vytvoří skupinu, automaticky se přidá jako vlastník této skupiny. Jako vlastník může spravovat vlastnosti skupiny, například její název, a také spravovat členství ve skupině. Vlastník může také přidat nebo odebrat další vlastníky. Na rozdíl od globálních správců a správců uživatelských účtů můžou vlastníci spravovat pouze skupiny, které vlastní. Pokud chcete přiřadit vlastníka skupiny, přečtěte si téma [Správa vlastníků skupiny](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak přiřadit role správce Azure AD najdete v tématu [uživatele přiřadit do rolí správce ve službě Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md).
* [Správa uživatelů](add-users-azure-active-directory.md)
