---
title: 'Kurz: Konfigurace služby Zendesk pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do této služby.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 6ba2fd9ee81b8551cc2a267cdc9767f47fe27456
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229049"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Zendesk pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují postup provést v Zendesku a Azure Active Directory (Azure AD) konfigurovat Azure AD a automaticky zřizovat a zrušte zřídit uživatele nebo skupiny do této služby. 

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénáři uvedeném v tomto kurzu se předpokládá, že už máte následující požadavky:

*   Klient služby Azure AD
*   Klientovi služby Zendesk s [Enterprise](https://www.zendesk.com/product/pricing/) plánu nebo lépe povoleno 
*   Uživatelský účet v Zendesku se oprávnění správce 

> [!NOTE]
> Azure AD zřizování integrace spoléhá na [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction), který je k dispozici pro Zendesk týmy v plánu podnikového nebo lepší.

## <a name="adding-zendesk-from-the-gallery"></a>Přidání Zendesk z Galerie
Před konfigurací této služby pro automatické zřizování s Azure AD uživatelů, je nutné přidat Zendesk si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Chcete-li přidat Zendesk v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat Zendesk, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zendesk**.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk6.png)

5. Na panelu výsledků vyberte **Zendesk**a pak klikněte na tlačítko **přidat** tlačítko Zendesk přidáte do seznamu aplikací SaaS.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk7.png)

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk20.png)

## <a name="assigning-users-to-zendesk"></a>Přiřazování uživatelů do této služby

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k této služby. Jakmile se rozhodli, lze přiřadit tyto uživatele nebo skupiny služby Zendesk podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Důležité tipy pro přiřazování uživatelů do této služby

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Zendesku a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k této služby, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-zendesk"></a>Konfiguraci zřizování automatické uživatelů do této služby 

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele nebo skupiny v této služby na základě uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Můžete také povolit na základě SAML jednotné přihlašování pro Zendesk, postupujte podle pokynů uvedených v [Zendesk jeden přihlašování kurzu](zendesk-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na uživatele automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro Zendesk ve službě Azure AD:

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS Zendesk.
 
    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk3.png)

3. Vyberte **zřizování** kartě.
    
    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. V části **přihlašovací údaje správce** části, zadejte **uživatelské jméno správce**, **tajný klíč tokenu**, a **domény** vaše Zendesk účtu. Příkladem tyto hodnoty jsou:

    *   V **uživatelské jméno správce** pole, naplnit uživatelské jméno účtu správce na klienta služby Zendesk. Příklad: admin@contoso.com.

    *   V **tajný klíč tokenu** pole, naplnit tajný tokenu, jak je popsáno v kroku 6.

    *   V **domény** pole, naplnit subdoméně klienta služby Zendesk.
    Příklad: pro účet s adresu URL klienta https://my-tenant.zendesk.com, bude vaše subdomény **Moje klienta**.

6. **Tajný klíč tokenu** pro vaše Zendesk účet se nachází v **správce > API > Nastavení**. 

    ![Zřizování Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png) ![Zendesk zřizování](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k této služby. Pokud se nepovede připojit, ujistěte se, že má oprávnění správce vašeho účtu Zendesk a akci opakujte.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk19.png)
    
8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtnutím políčka - **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Zendesk**.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD Zendesk v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Zendesku pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory do služby ZenDesk**.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Zkontrolujte skupiny atributy, které jsou synchronizované z Azure AD Zendesk v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti jsou slouží k přiřazení skupiny v této služby pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](./../active-directory-saas-scoping-filters.md).

15. Povolit Azure AD zřizování služby pro služby Zendesk, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zadejte uživatele nebo skupiny, které byste chtěli účelem zřízení Zendesk výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Zřizování této služby](./media/zendesk-provisioning-tutorial/ZenDesk18.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné při zřizování služby v této služby Azure AD.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Omezení konektoru
* Zendesk podporuje použití skupin pro uživatele s pouze role agenta. Další informace naleznete v [Zendesk na dokumentaci](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
