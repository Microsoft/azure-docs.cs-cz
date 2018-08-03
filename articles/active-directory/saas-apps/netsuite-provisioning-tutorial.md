---
title: 'Kurz: Konfigurace Netsuite pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3dac2b1c90f6555abc71a52d75f8d58958d978c7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449475"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Kurz: Konfigurace Netsuite pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést Netsuite a Azure AD a automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do Netsuite.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   Netsuite jednotné přihlašování v předplatném povolené.
*   Uživatelský účet v Netsuite s oprávněními správce týmu.

## <a name="assigning-users-to-netsuite"></a>Přiřazování uživatelů k Netsuite

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD, kteří potřebují přístup k vaší aplikaci Netsuite představují. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace Netsuite podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>Důležité tipy pro přiřazování uživatelů k Netsuite

*   Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Netsuite k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Netsuite, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojením služby Azure AD vaší Netsuite uživatelského účtu rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Netsuite podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP] 
> Můžete také pro Netsuite povoleno založené na SAML jednotného přihlašování, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-user-account-provisioning"></a>Postup konfigurace zřizování uživatelských účtů:

Cílem této části se popisují, jak povolit zřizování uživatelů z Active Directory uživatelské účty do Netsuite.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

1. Pokud jste už nakonfigurovali Netsuite pro jednotné přihlašování, vyhledejte svoji instanci služby Netsuite pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Netsuite** v galerii aplikací. Ve výsledcích hledání vyberte Netsuite a přidat do seznamu aplikací.

1. Vyberte instanci Netsuite a potom **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**. 

    ![zřizování](./media/netsuite-provisioning-tutorial/provisioning.png)

1. V části **přihlašovacích údajů správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce** textové pole, typ Netsuite účtu název, který má **správce systému** profilu v Netsuite.com přiřazené.
   
    b. V **heslo správce** textového pole zadejte heslo pro tento účet.
      
1. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Netsuite.

1. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měl přijímat oznámení zřizování chyby a zaškrtněte políčko.

1. Klikněte na tlačítko **uložit.**

1. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům Netsuite.**

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do Netsuite. Všimněte si, že vybrané atributy jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Netsuite pro operace update. Vyberte tlačítko Uložit potvrďte změny.

1. Chcete-li povolit služba pro Netsuite zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

1. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Netsuite v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci Netsuite zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](netsuite-tutorial.md)