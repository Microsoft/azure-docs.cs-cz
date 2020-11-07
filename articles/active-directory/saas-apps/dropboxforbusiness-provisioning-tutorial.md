---
title: 'Kurz: Konfigurace Dropboxu pro firmy pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro Automatické zřizování a rušení uživatelských účtů pro Dropbox pro firmy.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 7bbf9b199b15815127f0ecb26ae027ef7bfa52d2
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94355409"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Kurz: Konfigurace Dropboxu pro firmy pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v Dropboxu pro firmy a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD tak, aby automaticky zřídily a zrušily zřizování uživatelů a skupin do Dropboxu pro firmy.

> [!IMPORTANT]
> Microsoft a Dropbox budou svou starou integraci Dropboxu používat jako efektivní 04/01/2021. Abyste se vyhnuli přerušení služby, doporučujeme migrovat na novou integraci Dropboxu, která podporuje skupiny. K migraci na novou integraci Dropboxu přidejte a nakonfigurujte novou instanci Dropboxu pro zřizování ve vašem tenantovi Azure AD pomocí následujících kroků. Jakmile nakonfigurujete novou integraci Dropboxu, zakažte zřizování na staré integraci Dropboxu, abyste zabránili problémům zřizování.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant Dropbox pro firmy](https://www.dropbox.com/business/pricing)
* Uživatelský účet v Dropboxu pro firmy s oprávněními správce.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Přidání Dropboxu pro firmy z Galerie

Před konfigurací Dropboxu pro firmy pro Automatické zřizování uživatelů se službou Azure AD musíte přidat Dropbox pro firmy z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Dropbox pro firmy z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Dropbox pro firmy** , na panelu výsledků vyberte **Dropbox pro firmy** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Dropbox pro firmy v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Přiřazení uživatelů k Dropboxu pro firmy

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k Dropboxu pro firmy. Po rozhodnutí můžete tyto uživatele a skupiny přiřadit do Dropboxu pro firmy podle pokynů uvedených tady:

* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Důležité tipy pro přiřazování uživatelů do Dropboxu pro firmy

* Doporučuje se, aby jeden uživatel Azure AD byl přiřazený k Dropboxu pro firmy, aby otestoval automatickou konfiguraci zřizování uživatelů. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k Dropboxu pro firmy, musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurace automatického zřizování uživatelů do Dropboxu pro firmy 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Dropboxu pro firmy na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout, že pro Dropbox pro firmy povolíte jednotné přihlašování založené na SAML, a to podle pokynů uvedených v [kurzu jednotného přihlašování v Dropboxu pro firmy](dropboxforbusiness-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Dropbox pro firmy v Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Dropbox pro firmy**.

    ![Odkaz Dropbox pro firmy v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **Přihlašovací údaje správce** klikněte na **Autorizovat**. Otevře přihlašovací dialog Dropbox pro firmy v novém okně prohlížeče.

    ![Zřizování ](common/provisioning-oauth.png)

6. V dialogu **přihlášení k Dropboxu pro firmy k propojení s Azure AD** se přihlaste k tenantovi Dropbox pro firmy a ověřte svoji identitu.

    ![Přihlášení do Dropboxu pro firmy](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Po dokončení kroků 5 a 6 klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k Dropboxu pro firmy. Pokud se připojení nepovede, zajistěte, aby měl účet Dropbox pro firmy oprávnění správce, a zkuste to znovu.

    ![Token](common/provisioning-testconnection-oauth.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelů do Dropboxu**.

    ![Mapování uživatelů Dropboxu](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Zkontrolujte atributy uživatele synchronizované z Azure AD do Dropboxu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Dropboxu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupin do Dropboxu**.

    ![Mapování skupin Dropboxu](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizované z Azure AD do Dropboxu v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v Dropboxu pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy skupiny Dropboxu](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Dropbox, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

16. Určete uživatele nebo skupiny, které chcete zřídit do Dropboxu, a to tak, že v části **Nastavení** vyberete požadované hodnoty v **oboru** .

    ![Rozsah zřizování](common/provisioning-scope.png)

17. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. Část **Podrobnosti o synchronizaci** můžete použít ke sledování průběhu a následného odkazu na sestavu aktivity zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Dropboxu.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru
 
* Dropbox nepodporuje pozastavení pozvaných uživatelů. Pokud je pozastavený uživatel pozastaven, tento uživatel se odstraní.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

