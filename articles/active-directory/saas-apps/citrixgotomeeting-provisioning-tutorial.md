---
title: 'Kurz: Konfigurace GoToMeeting pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GoToMeeting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 6cb2c90658a69f3e63e9ebe08db41eea0a9bc7fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299693"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Kurz: Konfigurace GoToMeeting pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte v GoToMeeting a Azure AD použít k automatickému zřízení a zrušení zřízení uživatelských účtů z Azure AD až GoToMeeting.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

*   Tenant Azure Active Directory.
*   Předplatné s povoleným GoToMeetingm jednotným přihlašováním.
*   Uživatelský účet v GoToMeeting s oprávněními správce týmu.

## <a name="assigning-users-to-gotomeeting"></a>Přiřazování uživatelů k GoToMeeting

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte se rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup k aplikaci GoToMeeting. Po rozhodnutí můžete tyto uživatele přiřadit do aplikace GoToMeeting podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Důležité tipy pro přiřazení uživatelů k GoToMeeting

*   Doporučuje se, abyste k testování konfigurace zřizování přiřadili jednoho uživatele Azure AD GoToMeeting. Další uživatele a skupiny můžete přiřadit později.

*   Při přiřazování uživatele k GoToMeeting je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit automatizované zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů ve službě Azure AD a konfigurací služby zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v GoToMeeting na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit pro GoToMeeting jednu Sign-On založenou na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů:

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

1. Pokud jste už nakonfigurovali GoToMeeting pro jednotné přihlašování, vyhledejte vaši instanci GoToMeeting pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **GoToMeeting** v galerii aplikací. Ve výsledcích hledání vyberte GoToMeeting a přidejte je do seznamu aplikací.

1. Vyberte svou instanci GoToMeeting a pak vyberte kartu **zřizování** .

1. Nastavte režim **zřizování** na **automaticky**. 

    ![Snímek obrazovky s kartou zřizování pro GoToMeeting v Azure Portal. Režim zřizování je nastaven na automatické a je zvýrazněné uživatelské jméno správce, heslo a testovací připojení.](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. V části pověření správce proveďte následující kroky:
   
    a. Do textového pole **uživatelské jméno správce GoToMeeting** zadejte uživatelské jméno správce.

    b. V textovém poli **heslo správce GoToMeeting** je heslo správce.

1. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci GoToMeeting. Pokud se připojení nepovede, ujistěte se, že má váš účet GoToMeeting oprávnění správce týmu, a zkuste znovu spustit krok **"přihlašovací údaje správce"** .

1. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části mapování vyberte **synchronizovat Azure Active Directory uživatelé GoToMeeting.**

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do GoToMeeting. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v GoToMeeting pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

1. Pokud chcete povolit službu Azure AD Provisioning pro GoToMeeting, změňte **stav zřizování** na **zapnuto** v části nastavení.

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených GoToMeeting v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci GoToMeeting.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


