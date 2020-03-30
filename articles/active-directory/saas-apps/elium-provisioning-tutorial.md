---
title: 'Kurz: Konfigurace Elium pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a zřašovala uživatelské účty do Elium.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a3e1c936d46e0d9b0ad3b98dafeda7df0b4a23db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058459"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Kurz: Konfigurace Elium pro automatické zřizování uživatelů

Tento kurz ukazuje, jak nakonfigurovat Elium a Azure Active Directory (Azure AD) automaticky zřídit a de-provision uživatelů nebo skupin elium.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na nad službou Azure AD User Provisioning. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a nejčastější dotazy najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Preview. Obecné podmínky použití funkcí Azure ve verzi Preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Nájemce Elium](https://www.elium.com/pricing/)
* Uživatelský účet v Elium s oprávněními správce

## <a name="assigning-users-to-elium"></a>Přiřazení uživatelů k Elium

Azure AD používá koncept s názvem *přiřazení* k určení, kteří uživatelé získají přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé a skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Elium. Potom přiřaďte tyto uživatele a skupiny elium podle kroků v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Důležité tipy pro přiřazení uživatelů do Elium 

Doporučujeme přiřadit elium jednoho uživatele Azure AD k testování konfigurace automatického zřizování uživatelů. Další uživatelé a skupiny mohou být přiřazeny později.

Při přiřazování uživatele elium, musíte vybrat platnou roli specifické pro aplikaci (pokud jsou k dispozici) v dialogovém okně přiřazení. Uživatelé, kteří mají **roli výchozí přístup** jsou vyloučeni z zřizování.

## <a name="set-up-elium-for-provisioning"></a>Nastavení Elium pro zřizování

Před konfigurací Elium pro automatické zřizování uživatelů s Azure AD, musíte povolit system for cross-domain Identity Management (SCIM) zřizování na Elium. Postupujte následovně:

1. Přihlaste se do Elium a přejděte do části**Nastavení** **profilu** > .

    ![Nastavení položky nabídky v Elium](media/Elium-provisioning-tutorial/setting.png)

1. V levém dolním rohu vyberte v části **UPŘESNIT** **možnost Zabezpečení**.

    ![Bezpečnostní odkaz v Elium](media/Elium-provisioning-tutorial/security.png)

1. Zkopírujte hodnoty **adresy URL klienta** a **tokenu tajného klíče.** Tyto hodnoty použijete později v odpovídajících polích na kartě **Zřizování** vaší aplikace Elium na webu Azure Portal.

    ![Pole URL klienta a tajný token v Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Přidat Elium z galerie

Chcete-li nakonfigurovat Elium pro automatické zřizování uživatelů pomocí Azure AD, musíte také přidat Elium z galerie aplikací Azure AD do seznamu spravovaných aplikací pro software jako služba (SaaS). Postupujte následovně:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Položka nabídky Azure Active Directory](common/select-azuread.png)

1. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

     ![Okno aplikace Azure AD Enterprise](common/enterprise-applications.png)

1. Chcete-li přidat novou aplikaci, vyberte v horní části podokna možnost **Nová aplikace.**

    ![Odkaz na novou aplikaci](common/add-new-app.png)

1. Ve vyhledávacím poli zadejte **Elium**, vyberte **elium** v seznamu výsledků a pak vyberte **Přidat** a přidejte aplikaci.

    ![Vyhledávací pole galerie](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurace automatického zřizování uživatelů do Elium

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů a skupin v Elium, na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování pro Elium na základě jazyka podpisu zabezpečení (SAML) podle pokynů v [kurzu jednotného přihlášení Elium](Elium-tutorial.md). Jednotné přihlašování můžete konfigurovat nezávisle na automatickém zřizování uživatelů, i když se obě funkce vzájemně doplňují.

Chcete-li nakonfigurovat automatické zřizování uživatelů pro Elium ve službě Azure AD, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com)Portal , vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno aplikace Azure AD Enterprise](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **Elium**.

    ![Seznam aplikací v okně Podnikové aplikace](common/all-applications.png)

1. Vyberte kartu **Zřizování.**

    ![Karta Zřizování v okně Podnikové aplikace](common/provisioning.png)

1. Nastavte **režim zřizování** na **automatické**.

    ![Automatické nastavení režimu zřizování](common/provisioning-automatic.png)

1. V části **Pověření správce** zadejte ** \<adresu\>tenantURL /scim/v2** do pole ADRESA URL **klienta.** **(TenantURL** je hodnota načtená dříve z konzoly pro správu Elium.) Do pole **Tajný token** také zadejte hodnotu **tokenu Elium Secret.** Nakonec vyberte **testovat připojení** k ověření, že Azure AD můžete připojit k Elium. Pokud se připojení nezdaří, ujistěte se, že váš účet Elium má oprávnění správce a zkuste to znovu.

    ![Pole URL klienta a tajný token v pověřeních správce](common/provisioning-testconnection-tenanturltoken.png)

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která obdrží oznámení o chybách zřizování. Potom zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením](common/provisioning-notification-email.png)

1. Klikněte na **Uložit**.

1. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s Elium**.

    ![Synchronizovat propojení pro mapování uživatelů Azure AD na Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Elium v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Elium pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    ![Mapování atributů mezi Azure AD a Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Chcete-li povolit službu zřizování Azure AD pro Elium, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování nastavený na Zapnuto](common/provisioning-toggle-on.png)

1. Definujte uživatele a skupiny, které chcete zřídit elium výběrem hodnot, které chcete v rozevíracím seznamu **Obor** v části **Nastavení.**

    ![Seznam Obor zřizování](common/provisioning-scope.png)

1. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Tlačítko Uložit pro zřizování konfigurace](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a skupin definovaných v **oboru** v části **Nastavení.** Tento počáteční proces synchronizace trvá déle než pozdější synchronizace. Další informace o době potřebné pro zřizování naleznete [v tématu Jak dlouho bude trvat zřizování uživatelů?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Část **Aktuální stav** slouží ke sledování průběhu a sledování odkazů na přehled aktivit zřizování. Sestava aktivit zřizování popisuje všechny akce prováděné službou zřizování Azure AD na Elium. Další informace naleznete [v tématu Kontrola stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Chcete-li číst protokoly zřizování Azure AD, najdete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
