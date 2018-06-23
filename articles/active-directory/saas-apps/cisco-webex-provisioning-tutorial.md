---
title: 'Kurz: Konfigurace Cisco pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: fdaf77e3d8a1858372298fb0d67ca05c2717adf6
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321147"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Kurz: Konfigurace Cisco Webex pro zřizování automatické uživatelů


Cílem tohoto kurzu je ukazují postup provést v Cisco Webex a Azure Active Directory (Azure AD) ke konfiguraci Azure AD a automaticky zřizovat a zrušte zřídit uživatelům Cisco Webex.


> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénáři uvedeném v tomto kurzu se předpokládá, že už máte následující požadavky:

*   Klient služby Azure AD
*   Klient Cisco Webex
*   Uživatelský účet v Cisco Webex s oprávněními správce


> [!NOTE]
> Azure AD zřizování integrace spoléhá na [Cisco Webex Webservice](https://developer.webex.com/getting-started.html), což je k dispozici pro Cisco Webex týmy.

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie
Před konfigurací Cisco Webex pro zřizování automatické uživatelů s Azure AD, je nutné přidat Cisco Webex si na seznam spravovaných aplikací SaaS v galerii aplikací Azure AD.

**Chcete-li přidat Cisco Webex v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]

3. Chcete-li přidat Cisco Webex, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Webex**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **Cisco Webex**a potom klikněte na **přidat** tlačítko Cisco Webex přidáte do seznamu aplikací SaaS.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Přiřazení uživatelů k Cisco Webex

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování automatické uživatelů, byste měli rozhodnout, které uživatelé ve službě Azure AD potřebovat přístup ke Cisco Webex. Jakmile se rozhodli, můžete přiřadit těmto uživatelům Cisco Webex podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Důležité tipy pro přiřazování uživatelů do Cisco Webex

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Cisco Webex a test automatického zřizování konfiguraci uživatelů. Další uživatelé mohou přiřadit později.

*   Při přiřazování uživatele Cisco Webex, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurace automatického uživatele zajišťování, které Cisco Webex

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele v Cisco Webex na základě uživatele přiřazení ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro Cisco Webex ve službě Azure AD:


1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS Cisco Webex.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Vyberte **zřizování** kartě.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** části, zadejte **URL klienta**, a **tajný klíč tokenu** vaše Cisco Webex účtu.

    *   V **URL klienta** pole, naplnit adresy URL Cisco Webex SCIM rozhraní API pro vašeho tenanta, který má formu `https://api.webex.com/v1/scim/[Tenant ID]/`, kde `[Tenant ID]` je alfanumerický řetězec, jak je popsáno v kroku 6.

    *   V **tajný klíč tokenu** pole, naplnit tajný klíč tokenu, jak je popsáno v kroku 6.

1. **ID klienta** a **tajný klíč tokenu** pro vaše Cisco Webex účet najdete po přihlášení na [Cisco Webex vývojáře lokality](https://developer.webex.com/) pomocí vašeho účtu správce. Po přihlášení -
    * Přejděte na [stránku Začínáme](https://developer.webex.com/getting-started.html)
    * Přejděte dolů k položce [část ověřování](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Webex ověřování tokenu](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * Alfanumerický řetězec v poli je vaše **tajný klíč tokenu**. Zkopírujte tento token do schránky
    * Přejděte na [získat Moje vlastní stránce s podrobnostmi o](https://developer.webex.com/endpoint-people-me-get.html)
        * Ujistěte se, že testovací režimu je ON
        * Zadejte slovo "Nosiče" následované mezerou a potom vložte do pole autorizace tajný klíč tokenu ![Cisco Webex ověřování tokenu](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Klikněte na tlačítko Spustit
    * V textu odpovědi na pravé straně **ID klienta** se zobrazí jako "orgId":

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k Cisco Webex. Pokud se nepovede připojit, zajistěte, aby byl váš účet Cisco Webex oprávnění správce a zkuste to znovu.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtnutím políčka - **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Cisco Webex**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD Cisco Webex v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Cisco Webex pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](../active-directory-saas-scoping-filters.md).

13. Povolit zřizování služby pro Cisco Webex Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Zadejte uživatele nebo skupiny, které byste chtěli účelem zřízení Cisco Webex výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Zřizování Webex Cisco](./media/cisco-webex-provisioning-tutorial/Save.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné službou Azure AD zřizování služby na Cisco Webex.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png