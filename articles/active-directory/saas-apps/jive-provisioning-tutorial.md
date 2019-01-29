---
title: 'Kurz: Konfigurace Jive pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jive.
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
ms.openlocfilehash: 302c87eab4fdf7e7d7553fe002c14b42c804a18a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178150"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Kurz: Konfigurace Jive pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v Jive a Azure AD automaticky zřizovat a rušit přístup uživatelských účtů ze služby Azure AD k Jive.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   Jive jednotné přihlašování v předplatném povolené.
*   Uživatelský účet v Jive s oprávněními správce týmu.

## <a name="assigning-users-to-jive"></a>Přiřazování uživatelů k Jive

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD, kteří potřebují přístup k vaší aplikaci Jive představují. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace Jive podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Důležité tipy pro přiřazování uživatelů k Jive

*   Dále je doporučeno jednoho uživatele Azure AD pro Jive přidělí k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Jive, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojení k rozhraní API zřizování Jive uživatelský účet služby Azure AD a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Jive podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolena založené na SAML jednotného přihlašování pro Jive, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-user-account-provisioning"></a>Postup konfigurace zřizování uživatelských účtů:

Cílem této části se popisují, jak povolit zřizování uživatelů z Active Directory uživatelské účty do Jive.
V rámci tohoto postupu je nutné poskytnout token zabezpečení uživatele, které je potřeba Jive.com žádat.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

1. Pokud jste už nakonfigurovali Jive pro jednotné přihlašování, vyhledejte svoji instanci služby Jive pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Jive** v galerii aplikací. Ve výsledcích hledání vyberte Jive a přidat do seznamu aplikací.

1. Vyberte instanci Jive a potom **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**. 

    ![zřizování](./media/jive-provisioning-tutorial/provisioning.png)

1. V části **přihlašovacích údajů správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce Jive** textové pole, typ Jive účtu název, který má **správce systému** profilu v Jive.com přiřazené.
   
    b. V **heslo správce Jive** textového pole zadejte heslo pro tento účet.
   
    c. V **adresa URL Jive Tenanta** textového pole zadejte adresu URL Jive tenanta.
      
      > [!NOTE]
      > Adresa URL Jive tenanta je URL, která se používá ve vaší organizaci se přihlaste k Jive.  
      > Obvykle, adresa URL má následující formát: **www.\< organizace\>. jive.com**.          

1. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Jive.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko níže.

1. Klikněte na tlačítko **uložit.**

1. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům Jive.**

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do Jive. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Jive pro operace update. Vyberte tlačítko Uložit potvrďte změny.

1. Chcete-li povolit služba pro Jive zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

1. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Jive v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci Jive zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](jive-tutorial.md)
