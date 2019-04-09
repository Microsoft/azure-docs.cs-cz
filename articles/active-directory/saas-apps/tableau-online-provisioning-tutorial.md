---
title: 'Kurz: Konfigurace Tableau Online pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do režimu Online Tableau.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f732eebd410a6b52a21a46925a29bf4676f7c8cb
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59270782"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Kurz: Konfigurace Tableau Online pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Tableau Online a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Tableau Online.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že již máte následující:

*   Klient služby Azure AD
*   A [Tableau Online tenanta](https://www.tableau.com/)
*   Uživatelský účet v Tableau Online s oprávněními správce

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), která je k dispozici vývojářům Tableau Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Přidání Tableau Online z Galerie
Před konfigurací Tableau Online pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Tableau Online z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Tableau Online z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau Online**vyberte **Tableau Online** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Tableau Online v seznamu výsledků](common/search-new-app.png)

## <a name="assigning-users-to-tableau-online"></a>Přiřazování uživatelů k Tableau Online

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Tableau Online. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Tableau Online podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Důležité tipy pro přiřazování uživatelů k Tableau Online

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazen k Tableau Online k otestování automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Tableau Online, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Konfigurace automatické zřizování uživatelů pro Tableau Online

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Tableau Online podle uživatele a/nebo přiřazení skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Tableau Online, postupujte podle pokynů uvedených v [Tableau jednotné přihlašování – kurz Online](tableauonline-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Tableau Online ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Tableau Online**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Tableau Online**.

    ![Tableau Online odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **domény**, **uživatelské jméno správce**, **heslo správce**, a **obsahu Adresa URL** Tableau Online účtu:

   * V **domény** pole, naplnění subdoménu podle kroku 6.

   * V **uživatelské jméno správce** pole, uživatelské jméno účtu správce ve svém Tenantovi Clarizen naplnit. Příklad: admin@contoso.com.

   * V **heslo správce** pole, naplnění heslo účtu správce odpovídající uživatelské jméno správce.

   * V **adresa URL obsahu** pole, naplnění subdoménu podle kroku 6.

6. Po přihlášení na administrativní účet pro Online tableau. představují hodnoty **domény** a **adresa URL obsahu** může být extrahována z adresy URL stránky pro správu.

    * **Domény** pro Tableau Online účtu lze kopírovat z část této adresy URL:

        ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **Adresa URL obsahu** pro Tableau Online účtu lze kopírovat z této části, a je definována hodnota při nastavování účtu. V tomto příkladu je hodnota "contoso":

        ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Vaše **domény** může být jiný než ten, který je vidět tady.

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit ke službě Tableau Online. Pokud se nepovede, ujistěte se, že Tableau Online účet má oprávnění správce a zkuste to znovu.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtněte políčko **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Tableau**.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Tableau Online v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Tableau Online pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Tableau**.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD k Tableau Online v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Tableau Online pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Služba pro Tableau Online zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definovat uživatele a/nebo skupiny, které chcete ke zřízení Tableau online výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce provedené v Azure AD na Tableau Online služby zřizování.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
