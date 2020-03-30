---
title: 'Kurz: Konfigurace služby Netsuite OneWorld pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a Netsuite OneWorld.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9a823e6515c2bfe09e1ab7bcef471eb8169e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063291"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Kurz: Konfigurace sady Netsuite pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v Netsuite OneWorld a Azure AD automaticky zřídit a de-provision uživatelské účty z Azure AD do Netsuite.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

*   Tenant advitus azure active directory.
*   Předplatné Netsuite OneWorld. Všimněte si, že automatické zřizování uživatelů je v současné době podporována pouze s NetSuite OneWorld.
*   Uživatelský účet v netsuite s oprávněními správce.

## <a name="assigning-users-to-netsuite-oneworld"></a>Přiřazení uživatelů k Netsuite OneWorld

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Netsuite. Jakmile se rozhodnete, můžete tyto uživatele přiřadit k aplikaci Netsuite podle pokynů zde:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Důležité tipy pro přiřazení uživatelů k Netsuite OneWorld

*   Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Netsuite k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

*   Při přiřazování uživatele k sadě Netsuite je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů Netsuite a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v Netsuite na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP] 
> Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Netsuite podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="to-configure-user-account-provisioning"></a>Konfigurace zřizování uživatelských účtů:

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory do sady Netsuite.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

1. Pokud jste již nakonfigurovali Netsuite pro jednotné přihlašování, vyhledejte svou instanci Netsuite pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Netsuite** v galerii aplikací. Ve výsledcích hledání vyberte Netsuite a přidejte ho do seznamu aplikací.

1. Vyberte svou instanci Netsuite a pak vyberte kartu **Zřizování.**

1. Nastavte **režim zřizování** na **automatické**. 

    ![Zajišťování](./media/netsuite-provisioning-tutorial/provisioning.png)

1. V části **Pověření správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **Uživatelské jméno správce** zadejte název účtu Netsuite, který má v Netsuite.com přiřazen profil správce **systému.**
   
    b. Do textového pole **Heslo správce** zadejte heslo pro tento účet.
      
1. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Netsuite.

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**

1. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s netsuite.**

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Netsuite. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v netsuite pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

1. Chcete-li povolit službu zřizování Azure AD pro Netsuite, změňte **stav zřizování** **na Zapnuto** v části Nastavení

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů a/nebo skupin přiřazených k Netsuite v sekci Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Netsuite.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](netsuite-tutorial.md)
