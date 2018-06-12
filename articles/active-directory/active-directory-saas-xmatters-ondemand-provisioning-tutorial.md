---
title: 'Kurz: Konfigurace xMatters OnDemand pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do xMatters OnDemand.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: v-ant-msft
ms.openlocfilehash: 52fe23f5353a68e3afbac6a91955a61c7b8fb44c
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292666"
---
# <a name="tutorial-configure-xmatters-ondemand-for-automatic-user-provisioning"></a>Kurz: Konfigurace xMatters OnDemand pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují postup provést v xMatters OnDemand a Azure Active Directory (Azure AD) ke konfiguraci Azure AD a automaticky zřizovat a zrušte zřízení uživatelů nebo skupin k xMatters OnDemand.

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující:

*   Klient služby Azure AD
*   Klienta OnDemand xMatters s [Starter](https://www.xmatters.com/pricing) plánu nebo lépe povoleno 
*   Uživatelský účet v xMatters OnDemand s oprávněními správce

> [!NOTE]
> Azure AD zřizování integrace spoléhá na [xMatters OnDemand API](https://help.xmatters.com/xmAPI), který je k dispozici pro týmy OnDemand xMatters Starter plánu nebo lepší.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Přidání xMatters OnDemand z Galerie

Před konfigurací xMatters OnDemand pro automatické zřizování s Azure AD uživatelů, je nutné přidat xMatters OnDemand si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Pokud chcete přidat xMatters OnDemand v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat xMatters OnDemand, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **xMatters OnDemand**.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **xMatters OnDemand**a pak klikněte na tlačítko **přidat** tlačítko xMatters OnDemand přidáte do seznamu aplikací SaaS.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppSearchResults.png)

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-xmatters-ondemand"></a>Přiřazení uživatelů k xMatters OnDemand

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k xMatters OnDemand. Jakmile se rozhodli, lze přiřadit tyto uživatele nebo skupiny xMatters OnDemand podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-xmatters-ondemand"></a>Důležité tipy pro přiřazování uživatelů do xMatters OnDemand

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke xMatters OnDemand a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování uživatele xMatters OnDemand, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-xmatters-ondemand"></a>Konfigurace automatického uživatele zajišťování, které xMatters OnDemand 

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázání uživatele nebo skupiny v xMatters OnDemand založené na uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Můžete také povolit na základě SAML jednotné přihlašování pro xMatters OnDemand, postupujte podle pokynů uvedených v [xMatters OnDemand jednotné přihlašování v kurzu](active-directory-saas-xmatters-ondemand-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na uživatele automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-automatic-user-provisioning-for-xmatters-ondemand-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro xMatters OnDemand ve službě Azure AD:

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS xMatters OnDemand.
 
    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ApplicationInstanceSearch.png)

3. Vyberte **zřizování** kartě.
    
    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** části, zadejte **uživatelské jméno správce**, **heslo správce**, a **domény** z vaší systém xMatters OnDemand účet.

    *   V **uživatelské jméno správce** pole, naplnit uživatelské jméno účtu správce na klientovi xMatters OnDemand. Příklad: admin@contoso.com.

    *   V **heslo správce** pole, naplnit heslo odpovídající uživatelské jméno správce.

    *   V **domény** pole, naplnit subdoméně tenanta xMatters OnDemand.
    Příklad: pro účet s adresu URL klienta https://my-tenant.xmatters.com, bude vaše subdomény **Moje klienta**.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k xMatters OnDemand. Pokud připojení nezdaří, zkontrolujte vaši xMatters OnDemand účet má oprávnění správce a zkuste to znovu.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/TestConnection.png)
    
7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtnutím políčka - **odeslat e-mailové oznámení, když dojde k selhání**.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/EmailNotification.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům xMatters OnDemand**.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncUsers.png)

10. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD xMatters OnDemand v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti jsou slouží k přiřazení uživatelských účtů ve xMatters OnDemand pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/UserAttributes.png)

11. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k xMatters OnDemand**.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SyncGroups.png)

12. Zkontrolujte skupiny atributy, které jsou synchronizované z Azure AD xMatters OnDemand v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly skupin v xMatters OnDemand pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/GroupAttributes.png)

13. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](./active-directory-saas-scoping-filters.md).

14. Povolit zřizování služby pro xMatters OnDemand Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ProvisioningStatus.png)

15. Zadejte uživatele nebo skupiny, které chcete zřídit na xMatters OnDemand výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/ScopeSelection.png)

16. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![xMatters OnDemand zřizování](./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné službou Azure AD zřizování služby na xMatters OnDemand.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-xmatters-ondemand-provisioning-tutorial/tutorial_general_03.png
