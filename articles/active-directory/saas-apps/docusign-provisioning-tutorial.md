---
title: 'Kurz: Konfigurace DocuSign pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
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
ms.openlocfilehash: 121d147a3f8c91f17e955120b2c14f7dbd3da592
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191839"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Kurz: Konfigurace DocuSign pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v DocuSign a Azure AD automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do DocuSign.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   DocuSign jednotného přihlašování povolená předplatného.
*   Účet uživatele s oprávněními správce týmu v DocuSign.

## <a name="assigning-users-to-docusign"></a>Přiřazování uživatelů k DocuSign

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují, kteří potřebují přístup k vaší aplikaci DocuSign. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace DocuSign podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Důležité tipy pro přiřazování uživatelů k DocuSign

*   Dále je doporučeno jednoho uživatele Azure AD, je přiřazená DocuSign k otestování konfigurace zřizování. Další uživatele může být přiřazen později.

*   Při přiřazení uživatele k DocuSign, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

> [!NOTE]
> Azure AD nepodporuje skupinové zřizování s aplikací Docusign, jenom uživatelé se dá zřídit.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojením služby Azure AD na uživatelský účet DocuSign pro rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v DocuSign podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete také povolena založené na SAML jednotného přihlašování pro DocuSign, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-user-account-provisioning"></a>Postup konfigurace zřizování uživatelských účtů:

Cílem této části se popisují, jak povolit zřizování uživatelů z Active Directory uživatelské účty do DocuSign.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

1. Pokud jste už nakonfigurovali DocuSign pro jednotné přihlašování, vyhledejte svoji instanci služby DocuSign pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **DocuSign** v galerii aplikací. Ve výsledcích hledání vyberte DocuSign a přidat do seznamu aplikací.

1. Vyberte instanci DocuSign a potom **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**. 

    ![zřizování](./media/docusign-provisioning-tutorial/provisioning.png)

1. V části **přihlašovacích údajů správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce** textového pole zadejte název, který se má účet DocuSign **správce systému** profilu v DocuSign.com přiřazené.
   
    b. V **heslo správce** textového pole zadejte heslo pro tento účet.

1. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci DocuSign.

1. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měl přijímat oznámení zřizování chyby a zaškrtněte políčko.

1. Klikněte na tlačítko **uložit.**

1. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům DocuSign.**

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do DocuSign. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v DocuSign pro operace update. Vyberte tlačítko Uložit potvrďte změny.

1. Chcete-li povolit služba pro DocuSign zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

1. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci ze všech uživatelů, DocuSign, v části Uživatelé a skupiny přiřazené. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci DocuSign zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](docusign-tutorial.md)
