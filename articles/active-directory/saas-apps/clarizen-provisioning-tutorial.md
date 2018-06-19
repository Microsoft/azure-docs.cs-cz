---
title: 'Kurz: Konfigurace Clarizen pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Clarizen.
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
ms.date: 01/31/2018
ms.author: v-ant-msft
ms.openlocfilehash: 794db2280f7db93781939b7d517c18fa82b61b96
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901612"
---
# <a name="tutorial-configure-clarizen-for-automatic-user-provisioning"></a>Kurz: Konfigurace Clarizen pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují postup provést v Clarizen a Azure Active Directory (Azure AD) konfigurovat Azure AD a automaticky zřizovat a zrušte zřízení uživatelů nebo skupin k Clarizen.

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující:

*   Klient služby Azure AD
*   Clarizen klienta s [Enterprise Edition](https://www.clarizen.com/product/pricing/) plánu nebo lépe povoleno 
*   Uživatelský účet v Clarizen s oprávněními správce

> [!NOTE]
> Azure AD zřizování integrace spoléhá na [Clarizen API](https://api.clarizen.com/v2.0/services/), který je k dispozici pro týmy Clarizen plánu Enterprise Edition nebo lepší.

## <a name="adding-clarizen-from-the-gallery"></a>Přidání Clarizen z Galerie
Před konfigurací Clarizen pro automatické zřizování s Azure AD uživatelů, je nutné přidat Clarizen si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Pokud chcete přidat Clarizen v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]
    
3. Chcete-li přidat Clarizen, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Clarizen**.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **Clarizen**a pak klikněte na tlačítko **přidat** tlačítko Clarizen přidáte do seznamu aplikací SaaS.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/AppSearchResults.png)

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-clarizen"></a>Přiřazení uživatelů k Clarizen

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k Clarizen. Jakmile se rozhodli, lze přiřadit tyto uživatele nebo skupiny Clarizen podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-clarizen"></a>Důležité tipy pro přiřazování uživatelů do Clarizen

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Clarizen a test automatického zřizování konfiguraci uživatelů. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování Clarizen uživatele, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-clarizen"></a>Konfiguraci zřizování automatické uživatelů k Clarizen 

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele nebo skupiny v Clarizen podle uživatele nebo skupiny přiřazení ve službě Azure AD.

> [!TIP]
> Můžete také povolit na základě SAML jednotné přihlašování pro Clarizen, postupujte podle pokynů uvedených v [Clarizen jeden přihlašování kurzu](clarizen-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na uživatele automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-automatic-user-provisioning-for-clarizen-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro Clarizen ve službě Azure AD:

1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte Clarizen ze seznamu aplikací SaaS.
 
    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/AppInstanceSearch.png)

3. Vyberte **zřizování** kartě.
    
    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** části, zadejte **domény**, **uživatelské jméno správce**, a **heslo správce** vaše Clarizen účtu. Příkladem tyto hodnoty jsou:

    *   V **uživatelské jméno správce** pole, uživatelské jméno účtu správce na klientovi Clarizen naplnění. Příklad: admin@contoso.com.

    *   V **heslo správce** pole, naplnit heslo účtu správce odpovídající uživatelské jméno správce.

    *   V **domény** pole, naplnit subdomény podle kroku 6.
    
6. Načtení **serverLocation** pro váš účet Clarizen podle kroků uvedených v části **ověřování** z [Clarizen na Rest API průvodce](https://success.clarizen.com/hc/en-us/articles/205711828-REST-API-Guide-Version-2). Při získávání serverLocation, použijte k naplnění subdoménu dané adresy URL, jak je znázorněno níže, **domény** pole.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/ClarizenDomain.png)

7. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k Clarizen. Pokud se nepovede připojit, zajistěte, aby byl váš účet Clarizen oprávnění správce a zkuste to znovu.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/TestConnection.png)
    
8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtněte políčko **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/NotificationEmail.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Clarizen**.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/UserMapping.png)

11. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD Clarizen v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Clarizen pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/UserMappingAttributes.png)

12. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Clarizen**.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/GroupMapping.png)

13. Zkontrolujte skupiny atributy, které jsou synchronizované z Azure AD Clarizen v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly skupin v Clarizen pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/GroupMappingAttributes.png)

14. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](./../active-directory-saas-scoping-filters.md).

15. Povolit zřizování služby pro Clarizen Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/ProvisioningStatus.png)

16. Zadejte uživatele nebo skupiny, které chcete zřídit na Clarizen výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/ScopeSelection.png)

17. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Zřizování Clarizen](./media/clarizen-provisioning-tutorial/SaveProvisioning.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné při zřizování na Clarizen služby Azure AD.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/clarizen-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
