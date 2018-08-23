---
title: 'Kurz: Konfigurace Cisco Spark pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Cisco Spark.
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
ms.openlocfilehash: bb3b3061c15a661caff778ca5c0ec48b0434c718
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "42054219"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Kurz: Konfigurace Cisco Spark pro automatické zřizování uživatelů


Cílem tohoto kurzu je předvést postup provést v Cisco Spark a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatelů Cisco Spark.


> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

*   Klient služby Azure AD
*   Cisco Spark tenanta
*   Uživatelský účet ve Sparku Cisco s oprávněními správce


> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [webovou službu Sparku Cisco](https://developer.webex.com/getting-started.html), který je dostupný pro týmy Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Přidání Cisco Spark z Galerie
Před konfigurací Sparku Cisco pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Cisco Spark z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Přidání Cisco Spark z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]

3. Chcete-li přidat Cisco Spark, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Spark**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **Cisco Spark**a potom klikněte na tlačítko **přidat** tlačítko pro přidání Cisco Sparku do seznamu aplikací SaaS.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Přiřazování uživatelů k Cisco Spark

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatelé ve službě Azure AD potřebovat přístup ke Cisco Spark. Jakmile se rozhodli, můžete přiřadit tito uživatelé Cisco Spark podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Důležité tipy pro přiřazování uživatelů k Cisco Spark

*   Dále je doporučeno jednoho uživatele Azure AD je přiřazená Cisco Spark k otestování automatické konfigurace zřizování uživatelů. Další uživatele může být přiřazen později.

*   Při přiřazení uživatele k Cisco Sparku, je nutné vybrat žádné platné roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Konfigurace automatické zřizování uživatelů pro Cisco Spark

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele ve Sparku Cisco podle přiřazení uživatelů ve službě Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Cisco Spark ve službě Azure AD:


1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte ze seznamu aplikací SaaS Cisco Spark.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Vyberte **zřizování** kartu.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **adresy URL Tenanta**, a **tajný klíč tokenu** Cisco Spark účtu.

    *   V **adresy URL Tenanta** pole, vyplnit adresy URL Cisco Spark SCIM rozhraní API pro vašeho tenanta, který má formu `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, kde `[Tenant ID]` je alfanumerický řetězec, jak je popsáno v kroku 6.

    *   V **tajný klíč tokenu** pole, vyplňte tajný klíč tokenu, jak je popsáno v kroku 6.

1. **ID Tenanta** a **tajný klíč tokenu** pro Cisco Spark účtu najdete po přihlášení na [webu pro vývojáře Cisco Spark](https://developer.webex.com/) pomocí účtu správce. Po přihlášení -
    * Přejděte [stránka Začínáme](https://developer.webex.com/getting-started.html)
    * Přejděte dolů k položce [oddíl Authentication](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Spark ověřovací Token](./media/cisco-spark-provisioning-tutorial/SecretToken.png)
    * Alfanumerický řetězec v poli je vaše **tajný klíč tokenu**. Zkopírujte tento token do schránky
    * Přejděte [získat Moje vlastní stránka s podrobnostmi o](https://developer.webex.com/endpoint-people-me-get.html)
        * Zajistěte, aby byl Test režimu ON
        * Zadejte slovo "Nosiče" následované mezerou a vložte Token tajného klíče do pole autorizace ![Cisco Spark ověřovací Token](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png)
        * Klikněte na tlačítko Spustit
    * V textu odpovědi na pravé straně **ID Tenanta** se zobrazí jako "orgId":

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

1. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Cisco Spark. Pokud se nepovede, ujistěte se, že váš účet Cisco Spark má oprávnění správce a zkuste to znovu.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Klikněte na **Uložit**.

10. V části **mapování** vyberte **synchronizace Azure Active Directory Users Cisco Spark**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD k Cisco Sparku v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty ve Sparku Cisco pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../active-directory-saas-scoping-filters.md).

13. Služba pro Cisco Spark zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Definovat uživatele a/nebo skupiny, které chcete ke zřízení Cisco Spark výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Spark Cisco zřizování](./media/cisco-spark-provisioning-tutorial/Save.png)


Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba ve Sparku Cisco zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Cisco Spark je momentálně ve fázi včasného testování pole (EFT) Cisco. Další informace, obraťte se prosím na [tým podpory Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
