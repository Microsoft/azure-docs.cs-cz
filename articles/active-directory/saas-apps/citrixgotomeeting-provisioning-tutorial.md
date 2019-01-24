---
title: 'Kurz: Konfigurace GoToMeeting pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 1368bccfd71013f4f3c84148be1e74ffc98e3a51
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54815680"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Kurz: Konfigurace GoToMeeting pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést GoToMeeting a Azure AD a automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do GoToMeeting.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   GoToMeeting jednotného přihlašování povolená předplatného.
*   Účet uživatele s oprávněními správce týmu v GoToMeeting.

## <a name="assigning-users-to-gotomeeting"></a>Přiřazování uživatelů k GoToMeeting

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují, kteří potřebují přístup k vaší aplikaci GoToMeeting. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace GoToMeeting podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Důležité tipy pro přiřazování uživatelů k GoToMeeting

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazená GoToMeeting pro testování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k GoToMeeting, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit automatické zřizování uživatelů

Tato část vás provede připojení k rozhraní API zřizování GoToMeeting pro uživatelský účet služby Azure AD a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v GoToMeeting podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také pro GoToMeeting povoleno založené na SAML jednotného přihlašování, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-account-provisioning"></a>Postup konfigurace zřizování automatické uživatelských účtů:

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

1. Pokud jste už nakonfigurovali GoToMeeting pro jednotné přihlašování, vyhledejte svoji instanci služby GoToMeeting pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **GoToMeeting** v galerii aplikací. Ve výsledcích hledání vyberte GoToMeeting a přidat do seznamu aplikací.

1. Vyberte instanci GoToMeeting a potom **zřizování** kartu.

1. Nastavte **zřizování** režimu **automatické**. 

    ![zřizování](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. V části přihlašovací údaje správce proveďte následující kroky:
   
    a. V **uživatelské jméno správce GoToMeeting** textového pole zadejte uživatelské jméno správce.

    b. V **heslo správce GoToMeeting** textového pole hesla správce.

1. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci GoToMeeting. Pokud se nepovede, zajistěte váš GoToMeeting účet má oprávnění správce týmu a **"Přihlašovací údaje správce"** krok znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko.

1. Klikněte na tlačítko **uložit.**

1. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům GoToMeeting.**

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do GoToMeeting. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v GoToMeeting pro operace update. Vyberte tlačítko Uložit potvrďte změny.

1. Chcete-li povolit služba pro GoToMeeting zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

1. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k GoToMeeting v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci GoToMeeting zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


