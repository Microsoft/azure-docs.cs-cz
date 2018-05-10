---
title: Porovnání výchozí oprávnění uživatele v Azure Active Directory | Microsoft Docs
description: Porovnání člen, guest, aplikace a oprávnění vlastníka skupiny
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/29/2018
ms.author: curtand
ms.reviewer: vincesm
ms.openlocfilehash: a9bf9748de5f390f95b8b672e0cf77afa5c49581
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="default-user-permissions-in-azure-active-directory"></a>Výchozí oprávnění uživatele v Azure Active Directory

V Azure Active Directory (Azure AD) všichni uživatelé mají sadu výchozích oprávnění. Přístup uživatele se skládá typ uživatele, jejich [členství v rolích](https://docs.microsoft.com/azure/active-directory/active-directory-users-assign-role-azure-portal)a jejich vlastnictví jednotlivých objektů. Tento článek popisuje tyto výchozí oprávnění a obsahuje porovnání člen a hosta výchozí nastavení uživatele.

## <a name="member-and-guest-users"></a>Člen a hosta uživatelů
Sada výchozích oprávnění přijatých závisí na Pokud je uživatel členem nativního klienta (člen uživatele), nebo pokud je uživatel Host spolupráce B2B (hosta uživatele). Další informace o spolupráci B2B najdete v tématu [co je spolupráce Azure AD B2B?](active-directory-b2b-what-is-azure-ad-b2b.md) Další informace o uživatele typu Host). 
* Člen uživatelé zaregistrovat aplikace, spravovat jejich vlastní profil fotografie a mobilní telefonní číslo, změnit své vlastní heslo a pozvat hosté B2B. Kromě toho uživatelé mohou číst všech informací adresáře (s několika výjimkami). 
* Azure AD s B2B uživatele typu Host omezit oprávnění k adresářům. Například uživatele typu Host nemůže procházet informace z klienta nad rámec své vlastní informace o profilu. Uživatel typu Host však můžete načíst informace o jiného uživatele tím, že poskytuje hlavní název uživatele nebo objectId. Host nelze zobrazit žádné informace o další objekty klienta, například skupinám a aplikacím.

Výchozí oprávnění pro hosty jsou omezující ve výchozím nastavení. Hosté mohou být přidány do role správce, které jim udělit úplná oprávnění číst a zapisovat obsažené v roli. Neexistuje jeden další omezení k dispozici možnost pro hosty pozvaným jiných hosty. Nastavení **můžete pozvat hosté** k **ne** brání pozvání jiných hosté hosty. V tématu [delegovat pozvánky pro spolupráci B2B](active-directory-b2b-delegate-invitations.md) další jak. Ve výchozím nastavení udělit uživatele typu Host stejná oprávnění jako člen uživatele, nastavte **hosta uživatelé oprávnění jsou omezené** k **ne**. Toto nastavení uděluje oprávnění uživatele pro uživatele typu Host všechny člena ve výchozím nastavení, a že je možné Hosté mají být přidány do role správců.

## <a name="compare-member-and-guest-default-permissions"></a>Porovnání člen a hosta výchozích oprávnění

**Oblast** | **Člen uživatelských oprávnění** | **Oprávnění uživatele Guest**
------------ | --------- | ----------
Uživatelů a kontaktů | Číst všechny veřejné vlastnosti uživatelů a kontaktů<br>Pozvěte hosty<br>Změnit svoje heslo<br>Spravovat vlastní mobilní telefonní číslo<br>Spravovat vlastní fotografie<br>Zrušení platnosti vlastní obnovovacích tokenů | Vlastní vlastnosti pro čtení<br>Zobrazovaný název pro čtení, e-mailu, přihlašovací jméno, fotografií, hlavní název uživatele a vlastnosti typu uživatele jiných uživatelů a kontaktů<br>Změnit svoje heslo
Skupiny   | Vytvoření skupin zabezpečení<br>Vytvoření skupiny Office 365<br>Číst vlastnosti všech skupin<br>Přečtěte si neskrytý členství ve skupinách<br>Čtení skryté členství ve skupině Office 365 pro připojené k skupinu<br>Spravovat vlastnosti, vlastnictví a členství ve vlastnictví skupin<br>Přidat hosty do vlastní skupiny<br>Spravovat nastavení dynamické členství<br>Odstranit vlastní skupiny<br>Obnovení ve vlastnictví skupiny Office 365 | Číst vlastnosti všech skupin<br>Přečtěte si neskrytý členství ve skupinách<br>Skrytý členství ve skupině Office 365 pro připojených skupin pro čtení<br>Spravovat vlastní skupiny<br>Přidat hosty do vlastní skupiny (Pokud je povolena.)<br>Odstranit vlastní skupiny<br>Obnovení ve vlastnictví skupiny Office 365           
Aplikace | Registrace (vytvořit) novou aplikaci<br>Čtení vlastnosti zaregistrované a podnikových aplikací<br>Spravovat vlastnosti aplikace, přiřazení a přihlašovací údaje pro vlastní aplikace<br>Vytvořit nebo odstranit heslo aplikace pro uživatele<br>Odstranit vlastní aplikace<br>Obnovit vlastní aplikace | Čtení vlastnosti zaregistrované a podnikových aplikací<br>Spravovat vlastnosti aplikace, přiřazení a přihlašovací údaje pro vlastní aplikace<br>Odstranit vlastní aplikace<br>Obnovit vlastní aplikace
Zařízení | Číst vlastnosti všech zařízení<br>Spravovat všechny vlastnosti zařízení<br> | Žádná oprávnění<br>Zařízení vlastněná odstranění<br>
Adresář | Číst všechny informace o společnosti<br>Číst všechny domény<br>Číst všechny partnerem kontrakty | Přečtěte si zobrazovaný název a ověření domény
Role a obory | Číst všechny role pro správu a členství ve skupinách<br>Číst všechny vlastnosti a členství administrativních jednotek | Žádná oprávnění              
Předplatná | Číst všechny odběry<br>Zapnout člen plánu služby | Žádná oprávnění
Zásady | Číst vlastnosti všech zásad<br>Spravovat všechny vlastnosti vlastní zásady | Žádná oprávnění

## <a name="to-restrict-the-default-permissions-for-member-users"></a>Chcete-li omezit výchozí oprávnění pro uživatele člena


Výchozí oprávnění pro člen, který uživatelé se dá omezit následujícím způsobem. Další informace najdete v tématu [aplikace, oprávnění a souhlasu v Azure Active Directory](active-directory-apps-permissions-consent.md).

Oprávnění | Vysvětlení nastavení
---------- | ------------
Schopnost vytvářet skupiny zabezpečení | Nastavení této možnosti na Ne zabrání uživatelům ve vytváření skupin zabezpečení. Globální správci a správci účtu uživatele i přesto můžete vytvářet skupiny zabezpečení. V tématu [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](active-directory-accessmanagement-groups-settings-cmdlets.md) další jak.
Schopnost vytvářet skupiny Office 365 | Nastavení této možnosti na Ne zabraňuje uživatelům ve vytváření skupiny Office 365. Nastavení této možnosti na některé umožňuje vyberte sadu uživatelům vytvářet skupiny Office 365. Globální správci a správci účtu uživatele bude možné vytvářet skupiny Office 365. V tématu [rutiny služby Azure Active Directory pro konfiguraci nastavení skupiny](active-directory-accessmanagement-groups-settings-cmdlets.md) další jak.
Schopnost souhlasit s aplikací | Nastavení této možnosti na Ne, zabraňuje uživatelům souhlas pro aplikace. Globální správci bude stále moci svůj souhlas s aplikací. Další informace najdete v tématu [aplikace, oprávnění a souhlasu v Azure Active Directory](active-directory-apps-permissions-consent.md).
Možnost přidat do přístupového panelu Galerie aplikace | Nastavení této možnosti na Ne zabrání uživatelům v přidávání Galerie aplikace na jejich Panel přístupu.
Možnost registrovat (vytvořit) aplikací | Nastavení této možnosti na Ne zabrání vytváření aplikací bez oprávnění správce. Globální správci přesto bude možné vytvářet aplikace. Další informace najdete v tématu [aplikace, oprávnění a souhlasu v Azure Active Directory](active-directory-apps-permissions-consent.md).
Správci a uživatelé v roli hosta pozvánky můžete pozvat hosty | Nastavení této možnosti na Ne brání všem uživatelům pozváním hostů. V tématu Konfigurace výchozí oprávnění pro uživatele člena. Další informace najdete v tématu [aplikace, oprávnění a souhlasu v Azure Active Directory](active-directory-apps-permissions-consent.md).
Členy můžete pozvat hosty | Toto nastavení na žádné zabrání uživatelům pozváním hostů. Globální správci, správci uživatelského účtu a Inviters hosta budou mít pořád povolený pozvaným hostů. Další informace najdete v tématu [aplikace, oprávnění a souhlasu v Azure Active Directory](active-directory-apps-permissions-consent.md).
Omezit přístup k portálu pro správu Azure AD | Nastavení této možnosti na Ne, zabraňuje uživatelům přístup k portálu Azure Active Directory.
Možnost číst jiní uživatelé | Toto nastavení je pouze dostupné v prostředí PowerShell. Toto nastavení na $false zabrání čtení z adresáře uživatele získáte všechny bez oprávnění správce. To nezabrání čtení uživatelů získáte v jiných služeb společnosti Microsoft, jako je Exchange Online. Toto nastavení je určená pro zvláštní okolnosti a nastavení, které na $false se nedoporučuje.

## <a name="object-ownership"></a>Vlastnictví objektů

### <a name="application-registration-owner-permissions"></a>Oprávnění vlastníka registrace aplikací
Když se uživatel zaregistruje aplikace, se automaticky přidají jako vlastníka pro aplikaci. Jako vlastníka můžou spravovat metadata aplikace, jako je jméno a oprávnění aplikace požadavky. Mohou také spravovat konfiguraci konkrétního klienta aplikace, jako je například přiřazení konfigurace a uživatel jednotné přihlašování. Vlastníka můžete také přidat nebo odebrat ostatní vlastníky. Na rozdíl od globální správci jenom vlastníci můžou spravovat aplikace, které vlastní. Chcete-li přiřadit vlastníka registrace aplikace, přečtěte si téma [registrace aplikace Azure Active Directory](active-directory-app-registration.md).

<!-- ### Enterprise application owner permissions

When a user adds a new enterprise application, they are automatically added as an owner for the tenant-specific configuration of the application. As an owner, they can manage the tenant-specific configuration of the application, such as the SSO configuration, provisioning, and user assignments. An owner can also add or remove other owners. Unlike Global Administrators, owners can manage only the applications they own. <!--To assign an enterprise application owner, see *Assigning Owners for an Application*.-->

### <a name="group-owner-permissions"></a>Oprávnění vlastníka skupiny

Když uživatel vytvoří skupinu, jsou automaticky přidány jako vlastníka pro tuto skupinu. Jako vlastníka se může spravovat vlastnosti skupiny, například název, stejně jako spravovat členství. Vlastníka můžete také přidat nebo odebrat ostatní vlastníky. Na rozdíl od globální správci a správci uživatelského účtu jenom vlastníci můžou spravovat skupiny, které vlastní. Přiřadit vlastník skupiny, najdete v části [Správa vlastníků pro skupinu](active-directory-accessmanagement-managing-group-owners.md).

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak změnit správce pro předplatné služby Azure naleznete v tématu [Postup přidání nebo změna role správce služby Azure](../billing-add-change-azure-subscription-administrator.md)
* Další informace o tom, jak se přístup k prostředkům řídí ve službě Microsoft Azure, najdete v části [Principy přístupu k prostředkům ve službě Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o tom, jak Azure Active Directory má vztah k předplatnému Azure, najdete v části [asociování předplatných Azure se službou Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Správa uživatelů](active-directory-create-users.md)
