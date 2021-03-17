---
title: 'Kurz: Konfigurace Elium pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: e8f027ccc577df79e561fca7194c20b6cc7ef2c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005499"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Kurz: Konfigurace Elium pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak nakonfigurovat Elium a Azure Active Directory (Azure AD) k automatickému zřízení a zrušení zřizování uživatelů nebo skupin pro Elium.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Důležité informace o tom, co tato služba dělá a jak funguje, a nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro SaaS aplikací pomocí Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je momentálně ve verzi Preview. Obecné informace o použití funkcí Azure ve verzi Preview najdete v tématu [dodatečné licenční podmínkami použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že už máte následující předpoklady:

* Tenant Azure AD
* [Tenant Elium](https://www.elium.com/pricing/)
* Uživatelský účet v Elium s oprávněními správce

## <a name="assigning-users-to-elium"></a>Přiřazování uživatelů k Elium

Azure AD používá koncept nazvaný *přiřazení* k určení uživatelů, kteří přijímají přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Elium. Pak přiřaďte tyto uživatele a skupiny k Elium pomocí postupu v části [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Důležité tipy pro přiřazení uživatelů k Elium 

Doporučujeme, abyste jednomu uživateli Azure AD přiřadili Elium k otestování automatické konfigurace zřizování uživatelů. Další uživatele a skupiny se dají přiřadit později.

Když přiřadíte uživatele k Elium, musíte vybrat platnou roli specifickou pro aplikaci (Pokud je dostupná) v dialogovém okně přiřazení. Uživatelé, kteří mají **výchozí roli přístupu** , jsou ze zřizování vyloučeni.

## <a name="set-up-elium-for-provisioning"></a>Nastavení Elium pro zřizování

Před konfigurací Elium pro Automatické zřizování uživatelů se službou Azure AD musíte povolit systém pro zřizování SCIM (Domain Identity Management) na Elium. Postupujte takto:

1. Přihlaste se k Elium a **My Profile** pokračujte na  >  **Nastavení** profilu.

    ![Položka nabídky nastavení v Elium](media/Elium-provisioning-tutorial/setting.png)

1. V levém dolním rohu v části **Upřesnit** vyberte **zabezpečení**.

    ![Odkaz na zabezpečení v Elium](media/Elium-provisioning-tutorial/security.png)

1. Zkopírujte **adresu URL tenanta** a hodnoty **tajného tokenu** . Tyto hodnoty použijete později v příslušných polích na kartě **zřizování** aplikace Elium v Azure Portal.

    ![Adresa URL tenanta a pole tajného tokenu v Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Přidání Elium z Galerie

Pokud chcete nakonfigurovat Elium pro Automatické zřizování uživatelů pomocí Azure AD, musíte taky přidat Elium z Galerie aplikací Azure AD do seznamu spravovaných aplikací typu software jako služba (SaaS). Postupujte takto:

1. V [Azure Portal](https://portal.azure.com)v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Položka nabídky Azure Active Directory](common/select-azuread.png)

1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

     ![Okno Azure AD Enterprise Applications](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna možnost **Nová aplikace** .

    ![Odkaz na novou aplikaci](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **Elium**, v seznamu výsledků vyberte **Elium** a pak vyberte **Přidat** , aby se aplikace přidala.

    ![Vyhledávací pole galerie](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurace automatického zřizování uživatelů na Elium

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů a skupin v Elium podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování pro Elium na základě Security Assertion Markup Language (SAML) podle pokynů v [kurzu Elium jednotného přihlašování](Elium-tutorial.md). Jednotné přihlašování můžete nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce spolu doplňují.

Pokud chcete nakonfigurovat automatické zřizování uživatelů pro Elium ve službě Azure AD, postupujte podle těchto kroků:

1. Přihlaste se k [Azure Portal](https://portal.azure.com), vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno Azure AD Enterprise Applications](common/enterprise-applications.png)

1. V seznamu aplikace vyberte **Elium**.

    ![Seznam aplikací v okně podnikové aplikace](common/all-applications.png)

1. Vyberte kartu **Zřizování**.

    ![Karta zřizování v okně podnikové aplikace](common/provisioning.png)

1. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Automatické nastavení pro režim zřizování](common/provisioning-automatic.png)

1. V části **přihlašovací údaje správce** zadejte do pole **Adresa URL klienta** **\<tenantURL\> /SCIM/v2** . ( **TenantURL** je hodnota načtená dříve z konzoly pro správu Elium.) Zadejte také hodnotu **tajného tokenu** Elium do pole **token tajného** kódu. Nakonec vyberte **Test připojení** , abyste ověřili, že se služba Azure AD může připojit k Elium. Pokud se připojení nepovede, ujistěte se, že má váš účet Elium oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta a pole tajného tokenu v přihlašovacích údajích správce](common/provisioning-testconnection-tenanturltoken.png)

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, kterým se budou zobrazovat oznámení o chybách zřizování. Potom zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

1. Klikněte na **Uložit**.

1. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Elium**.

    ![Synchronizovat odkaz pro mapování uživatelů Azure AD na Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Zkontrolujte atributy uživatele synchronizované z Azure AD do Elium v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Elium pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    ![Mapování atributů mezi Azure AD a Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Pokud chcete povolit službu Azure AD Provisioning pro Elium, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Stav zřizování je nastaven na zapnuto.](common/provisioning-toggle-on.png)

1. Určete uživatele a skupiny, které chcete zřídit pro Elium, tak, že vyberete požadované hodnoty v rozevíracím seznamu **obor** v části **Nastavení** .

    ![Seznam oborů zřizování](common/provisioning-scope.png)

1. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Tlačítko Uložit pro konfiguraci zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v **oboru** v části **Nastavení** . Tento proces počáteční synchronizace trvá déle než pozdější synchronizace. Další informace o době potřebné ke zřízení najdete v tématu [Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování. Sestava aktivity zřizování popisuje všechny akce prováděné službou zřizování Azure AD v Elium. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
