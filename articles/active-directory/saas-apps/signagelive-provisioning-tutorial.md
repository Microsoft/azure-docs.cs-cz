---
title: 'Kurz: Konfigurace Signagelive pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro Signagelive.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: f6bb119e5e82ce642722d0f739177fc1e4c06c25
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255716"
---
# <a name="tutorial-configure-signagelive--for-automatic-user-provisioning"></a>Kurz: Konfigurace Signagelive pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v Signagelive a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů nebo skupin pro Signagelive.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenanta Azure AD.
* [Tenant Signagelive](https://signagelive.com/pricing/)
* Uživatelský účet v Signagelive s oprávněními správce.

## <a name="assigning-users-to-signagelive"></a>Přiřazování uživatelů k Signagelive   

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Signagelive. Po rozhodnutí můžete přiřadit tyto uživatele nebo skupiny k Signagelive podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-signagelive"></a>Důležité tipy pro přiřazení uživatelů k Signagelive   

* Doporučuje se, aby se k Signagelive k testování automatické konfigurace zřizování uživatelů přiřadil jeden uživatel Azure AD. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Signagelive musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="setup-signagelive--for-provisioning"></a>Nastavení Signagelive pro zřizování

Před konfigurací Signagelive pro Automatické zřizování uživatelů pomocí Azure AD budete muset povolit SCIM zřizování na Signagelive.

  Zajistěte si přístup k  [Signagelive](mailto:development@signagelive.com) , abyste získali tajný token potřebný ke konfiguraci zřizování SCIM.

## <a name="add-signagelive-from-the-gallery"></a>Přidání Signagelive z Galerie

Pokud chcete nakonfigurovat Signagelive pro Automatické zřizování uživatelů pomocí Azure AD, musíte přidat Signagelive z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Signagelive z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Signagelive**, na panelu výsledků vyberte **Signagelive** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Signagelive v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-signagelive"></a>Konfigurace automatického zřizování uživatelů na Signagelive    

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Signagelive na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
>  Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro Signagelive podle pokynů uvedených v [kurzu Signagelive jednotného přihlašování](Signagelive-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-signagelive--in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Signagelive ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Signagelive**.

    ![Odkaz Signagelive v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části přihlašovací údaje správce zadejte ` https://samlapi.signagelive.com/scim/v2` **adresu URL tenanta**. Do pole **tajný token** zadejte hodnotu **nosných tokenů** poskytovanou technickým vývojářským týmem. Klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Signagelive. Pokud se připojení nepovede, ujistěte se, že má váš účet Signagelive oprávnění správce, a zkuste to znovu.
    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Signagelive**.

    ![Snímek obrazovky s oddílem mapování s možností synchronizovat Azure Active Directory uživatele do Signagelive, která se vyvolala](media/signagelive-provisioning-tutorial/usermapping.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD do Signagelive v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Signagelive pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Snímek obrazovky s oddílem mapování atributů se zobrazenými sedmi mapováními](media/signagelive-provisioning-tutorial/userattribute.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupinu do Signagelive**.

    ![Snímek obrazovky s oddílem mapování se skupinou synchronizace Azure Active Directory k Signagelive možnosti s názvem.](media/signagelive-provisioning-tutorial/groupmapping.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD do Signagelive v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupinových účtů v Signagelive pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Snímek obrazovky oddílu mapování atributů se třemi zobrazenými mapováními](media/signagelive-provisioning-tutorial/groupattribute.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro Signagelive, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit pro Signagelive, výběrem požadovaných hodnot v **oboru** v části **Nastavení** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než následné synchronizace. Další informace o tom, jak dlouho bude trvat pro uživatele a/nebo skupiny, najdete v článku [Jak dlouho bude trvat, než budou uživatelé zřizovat](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Pomocí oddílu **aktuální stav** můžete monitorovat průběh a postupovat podle odkazů na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD v Signagelive. Další informace najdete v tématu o [kontrole stavu zřizování uživatelů](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Pokud si chcete přečíst protokoly zřizování Azure AD, přečtěte si téma [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
