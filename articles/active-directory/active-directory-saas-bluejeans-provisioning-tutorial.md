---
title: 'Kurz: Konfigurace BlueJeans pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.openlocfilehash: 522d94e2a6fbd8e7eb32941e2cab59de7b28384c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Kurz: Konfigurace BlueJeans pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují postup provést v BlueJeans a Azure Active Directory (Azure AD) konfigurovat Azure AD a automaticky zřizovat a zrušte zřízení uživatelů nebo skupin k BlueJeans.

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující:

*   Klient služby Azure AD
*   BlueJeans klienta s [Moje společnost](https://www.BlueJeans.com/pricing) plánu nebo lépe povoleno
*   Uživatelský účet v BlueJeans s oprávněními správce

> [!NOTE]
> Azure AD zřizování integrace spoléhá na [BlueJeans API](https://BlueJeans.github.io/developer), který je k dispozici pro týmy BlueJeans na plán Standard nebo vyšší.

## <a name="adding-bluejeans-from-the-gallery"></a>Přidání BlueJeans z Galerie
Před konfigurací BlueJeans pro zřizování automatické uživatelů s Azure AD, je nutné přidat BlueJeans si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Pokud chcete přidat BlueJeans v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat BlueJeans, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **BlueJeans**.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. Na panelu výsledků vyberte **BlueJeans**a pak klikněte na tlačítko **přidat** tlačítko BlueJeans přidáte do seznamu aplikací SaaS.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Přiřazení uživatelů k BlueJeans

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k BlueJeans. Jakmile se rozhodli, lze přiřadit tyto uživatele nebo skupiny BlueJeans podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Důležité tipy pro přiřazování uživatelů do BlueJeans

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke BlueJeans a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování BlueJeans uživatele, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfiguraci zřizování automatické uživatelů k BlueJeans

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele nebo skupiny v BlueJeans podle uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Můžete také povolit na základě SAML jednotné přihlašování pro BlueJeans, postupujte podle pokynů uvedených v [BlueJeans jeden přihlašování kurzu](active-directory-saas-bluejeans-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na uživatele automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro BlueJeans ve službě Azure AD:

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte BlueJeans ze seznamu aplikací SaaS.
 
    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Vyberte **zřizování** kartě.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/Bluejeans1.png)

5. V části **přihlašovací údaje správce** části, zadejte **uživatelské jméno správce**, a **heslo správce** účtu BlueJeans. Příkladem tyto hodnoty jsou:

    *   V **uživatelské jméno správce** pole, uživatelské jméno účtu správce na klientovi BlueJeans naplnění. Příklad: admin@contoso.com.

    *   V **heslo správce** pole, naplnit heslo odpovídající uživatelské jméno správce.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k BlueJeans. Pokud se nepovede připojit, zajistěte, aby byl váš účet BlueJeans oprávnění správce a zkuste to znovu.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtnutím políčka - **odeslat e-mailové oznámení, když dojde k selhání**.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům BlueJeans**.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD BlueJeans v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v BlueJeans pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](./active-directory-saas-scoping-filters.md).

12. Povolit zřizování služby pro BlueJeans Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Zadejte uživatele nebo skupiny, které chcete zřídit na BlueJeans výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![BlueJeans zřizování](./media/active-directory-saas-bluejeans-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné při zřizování na BlueJeans služby Azure AD.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](./active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Bluejeans neumožňuje uživatelská jména, které delší než 30 znaků.

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](active-directory-enterprise-apps-manage-provisioning.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bluejeans-tutorial/tutorial_general_03.png
