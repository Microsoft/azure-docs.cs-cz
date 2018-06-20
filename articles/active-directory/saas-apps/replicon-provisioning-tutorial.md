---
title: 'Kurz: Konfigurace Replicon pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Replicon.
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
ms.date: 02/20/2018
ms.author: v-ant
ms.openlocfilehash: 0f71c449e2fb0ab8b26ee9ba68850cbf7fe03b28
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214742"
---
# <a name="tutorial-configure-replicon-for-automatic-user-provisioning"></a>Kurz: Konfigurace Replicon pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují postup provést v Replicon a Azure Active Directory (Azure AD) konfigurovat Azure AD a automaticky zřizovat a zrušte zřízení uživatelů nebo skupin k Replicon.

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klient služby Azure AD
*   Replicon klienta s [Plus](https://www.replicon.com/time-bill-pricing/) plánu nebo lépe povoleno
*   Uživatelský účet v Replicon s oprávněními správce

> [!NOTE]
> Azure AD zřizování integrace spoléhá na [Replicon API](https://www.replicon.com/help/developers), která je k dispozici do Replicon týmů na Plus plánu nebo vyšší.

## <a name="adding-replicon-from-the-gallery"></a>Přidání Replicon z Galerie
Před konfigurací Replicon pro zřizování automatické uživatelů s Azure AD, je nutné přidat Replicon si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Pokud chcete přidat Replicon v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat Replicon, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Replicon**.

    ![Hledání replicon aplikace](./media/replicon-provisioning-tutorial/RepliconAppSearch.png)

5. Na panelu výsledků vyberte **Replicon**a pak klikněte na tlačítko **přidat** tlačítko Replicon přidáte do seznamu aplikací SaaS.

    ![Výsledky hledání replicon](./media/replicon-provisioning-tutorial/RepliconAppSearchResults.png)

    ![Replicon vytvoření aplikace](./media/replicon-provisioning-tutorial/RepliconAppCreate.png)


## <a name="assigning-users-to-replicon"></a>Přiřazení uživatelů k Replicon

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k Replicon. Jakmile se rozhodli, lze přiřadit tyto uživatele nebo skupiny Replicon podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-replicon"></a>Důležité tipy pro přiřazování uživatelů do Replicon

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Replicon a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování Replicon uživatele, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-replicon"></a>Konfiguraci zřizování automatické uživatelů k Replicon 

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele nebo skupiny v Replicon podle uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Můžete také povolit na základě SAML jednotné přihlašování pro Replicon, postupujte podle pokynů uvedených v [Replicon jeden přihlašování kurzu](replicon-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na uživatele automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-automatic-user-provisioning-for-replicon-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro Replicon ve službě Azure AD:

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte Replicon ze seznamu aplikací SaaS.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/Replicon2.png)

3. Vyberte **zřizování** kartě.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/Replicon1.png)

5. V části **přihlašovací údaje správce** části, zadejte **uživatelské jméno správce**, **heslo správce**, **CompanyId**, a **domény**  vaše Replicon účtu. Příkladem tyto hodnoty jsou:

    *   V **uživatelské jméno správce** pole, uživatelské jméno účtu správce na klientovi Replicon naplnění. Příklad: contosoadmin.

    *   V **heslo správce** pole, naplnit heslo odpovídající uživatelské jméno správce.

    *   V **CompanyId** pole, naplnit ID společnosti klienta služby Replicon. Příklad: CompanyID podle přihlášení pod je Contoso.

    ![Replicon přihlášení](./media/replicon-provisioning-tutorial/RepliconLogin.png)

    *   V **domény** pole, naplnit doméně, jak je popsáno v kroku 6.
    
6. Získat **serviceEndpointRootURL** pro vaše Replicon klienta účet podle kroků v [pomůže Service Replicon](https://www.replicon.com/help/determining-the-url-for-your-service-calls). Při získávání adresu URL, **domény** subdoménou **serviceEndpointRootURL** jako zvýrazněné. 

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconEndpoint.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k Replicon. Pokud se nepovede připojit, zajistěte, aby byl váš účet Replicon oprávnění správce a zkuste to znovu.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconTestConnection.png)

8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtněte políčko **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconNotificationEmail.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Replicon**.
    
    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconUserMapping.png)

11. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD Replicon v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Replicon pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconUserMappingAtrributes.png)

12. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](../active-directory-saas-scoping-filters.md).

13. Povolit zřizování služby pro Replicon Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/RepliconProvisioningStatus.png)

14. Zadejte uživatele nebo skupiny, které chcete zřídit na Replicon výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/UserGroupSelection.png)

15. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Zřizování replicon](./media/replicon-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné při zřizování na Replicon služby Azure AD.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/replicon-tutorial/tutorial_general_01.png
[2]: ./media/replicon-tutorial/tutorial_general_02.png
[3]: ./media/replicon-tutorial/tutorial_general_03.png
