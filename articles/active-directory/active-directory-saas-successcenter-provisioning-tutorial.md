---
title: "Kurz: Konfigurace základním kamenem OnDemand pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs"
description: "Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do základním kamenem OnDemand."
services: active-directory
documentationcenter: 
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: v-ant
ms.openlocfilehash: 8aed9bdcdd54f2f0ef3cd81f979635e5716f0b5f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Kurz: Konfigurace základním kamenem OnDemand pro zřizování automatické uživatelů


Cílem tohoto kurzu je ukazují postup provést v základním kamenem OnDemand a Azure Active Directory (Azure AD) ke konfiguraci Azure AD a automaticky zřizovat a zrušte zřízení uživatelů nebo skupin k základním kamenem OnDemand.


> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující:

*   Klient služby Azure AD
*   Základním kamenem OnDemand klienta
*   Uživatelský účet v základním kamenem OnDemand s oprávněními správce


> [!NOTE]
> Azure AD zřizování integrace spoléhá na [základním kamenem OnDemand Webservice](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf), která je k dispozici na základním kamenem OnDemand týmy.

## <a name="adding-cornerstone-ondemand-from-the-gallery"></a>Přidání základním kamenem OnDemand z Galerie
Před konfigurací základním kamenem OnDemand pro zřizování automatické uživatelů s Azure AD, je nutné přidat základním kamenem OnDemand si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Chcete-li přidat základním kamenem OnDemand v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat základním kamenem OnDemand, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **základním kamenem OnDemand**.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **základním kamenem OnDemand**a pak klikněte na tlačítko **přidat** tlačítko základním kamenem OnDemand přidáte do seznamu aplikací SaaS.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/AppSearchResults.png)

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cornerstone-ondemand"></a>Přiřazení uživatelů k základním kamenem OnDemand

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k základním kamenem OnDemand. Jakmile se rozhodli, můžete přiřadit tyto uživatele nebo skupiny k základním kamenem OnDemand podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Důležité tipy pro přiřazení uživatele k základním kamenem OnDemand

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke základním kamenem OnDemand a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k základním kamenem OnDemand, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfiguraci zřizování automatické uživatelů k základním kamenem OnDemand

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázání uživatele nebo skupiny v základním kamenem OnDemand podle uživatele nebo skupiny přiřazení ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cornerstone-ondemand-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro základním kamenem OnDemand ve službě Azure AD:


1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS základním kamenem OnDemand.
 
    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/Successcenter2.png)

3. Vyberte **zřizování** kartě.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** části, zadejte **uživatelské jméno správce**, **heslo správce**, a **domény** z vaší základním kamenem OnDemand účet.

    *   V **uživatelské jméno správce** pole, naplnit uživatelské jméno účtu správce na klientovi základním kamenem OnDemand. Příklad: správce.

    *   V **heslo správce** pole, naplnit heslo odpovídající uživatelské jméno správce.

    *   V **domény** pole, adresa URL webové služby ze základním kamenem naplnění. Příklad: Se služba nachází v `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`pro doménu Contoso je `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k základním kamenem OnDemand. Pokud se nepovede připojit, zajistěte, aby byl váš účet základním kamenem OnDemand oprávnění správce a zkuste to znovu.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/TestConnection.png)

7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtněte políčko **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/EmailNotification.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům základním kamenem OnDemand**.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMapping.png)

10. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD základním kamenem OnDemand v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v základním kamenem OnDemand pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/UserMappingAttributes.png)

11. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](./active-directory-saas-scoping-filters.md).

12. Povolit zřizování služby pro základním kamenem OnDemand Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/ProvisioningStatus.png)

13. Zadejte uživatele nebo skupiny, které chcete zřídit na základním kamenem OnDemand výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/SyncScope.png)

14. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Základním kamenem OnDemand zřizování](./media/active-directory-saas-successcenter-provisioning-tutorial/Save.png) 


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné službou Azure AD zřizování služby v základním kamenem OnDemand.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](./active-directory-saas-provisioning-reporting.md).
## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](active-directory-enterprise-apps-manage-provisioning.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successcenter-provisioning-tutorial/tutorial_general_03.png
