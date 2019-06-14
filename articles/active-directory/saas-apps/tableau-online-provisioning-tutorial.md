---
title: 'Kurz: Konfigurace Tableau Online pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a zrušit jejich zřízení uživatelských účtů do režimu Online Tableau.
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
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708924"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Kurz: Konfigurace Tableau Online pro automatické zřizování uživatelů

V tomto kurzu si ukážeme postup provést v Tableau Online a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a zrušit jejich zřízení uživatelů a skupin a tableau. představují Online.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na služba zřizování uživatelů Azure AD. Informace o význam této služby, jak to funguje a nejčastější dotazy, naleznete v tématu [automatizace zřizování uživatelů a jeho rušení pro software-as-a-service (SaaS) aplikací pomocí Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte:

*   Tenanta Azure AD.
*   A [tenanta Tableau Online](https://www.tableau.com/).
*   Účet uživatele s oprávněními správce v Tableau Online.

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Toto rozhraní API je k dispozici pro vývojáře v Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Přidat Tableau Online na webu Azure Marketplace
Než začnete konfigurovat Tableau Online pro automatické zřizování uživatelů pomocí Azure AD, přidejte si Tableau Online z Azure Marketplace na seznam spravovaných aplikací SaaS.

Chcete-li přidat Tableau Online z webu Marketplace, postupujte takto.

1. V [webu Azure portal](https://portal.azure.com), v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau Online** a vyberte **Tableau Online** na panelu výsledků. Chcete-li přidat aplikaci, **přidat**.

    ![Tableau Online v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Přiřazení uživatelů k Tableau Online

Azure Active Directory používá koncept volá *přiřazení* určit, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Než začnete konfigurovat a povolit automatické zřizování uživatelů, rozhodněte, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k Tableau Online. Přiřadit těmto uživatelům nebo skupinám Tableau Online, postupujte podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Důležité tipy pro přiřazování uživatelů k Tableau Online

*   Doporučujeme vám, přiřadit jeden uživatele Azure AD k Tableau Online k otestování automatické konfigurace zřizování uživatelů. Můžete přiřadit dalším uživatelům nebo skupinám později.

*   Když přiřadíte uživatele k Tableau Online, vyberte libovolný platný role specifické pro aplikaci, pokud je k dispozici, v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurovat automatické zřizování uživatelů pro Tableau Online

Tato část vás provede kroky konfigurace zřizování služby Azure AD. Umožňuje vytvářet, aktualizovat a zakázat uživatele nebo skupiny v Tableau Online na základě uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Také můžete povolit na základě SAML jednotného přihlašování pro Tableau Online. Postupujte podle pokynů [Tableau jednotné přihlašování – kurz Online](tableauonline-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurovat automatické zřizování uživatelů pro Tableau Online ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace** > **všechny aplikace** > **Tableau Online**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Tableau Online**.

    ![Tableau Online odkaz v seznamu aplikací](common/all-applications.png)

3. Vyberte **zřizování** kartu.

    ![Tableau Online zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Tableau Online režim zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte doménu, uživatelské jméno správce, správce hesel a adresu URL vašeho účtu Tableau Online obsahu:

   * V **domény** pole, zadejte subdoménu podle kroku 6.

   * V **uživatelské jméno správce** pole, zadejte uživatelské jméno účtu správce ve svém Tenantovi Clarizen. Příklad: admin@contoso.com.

   * V **heslo správce** pole, zadejte heslo účtu správce, který odpovídá uživatelské jméno správce.

   * V **adresa URL obsahu** pole, zadejte subdoménu podle kroku 6.

6. Po přihlášení k účtu pro správu pro Tableau Online, můžete získat hodnoty **domény** a **adresa URL obsahu** z adresy URL stránky pro správu.

    * **Domény** pro Tableau Online účtu lze kopírovat z část této adresy URL:

        ![Tableau Online domény](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * **Adresa URL obsahu** pro Tableau Online účtu lze kopírovat z této části. Je hodnota, která je definována během nastavení účtu. V tomto příkladu je hodnota "contoso":

        ![Tableau adresa URL obsahu Online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Vaše **domény** může lišit od znázorněno zde.

7. Po vyplnění polí uvedené v kroku 5, vyberte **Test připojení** abyste měli jistotu, že Azure AD můžete připojit ke službě Tableau Online. Pokud se nepovede, ujistěte se, že Tableau Online účet má oprávnění správce a zkuste to znovu.

    ![Tableau Online Test připojení](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu osoby nebo skupiny, ke zřizování chyba oznámení vůbec dostat. Vyberte **odeslání e-mailové oznámení, když dojde k selhání** zaškrtávací políčko.

    ![Tableau Online oznamovacího e-mailu](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Tableau**.

    ![Synchronizace uživatelů Online tableau](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Tableau Online v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Tableau Online pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Atributy uživatele Online odpovídající tableau](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Tableau**.

    ![Tableau Online skupiny synchronizace](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Zkontrolujte skupiny atributů, které jsou synchronizovány ze služby Azure AD k Tableau Online v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Tableau Online pro operace update. Chcete-li uložit všechny změny, vyberte **Uložit**.

    ![Tableau Online odpovídající skupinu atributů](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Konfigurace filtrů oborů, postupujte podle pokynů [oboru filtru kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Povolit zřizování služby pro Tableau Online v Azure AD **nastavení** oddíl, změna **stavu zřizování** k **na**.

    ![Tableau Online stav zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definujte uživatele nebo skupiny, které chcete ke zřízení Tableau online. V **nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Tableau Online oboru](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Jakmile budete připraveni ke zřízení, vyberte **Uložit**.

    ![Tableau Online uložit](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupinám definovaným v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než novější se synchronizuje. Odehrávalo přibližně každých 40 minut za předpokladu, spustí služba zřizování Azure AD. 

Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na sestavu aktivit zřizování. Sestava popisuje všechny akce prováděné služba na Tableau Online zřizování Azure AD.

Informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
