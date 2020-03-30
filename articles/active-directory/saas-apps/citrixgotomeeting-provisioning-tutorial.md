---
title: 'Kurz: Konfigurace programu GoToMeeting pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0ac06fc3018b4230cbf32712067c48400599082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058259"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Kurz: Konfigurace programu GoToMeeting pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v GoToMeeting a Azure AD automaticky zřídit a de-provision uživatelské účty z Azure AD na GoToMeeting.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

*   Tenant advitus azure active directory.
*   Předplatné s povoleným jedním přihlášením GoToMeeting.
*   Uživatelský účet v gotoprogramu s oprávněními správce týmu.

## <a name="assigning-users-to-gotomeeting"></a>Přiřazení uživatelů ke schůzce GoToMeeting

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci GoToMeeting. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci GoToMeeting podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Důležité tipy pro přiřazení uživatelů ke schůzce GoToMeeting

*   Doporučuje se, aby jeden uživatel Azure AD je přiřazen k GoToMeeting otestovat konfiguraci zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

*   Při přiřazování uživatele ke programu GoToMeeting je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolení automatického zřizování uživatelů

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů GoToMeeting a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v programu GoToMeeting na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také zvolit povolení jednotného přihlašování na saml pro GoToMeeting podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů:

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

1. Pokud jste již nakonfigurovali gotomeeting pro jednotné přihlašování, vyhledejte svou instanci programu GoToMeeting pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **GoToMeeting** v galerii aplikací. Ve výsledcích hledání vyberte GoToMeeting a přidejte ho do seznamu aplikací.

1. Vyberte svou instanci GoToMeeting a pak vyberte kartu **Zřizování.**

1. Nastavte režim **zřizování** na **automatické**. 

    ![Zajišťování](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. V části Pověření správce proveďte následující kroky:
   
    a. Do textového pole **GoToMeeting Admin User Name** zadejte uživatelské jméno správce.

    b. V textovém poli **GoToMeeting Admin Password** heslo heslo správce.

1. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci GoToMeeting. Pokud se připojení nezdaří, ujistěte se, že váš účet GoToMeeting má oprávnění správce týmu a zkuste krok **"Pověření správce"** znovu.

1. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s programem GoToMeeting.**

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na GoToMeeting. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v gotoprogramu pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

1. Chcete-li povolit službu zřizování Azure AD pro GoToMeeting, změňte **stav zřizování** **na Zapnuto** v části Nastavení

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených k programu GoToMeeting v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné zřizovací službou v aplikaci GoToMeeting.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


