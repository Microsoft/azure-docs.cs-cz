---
title: 'Kurz: Konfigurace tableau online pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a rozmisťovala uživatelské účty do Tableau Online.
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
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064205"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Kurz: Konfigurace tableau online pro automatické zřizování uživatelů

Tento kurz ukazuje kroky, které je třeba provést v Tableau Online a Azure Active Directory (Azure AD) pro konfiguraci Azure AD tak, aby automaticky zřizovala a odstavovala uživatele a skupiny do Tableau Online.

> [!NOTE]
> Tento kurz popisuje konektor, který je postavený na nad službou zřizování uživatelů Azure AD. Informace o tom, co tato služba dělá, jak funguje, a nejčastější dotazy naleznete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací masu jako služby (SaaS) pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte:

*   Tenanta Azure AD.
*   [Tenant Tableau Online](https://www.tableau.com/).
*   Uživatelský účet v Tableau Online s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD závisí na [tableau online rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Toto rozhraní API je k dispozici vývojářům Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Přidání Tableau Online z Azure Marketplace
Než nakonfigurujete Tableau Online pro automatické zřizování uživatelů pomocí Azure AD, přidejte Tableau Online z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete přidat Tableau Online z Marketplace, postupujte takto.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v navigačním podokně vlevo **službu Azure Active Directory**.

    ![Ikona služby Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace.**

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau Online** a z panelu výsledků vyberte **Tableau Online.** Chcete-li aplikaci přidat, vyberte **přidat**.

    ![Tableau Online v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Přiřazení uživatelů k Tableau Online

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů jsou synchronizováni pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů se rozhodněte, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Tableau Online. Chcete-li tyto uživatele nebo skupiny přiřadit k aplikaci Tableau Online, postupujte podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Důležité tipy pro přiřazení uživatelů do Tableau Online

*   Doporučujeme přiřadit jednoho uživatele Azure AD do Tableau Online k testování konfigurace automatického zřizování uživatelů. Později můžete přiřadit další uživatele nebo skupiny.

*   Když přiřadíte uživatele do tableau online, vyberte v dialogovém okně přiřazení libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurace automatického zřizování uživatelů do Tableau Online

Tato část vás provede kroky ke konfiguraci služby zřizování Azure AD. Použijte ji k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Tableau Online na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování saml pro Tableau Online. Postupujte podle pokynů v [Tableau Online jednotné přihlášení tutorial](tableauonline-tutorial.md). Jednotné přihlašování lze konfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce se vzájemně doplňují.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Tableau Online ve službě Azure AD

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace** > **Všechny aplikace** > **Tableau Online**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Tableau Online**.

    ![Odkaz Tableau Online v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Tableau Online Zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Tableau Online zřizování režimu](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **Pověření správce** zadejte doménu, uživatelské jméno správce, heslo správce a adresu URL obsahu účtu Tableau Online:

   * Do pole **Doména** vyplňte subdoménu založenou na kroku 6.

   * Do pole **Uživatelské jméno správce** vyplňte uživatelské jméno účtu správce ve svém klientském tenantovi Clarizen. Příklad: admin@contoso.com.

   * Do pole **Heslo správce** vyplňte heslo účtu správce, které odpovídá uživatelskému jménu správce.

   * V poli **Adresa URL obsahu** vyplňte subdoménu na základě kroku 6.

6. Po přihlášení ke svému účtu pro správu pro Tableau Online můžete získat hodnoty adresy **URL domény** a **obsahu** z adresy URL stránky správce.

    * **Doménu** vašeho účtu Tableau Online lze zkopírovat z této části adresy URL:

        ![Tableau Online doména](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Adresu **URL obsahu** vašeho účtu Tableau Online lze zkopírovat z této části. Je to hodnota, která je definována při nastavení účtu. V tomto příkladu je hodnota "contoso":

        ![Adresa URL obsahu tableau online](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Vaše **doména** se může lišit od domény uvedené zde.

7. Po vyplnění polí uvedených v kroku 5 vyberte **testovat připojení** a ujistěte se, že Azure AD můžete připojit k Tableau Online. Pokud se připojení nezdaří, ujistěte se, že váš účet Tableau Online má oprávnění správce a zkuste to znovu.

    ![Tableau Online Testovací připojení](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, aby se zjištovací oznámení o chybách zřizování zjištovací oznámení. Zaškrtněte **políčko Odeslat e-mailové oznámení, když dojde k chybě.**

    ![E-mail s oznámením na tableau online](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Vyberte **Uložit**.

10. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s tableau**.

    ![Tableau Online synchronizace uživatelů](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Tableau Online v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Tableau Online pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Tableau Online odpovídající uživatelské atributy](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure do tableau**.

    ![Synchronizace skupiny Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Tableau Online v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Tableau Online pro operace aktualizace. Chcete-li uložit změny, vyberte **uložit**.

    ![Tableau Online odpovídající skupiny atributy](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Chcete-li konfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Chcete-li povolit službu zřizování Azure AD pro Tableau Online, v části **Nastavení** změňte **stav zřizování** na **Zapnuto**.

    ![Tableau Stav online zřizování](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit do Tableau Online. V části **Nastavení** vyberte požadované hodnoty v **oboru**.

    ![Obor online tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Až budete připraveni k zřízení, vyberte **Uložit**.

    ![Tableau Online Uložit](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než pozdější synchronizace. Dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. 

Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivity zřizování. Sestava popisuje všechny akce prováděné službou zřizování Azure AD na Tableau Online.

Informace o tom, jak číst protokoly zřizování Azure AD, naleznete v [tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
