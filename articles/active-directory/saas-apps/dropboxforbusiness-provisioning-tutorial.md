---
title: 'Kurz: Konfigurace Dropbox for Business pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů na Dropbox for Business.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: zchia
ms.openlocfilehash: c95346ff9026b7fc8c9141234fb3cde9c0809d23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053462"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Kurz: Konfigurace Dropbox for Business pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Dropboxu pro firmy a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny na Dropbox for Business.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* [Dropbox pro obchodní tenanta](https://www.dropbox.com/business/pricing)
* Účet uživatele s oprávněními správce v Dropboxu pro firmy.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Dropbox for Business přidat z Galerie

Před konfigurací Dropbox for Business pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Dropbox for Business z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Dropbox for Business přidat z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V  **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu vyberte **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části podokna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Dropbox for Business**vyberte **Dropbox for Business** panel výsledků a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Dropbox for Business v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Přiřazování uživatelů k Dropboxu pro firmy

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze na uživatele a/nebo skupiny, které jsou přiřazené k aplikaci ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Dropboxu pro firmy. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny na Dropbox for Business podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Důležité tipy pro přiřazování uživatelů k Dropboxu pro firmy

* Dále je doporučeno jednoho uživatele Azure AD je přiřazen k Dropboxu pro firmy k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazování uživatele na Dropbox for Business, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurace automatické zřizování uživatelů na Dropbox for Business 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Dropboxu pro firmy podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Dropbox for Business, postupujte podle pokynů uvedených v [z Dropboxu obchodní jednotné přihlašování – kurz](dropboxforbusiness-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Dropbox for Business ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Dropbox for Business**.

    ![Dropbox pro obchodní odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Zřizování](common/provisioning-automatic.png)

5. V části **přihlašovacích údajů správce** klikněte na tlačítko **Authorize**. Dropbox pro obchodní přihlašovací dialogové okno se otevře v novém okně prohlížeče.

    ![Zřizování ](common/provisioning-oauth.png)

6. Na **přihlásit se na Dropbox for Business, které chcete propojit s Azure AD** dialog, přihlaste se do vaší schránky pro obchodní tenanta a ověřit vaši identitu.

    ![Dropbox obchodní přihlásit](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Po dokončení kroku 5 a 6, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit na Dropbox for Business. Pokud se nepovede, ujistěte se, že váš Dropbox pro obchodní účet má oprávnění správce a zkuste to znovu.

    ![Podpisový](common/provisioning-testconnection-oauth.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![E-mailové oznámení](common/provisioning-notification-email.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users na Dropbox**.

    ![Mapování uživatelů na dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Dropboxu v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Dropboxu pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy uživatele Dropboxu](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory do Dropboxu**.

    ![Mapování skupin Dropboxu](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD do Dropboxu v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v Dropboxu pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Atributy skupiny Dropboxu](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Služba pro Dropbox zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Stav zřizování zapnutém](common/provisioning-toggle-on.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení na Dropbox výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Zřizování oboru](common/provisioning-scope.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Ukládá se konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Dropboxu zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru
 
* Dropbox nepodporuje pozastavení pozvaným uživatelům. Pokud pozvaného uživatele je pozastavený, tento uživatel se odstraní.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

