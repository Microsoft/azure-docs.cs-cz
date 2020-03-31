---
title: 'Kurz: Konfigurace Jive pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6fbfdbe7-d66c-4305-9fea-76d6a6a92830
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602eed65745eea1fd9096508c442a27ea79bcba9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057730"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Kurz: Konfigurace Jive pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést v Jive a Azure AD automaticky zřídit a de-zřizování uživatelských účtů z Azure AD do Jive.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

*   Tenant advitus azure active directory.
*   Jive jednotné přihlášení na povolené předplatné.
*   Uživatelský účet v Jive s oprávněními správce týmu.

## <a name="assigning-users-to-jive"></a>Přiřazení uživatelů k Jive

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Jive. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Jive podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Důležité tipy pro přiřazení uživatelů do Jive

*   Doporučuje se, aby jeden uživatel Azure AD přiřazen jive k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

*   Při přiřazování uživatele k jive je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů Jive a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v Jive na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Jive podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory do Jive.
V rámci tohoto postupu je nutné zadat token zabezpečení uživatele, který potřebujete požadovat od Jive.com.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

1. Pokud jste již nakonfigurovali Jive pro jednotné přihlašování, vyhledejte svou instanci Jive pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Jive** v galerii aplikací. Ve výsledcích hledání vyberte Jive a přidejte ji do seznamu aplikací.

1. Vyberte svou instanci Jive a pak vyberte kartu **Zřizování.**

1. Nastavte **režim zřizování** na **automatické**. 

    ![Zajišťování](./media/jive-provisioning-tutorial/provisioning.png)

1. V části **Pověření správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **Uživatelské jméno správce Jive** zadejte název účtu Jive, který má v Jive.com přiřazen profil **správce systému.**
   
    b. Do textového pole **Heslo správce jive** zadejte heslo pro tento účet.
   
    c. Do textového pole **Adresa URL klienta Jive** zadejte adresu URL klienta Jive.
      
      > [!NOTE]
      > Adresa URL klienta Jive je adresa URL, kterou vaše organizace používá k přihlášení do Jive.  
      > Adresa URL má obvykle následující formát: **www.\< organizace\>.jive.com**.          

1. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Jive.

1. Do pole **E-mail** s oznámením zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

1. Klikněte na **Uložit.**

1. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s Jive.**

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Jive. Atributy vybrané jako **odpovídající** vlastnosti se používají k porovnání uživatelských účtů v Jive pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

1. Chcete-li povolit službu zřizování Azure AD pro Jive, změňte **stav zřizování** **na Zapnuto** v části Nastavení

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů a/nebo skupin přiřazených Jive v sekci Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Jive.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](jive-tutorial.md)
