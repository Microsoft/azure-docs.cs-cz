---
title: 'Kurz: Konfigurace NetSuite OneWorld pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetSuite OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648080"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Kurz: Konfigurace NetSuite pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte provést v NetSuite OneWorld a Azure AD a automaticky zřizovat a zrušit zřizování uživatelských účtů z Azure AD až po NetSuite.

> [!WARNING]
> Tato zřizování integrace přestanou v důsledku změny NetSuite rozhraní API, která společnost Microsoft používá ke zřizování uživatelů v NetSuite, v rámci vydání NetSuite aktualizace na jaře 2021.  Tato aktualizace bude kontaktovat NetSuite zákazníky mezi únorem a duben 2021. V důsledku toho se brzy odeberou funkce zřizování aplikace NetSuite v galerii aplikací Azure Active Directory Enterprise. Funkce jednotného přihlašování do aplikace zůstane beze změny. Microsoft spolupracuje s NetSuite, aby vytvořil novou moderní integraci zřizování, ale v současné době se v tuto chvíli nedokončí.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

*   Tenant Azure Active Directory.
*   Předplatné NetSuite OneWorld. Všimněte si, že Automatické zřizování uživatelů je v současné době podporováno pouze pomocí NetSuite OneWorld.
*   Uživatelský účet v NetSuite s oprávněními správce.
*   Integrace se službou Azure AD vyžaduje výjimku 2FA. Pokud si chcete vyžádat tuto výjimku, obraťte se na tým podpory pro NetSuite.

## <a name="assigning-users-to-netsuite-oneworld"></a>Přiřazení uživatelů k NetSuite OneWorld

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci NetSuite. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace NetSuite podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Důležité tipy pro přiřazení uživatelů k NetSuite OneWorld

*   Doporučuje se, abyste k testování konfigurace zřizování přiřadili jednoho uživatele Azure AD NetSuite. Další uživatele a skupiny můžete přiřadit později.

*   Při přiřazování uživatele k NetSuite je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v NetSuite na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP] 
> Můžete se také rozhodnout povolit pro NetSuite jednu Sign-On založenou na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je vysvětlit, jak povolit uživatelům zřizování uživatelských účtů služby Active Directory pro NetSuite.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

1. Pokud jste už nakonfigurovali NetSuite pro jednotné přihlašování, vyhledejte vaši instanci NetSuite pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **NetSuite** v galerii aplikací. Ve výsledcích hledání vyberte NetSuite a přidejte je do seznamu aplikací.

1. Vyberte svou instanci NetSuite a pak vyberte kartu **zřizování** .

1. Nastavte **Režim zřizování** na hodnotu **Automaticky**. 

    ![Snímek obrazovky se zobrazí stránka zřizování NetSuite s režimem zřizování nastaveným na automatické a jiné hodnoty, které můžete nastavit.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. V části **přihlašovací údaje správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **správce uživatelské jméno** zadejte název účtu NetSuite, který má přiřazený profil **správce systému** v NetSuite.com.
   
    b. Do textového pole **heslo správce** zadejte heslo pro tento účet.
      
1. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci NetSuite.

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé NetSuite.**

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do NetSuite. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v NetSuite pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

1. Pokud chcete povolit službu Azure AD Provisioning pro NetSuite, změňte **stav zřizování** na **zapnuto** v části nastavení.

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených NetSuite v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci NetSuite.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](netsuite-tutorial.md)
