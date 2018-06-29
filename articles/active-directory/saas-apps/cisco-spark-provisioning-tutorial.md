---
title: 'Kurz: Konfigurace Cisco Spark pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Cisco Spark.
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
ms.openlocfilehash: 74907693270e6cd340d3b34585a80077aa87f0f7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37058895"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Kurz: Konfigurace Cisco Spark pro zřizování automatické uživatelů


Cílem tohoto kurzu je ukazují postup provést v Cisco Spark a Azure Active Directory (Azure AD) ke konfiguraci Azure AD a automaticky zřizovat a zrušte zřídit uživatelům Cisco Spark.


> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénáři uvedeném v tomto kurzu se předpokládá, že už máte následující požadavky:

*   Klient služby Azure AD
*   Cisco Spark klienta
*   Uživatelský účet v Cisco Spark s oprávněními správce


> [!NOTE]
> Azure AD zřizování integrace spoléhá na [Cisco Spark Webservice](https://developer.webex.com/getting-started.html), která je k dispozici do týmů Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Přidání Cisco Spark z Galerie
Před konfigurací Cisco Spark pro zřizování automatické uživatelů s Azure AD, je nutné přidat Cisco Spark v galerii aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Cisco Spark v galerii aplikací Azure AD, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, na levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které** > **všechny aplikace**.

    ![Podnikové aplikace části][2]

3. Chcete-li přidat Cisco Spark, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Spark**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **Cisco Spark**a pak klikněte na tlačítko **přidat** tlačítko Cisco Spark přidáte do seznamu aplikací SaaS.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Přiřazení uživatelů k Cisco Spark

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování automatické uživatelů, byste měli rozhodnout, které uživatelé ve službě Azure AD potřebovat přístup ke Cisco Spark. Jakmile se rozhodli, můžete přiřadit těmto uživatelům Cisco Spark podle pokynů tady:

*   [Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Důležité tipy pro přiřazování uživatelů do Cisco Spark

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Cisco Spark a test automatického zřizování konfiguraci uživatelů. Další uživatelé mohou přiřadit později.

*   Při přiřazování uživatele Cisco Spark, je třeba vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučeny z zřizování.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Konfiguraci zřizování automatické uživatelů k Cisco Spark

Tato část vás provede kroky pro konfiguraci Azure AD zřizování služby vytvářet, aktualizovat a zakázat uživatele v Cisco Spark na základě uživatele přiřazení ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Konfigurace automatického uživatele zřizování pro Cisco Spark v Azure AD:


1. Přihlaste se k [portál Azure](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace, které > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS Cisco Spark.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Vyberte **zřizování** kartě.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režimu zřizování** k **automatické**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovací údaje správce** části, zadejte **URL klienta**, a **tajný klíč tokenu** Cisco Spark účtu.

    *   V **URL klienta** pole, naplnit adresy URL Cisco Spark SCIM rozhraní API pro vašeho tenanta, který má formu `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, kde `[Tenant ID]` je alfanumerický řetězec, jak je popsáno v kroku 6.

    *   V **tajný klíč tokenu** pole, naplnit tajný klíč tokenu, jak je popsáno v kroku 6.

1. **ID klienta** a **tajný klíč tokenu** pro vaše Cisco Spark účet najdete po přihlášení na [Cisco Spark vývojáře lokality](https://developer.webex.com/) pomocí vašeho účtu správce. Po přihlášení -
    * Přejděte na [stránku Začínáme](https://developer.webex.com/getting-started.html)
    * Přejděte dolů k položce [část ověřování](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Spark ověřování tokenu](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
    * Alfanumerický řetězec v poli je vaše **tajný klíč tokenu**. Zkopírujte tento token do schránky
    * Přejděte na [získat Moje vlastní stránce s podrobnostmi o](https://developer.webex.com/endpoint-people-me-get.html)
        * Ujistěte se, že testovací režimu je ON
        * Zadejte slovo "Nosiče" následované mezerou a potom vložte do pole autorizace tajný klíč tokenu ![Cisco Spark ověřování tokenu](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
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

1. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k Cisco Spark. Pokud se nepovede připojit, zajistěte, aby byl váš účet Cisco Spark oprávnění správce a zkuste to znovu.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování chyba a zaškrtnutím políčka - **odeslat e-mailové oznámení, když dojde k selhání**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Cisco Spark**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Zkontrolujte uživatelské atributy, které jsou synchronizované z Azure AD Cisco Spark v **mapování atributů** části. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Cisco Spark pro operace aktualizace. Vyberte **Uložit** tlačítko potvrzení změny.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Konfigurace oboru filtrů, použijte následující pokyny uvedené v [Scoping filtru kurzu](../active-directory-saas-scoping-filters.md).

13. Povolit Azure AD zřizování služby pro Cisco Spark, změňte **Stav zřizování** k **na** v **nastavení** části.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Zadejte uživatele nebo skupiny, které byste chtěli účelem zřízení Cisco Spark výběrem požadované hodnoty v **oboru** v **nastavení** části.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Až budete připravení zřizování, klikněte na tlačítko **Uložit**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/Save.png)


Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupiny definované v **oboru** v **nastavení** části. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení aktivity zprávu, která popisuje všechny akce prováděné službou Azure AD zřizování služby na Cisco Spark.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další postup

* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png