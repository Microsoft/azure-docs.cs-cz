---
title: 'Kurz: Konfigurace Tableau online pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů k Tableauí online.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359200"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Kurz: Konfigurace Tableau online pro Automatické zřizování uživatelů

V tomto kurzu se dozvíte, jak provést Tableau online a Azure Active Directory (Azure AD) a nakonfigurovat službu Azure AD tak, aby automaticky zřídila a zrušila zřizování uživatelů a skupin pro Tableau online.

> [!NOTE]
> Tento kurz popisuje konektor, který je založený na službě zřizování uživatelů Azure AD. Informace o tom, jak tato služba funguje, jak funguje a nejčastější dotazy, najdete v tématu [Automatizace zřizování a rušení zřizování uživatelů pro aplikace typu software jako služba (SaaS) s Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že máte následující:

*   Tenanta Azure AD.
*   [Online tenant Tableau](https://www.tableau.com/).
*   Uživatelský účet v Tableau online s oprávněními správce.

> [!NOTE]
> Integrace zřizování Azure AD spoléhá na [rozhraní Tableau online REST API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Toto rozhraní API je k dispozici pro Tableau vývojářů online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Přidat Tableau online z Azure Marketplace
Před konfigurací Tableau online pro Automatické zřizování uživatelů pomocí Azure AD přidejte Tableau online z Azure Marketplace do seznamu spravovaných aplikací SaaS.

Pokud chcete na webu Marketplace přidat Tableau online, postupujte podle těchto kroků.

1. V [Azure Portal](https://portal.azure.com)v navigačním podokně na levé straně vyberte **Azure Active Directory**.

    ![Ikona Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte v horní části dialogového okna možnost **Nová aplikace** .

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau online** a na panelu výsledků vyberte **Tableau online** . Chcete-li přidat aplikaci, vyberte možnost **Přidat**.

    ![Tableau online v seznamu výsledků](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Přiřadit uživatele k Tableau online

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé nebo skupiny, které jsou přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, rozhodněte se, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Tableau online. Pokud chcete přiřadit tyto uživatele nebo skupiny k Tableau online, postupujte podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Důležité tipy pro přiřazení uživatelů k Tableau online

*   Pro otestování automatické konfigurace zřizování uživatelů Doporučujeme, abyste přiřadili jednoho uživatele Azure AD k Tableau online. Později můžete přiřadit další uživatele nebo skupiny.

*   Když přiřadíte uživatele k Tableau online, vyberte libovolnou platnou roli specifickou pro aplikaci, pokud je k dispozici v dialogovém okně přiřazení. Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Konfigurace automatického zřizování uživatelů pro Tableau online

Tato část vás provede postupem konfigurace služby zřizování Azure AD. Slouží k vytváření, aktualizaci a zakázání uživatelů nebo skupin v Tableau online na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete také povolit jednotné přihlašování založené na SAML pro Tableau online. Postupujte podle pokynů v [kurzu Tableau online jednotného přihlašování](tableauonline-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když tyto dvě funkce doplňují sebe.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Tableau online ve službě Azure AD

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **podnikové aplikace**  >  **všechny aplikace**  >  **Tableau online**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Tableau online**.

    ![Online odkaz Tableau v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Online zřizování Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Tableau režim zřizování online](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** zadejte doménu, uživatelské jméno správce, heslo správce a adresu URL obsahu vašeho online účtu Tableau:

   * V poli **doména** vyplňte subdoménu na základě kroku 6.

   * V poli **uživatelské jméno správce** Vyplňte uživatelské jméno účtu správce v online tenantovi Tableau. Příklad: admin@contoso.com.

   * Do pole **heslo správce** zadejte heslo účtu správce, který odpovídá uživatelskému jménu správce.

   * V poli **Adresa URL obsahu** vyplňte subdoménu na základě kroku 6.

6. Až se přihlásíte ke svému účtu správce pro Tableau online, můžete získat hodnoty pro **adresu URL** **domény** a obsahu z adresy URL stránky pro správu.

    * **Doménu** pro online účet Tableau můžete zkopírovat z této části adresy URL:

        ![Online doména Tableau](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * Z této části se dá zkopírovat **Adresa URL obsahu** pro online účet Tableau. Je to hodnota, která je definována při nastavení účtu. V tomto příkladu je hodnota "contoso":

        ![Adresa URL online obsahu Tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Vaše **doména** se může lišit od tu, kterou tady vidíte.

7. Po vyplnění polí zobrazených v kroku 5 vyberte **Test připojení** , abyste se ujistili, že se služba Azure AD může připojit k Tableau online. Pokud se připojení nepovede, ujistěte se, že váš účet Tableau online má oprávnění správce, a zkuste to znovu.

    ![Tableau testovací připojení online](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, ve které se budou zobrazovat oznámení o chybách zřizování. Zaškrtněte políčko **Odeslat e-mailové oznámení, když dojde k chybě** .

    ![E-mail s online oznámením Tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Vyberte **Uložit**.

10. V části **mapování** vyberte **synchronizovat Azure Active Directory uživatelé Tableau**.

    ![Online synchronizace uživatelů Tableau](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Zkontrolujte atributy uživatelů synchronizované z Azure AD a Tableau online v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Tableau online pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Tableau online, které odpovídají atributy uživatele](./media/tableau-online-provisioning-tutorial/attribute.png)

12. V části **mapování** vyberte **synchronizovat Azure Active Directory skupiny do Tableau**.

    ![Synchronizace online skupin Tableau](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Zkontrolujte atributy skupiny synchronizované z Azure AD a Tableau online v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Tableau online pro operace aktualizace. Chcete-li uložit všechny změny, vyberte možnost **Uložit**.

    ![Tableau online atributy skupiny pro porovnání](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Pokud chcete nakonfigurovat filtry oborů, postupujte podle pokynů v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Pokud chcete povolit službu Azure AD Provisioning pro Tableau online, v části **Nastavení** změňte **stav zřizování** na **zapnuto**.

    ![Tableau stav zřizování online](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Definujte uživatele nebo skupiny, které chcete zřídit pro Tableau online. V části **Nastavení** vyberte hodnoty, které chcete v **oboru**.

    ![Online obor Tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Až budete připraveni zřídit, vyberte **Uložit**.

    ![Online uložení Tableau](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než pozdější synchronizace. Vyskytují se přibližně každých 40 minut, pokud se služba zřizování Azure AD spouští. 

V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivity zřizování. Tato sestava popisuje všechny akce prováděné službou zřizování Azure AD na Tableau online.

Informace o tom, jak číst protokoly zřizování služby Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Protokol změn
* 09/30/2020 – byla přidána podpora atributu "authSetting" pro uživatele.

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png