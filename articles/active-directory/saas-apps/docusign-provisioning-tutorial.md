---
title: 'Kurz: Konfigurace docuSignu pro automatické zřizování uživatelů pomocí služby Azure Active Directory| Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88b65c8e8962ad8420ded47da1a343672123c589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058174"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Kurz: Konfigurace docuSignu pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v DocuSign a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD do DocuSign.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

*   Tenant advitus azure active directory.
*   DocuSign jednotné přihlášení povoleno předplatné.
*   Uživatelský účet v DocuSign s oprávněními správce týmu.

## <a name="assigning-users-to-docusign"></a>Přiřazení uživatelů k DocuSign

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci DocuSign. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci DocuSign podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Důležité tipy pro přiřazení uživatelů do DocuSign

*   Doporučuje se, aby jeden uživatel Azure AD je přiřazen do DocuSign k testování konfigurace zřizování. Další uživatelé mohou být přiřazeny později.

*   Při přiřazování uživatele do DocuSign je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

> [!NOTE]
> Azure AD nepodporuje zřizování skupiny s aplikací Docusign, zřídit jenom uživatelé.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojením vašeho Azure AD k rozhraní API pro zřizování uživatelských účtů DocuSign a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v DocuSign u přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete se také rozhodnout povolit jednotné přihlašování na saml pro DocuSign podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory do doaplikace DocuSign.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

1. Pokud jste již nakonfigurovali DocuSign pro jednotné přihlašování, vyhledejte svou instanci DocuSign pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **DocuSign** v galerii aplikací. Ve výsledcích hledání vyberte DocuSign a přidejte ho do seznamu aplikací.

1. Vyberte svou instanci DocuSign a pak vyberte kartu **Zřizování.**

1. Nastavte **režim zřizování** na **automatické**. 

    ![Zajišťování](./media/docusign-provisioning-tutorial/provisioning.png)

1. V části **Pověření správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **Uživatelské jméno správce** zadejte název účtu DocuSign, který má v DocuSign.com přiřazen profil správce **systému.**
   
    b. Do textového pole **Heslo správce** zadejte heslo pro tento účet.

1. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci DocuSign.

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s docuSignem.**

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do DocuSign. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v DocuSign pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

1. Chcete-li povolit službu zřizování Azure AD pro DocuSign, změňte **stav zřizování** **na Zapnuto** v části Nastavení

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů přiřazených do DocuSign v sekci Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci DocuSign.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](docusign-tutorial.md)
