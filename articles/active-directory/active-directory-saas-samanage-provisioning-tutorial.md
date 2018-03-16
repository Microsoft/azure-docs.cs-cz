---
title: "Kurz: Konfigurace Samanage pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs"
description: "Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Samanage."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8f11beff2c78386f4c3e8130c8e5d72465b8fe87
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace Samanage pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují postup provést v Samanage a Azure Active Directory (Azure AD) konfigurovat Azure AD a automaticky zřizovat a zrušte zřízení uživatelů nebo skupin k Samanage.

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující:

*   Klient služby Azure Active Directory
*   Samanage klienta s [Professional](https://www.samanage.com/pricing/) plánu nebo lépe povoleno 
*   Uživatelský účet v Samanage s oprávněními správce 

> [!NOTE]
> Azure AD zřizování integrace spoléhá na [Samanage REST API](https://www.samanage.com/api/), který je k dispozici pro Samanage týmy pro odborníky v oblasti plán nebo vyšší.

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z Galerie
Před konfigurací Samanage pro automatické zřizování s Azure AD uživatelů, je nutné přidat Samanage si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Pokud chcete přidat Samanage v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat Samanage, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Samanage**.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage4.png)

5. Na panelu výsledků vyberte **Samanage**a pak klikněte na tlačítko **přidat** tlačítko Samanage přidáte do seznamu aplikací SaaS.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage6.png)

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage5.png)

## <a name="assigning-users-to-samanage"></a>Přiřazení uživatelů k Samanage

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k Samanage. Jakmile se rozhodli, lze přiřadit tyto uživatele nebo skupiny Samanage podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Důležité tipy pro přiřazování uživatelů do Samanage

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Samanage a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování Samanage uživatele, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Konfiguraci zřizování automatické uživatelů k Samanage

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele nebo skupiny v Samanage podle uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Můžete také povolit na základě SAML jednotné přihlašování pro Samanage, postupujte podle pokynů uvedených v [Samanage jeden přihlašování kurzu](active-directory-saas-samanage-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na uživatele automatické zřizování, i když tyto dvě funkce doplnění navzájem. Další informace najdete v tématu [Samanage jeden přihlašování kurzu](active-directory-saas-samanage-tutorial.md).

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro Samanage ve službě Azure AD:

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte Samanage ze seznamu aplikací SaaS.
 
    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage7.png)

3. Vyberte **zřizování** kartě.
    
    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage8.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage9.png)

5. V části **přihlašovací údaje správce** části, zadejte **uživatelské jméno správce**, a **heslo správce** vaše Samanages účtu. Příkladem tyto hodnoty jsou:

    *   V **uživatelské jméno správce** pole, uživatelské jméno účtu správce na klientovi Samanage naplnění. Příklad: admin@contoso.com.

    *   V **heslo správce** pole, naplnit heslo odpovídající dříve zadané uživatelské jméno správce.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k Samanage. Pokud se nepovede připojit, zajistěte, aby byl váš účet Samanage oprávnění správce a zkuste to znovu.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage10.png)

7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtněte políčko **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage11.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Samanage**.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage12.png)

10. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD Samanage v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Samanage pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage13.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Samanage**.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage14.png)

13. Zkontrolujte skupiny atributy, které jsou synchronizované z Azure AD Samanage v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly skupin v Samanage pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage15.png)

14. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](./active-directory-saas-scoping-filters.md).

15. Povolit zřizování služby pro Samanage Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage16.png)

16. Zadejte uživatele nebo skupiny, které chcete zřídit na Samanage výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage17.png)

17. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Zřizování Samanage](./media/active-directory-saas-samanage-provisioning-tutorial/Samanage18.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné při zřizování na Samanage služby Azure AD.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](./active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](active-directory-enterprise-apps-manage-provisioning.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png
