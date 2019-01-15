---
title: 'Kurz: Integrace Azure Active Directory se SAP Netweaverem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/07/2019
ms.author: jeedes
ms.openlocfilehash: 6bbe34cca1d5f3630f95c2748c852be24e5781d6
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54266158"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Kurz: Integrace Azure Active Directory se SAP Netweaverem

V tomto kurzu se dozvíte, jak integrovat SAP NetWeaver pomocí Azure Active Directory (Azure AD).
SAP NetWeaver integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAP Netweaveru.
* Uživatelům se automaticky přihlášeni k SAP NetWeaver (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP NetWeaver, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SAP NetWeaver jednotného přihlašování povolená předplatného
* SAP NetWeaver V7.20 vyžaduje alespoň

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* SAP NetWeaver podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-sap-netweaver-from-the-gallery"></a>Přidání SAP NetWeaver v galerii

Konfigurace integrace SAP NetWeaver do služby Azure AD, budete muset přidat SAP NetWeaver na váš seznam spravovaných aplikací SaaS z galerie.

**SAP NetWeaver přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP NetWeaver**vyberte **SAP NetWeaver** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![SAP NetWeaver v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí SAP NetWeaver na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému SAP NetWeaver.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SAP NetWeaver, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SAP NetWeaver Single Sign-On](#configure-sap-netweaver-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele SAP NetWeaver](#create-sap-netweaver-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP NetWeaver, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se SAP Netweaverem, proveďte následující kroky:

1. Otevřete nové okno webového prohlížeče a přihlaste na webu společnosti SAP NetWeaver jako správce

2. Ujistěte se, že **http** a **https** služby jsou aktivní a příslušné porty jsou přiřazeny v **SMICM** T kódu.

3. Přihlaste se na obchodní klienta systému SAP (T01), ve kterém jsou vyžadována jednotného přihlašování a aktivovat relace HTTP zabezpečení správy.

    a. Přejděte na kód transakce **SICF_SESSIONS**. Zobrazí všechny parametry příslušný profil pomocí aktuálních hodnot. Vypadat následovně:-
    ```
    login/create_sso2_ticket = 2
    login/accept_sso2_ticket = 1
    login/ticketcache_entries_max = 1000
    login/ticketcache_off = 0  login/ticket_only_by_https = 0 
    icf/set_HTTPonly_flag_on_cookies = 3
    icf/user_recheck = 0  http/security_session_timeout = 1800
    http/security_context_cache_size = 2500
    rdisp/plugin_auto_logout = 1800
    rdisp/autothtime = 60
    ```
    >[!NOTE]
    > Upravit nad parametry podle požadavků vaší organizace, nad parametry jsou uvedeny jako označení pouze zde.

    b. V případě potřeby upravte parametry v instanci/výchozí profil systému SAP a restartování systému SAP.

    c. Dvakrát klikněte na příslušné klientovi relace zabezpečení protokolu HTTP.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. Aktivujte si níže SICF služby:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. Přejděte na kód transakce **SAML2** v klientovi obchodní systému SAP [T01/122]. Otevře se uživatelské rozhraní v prohlížeči. V tomto příkladu jsme předpokládá, že 122 jako SAP business klienta.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. Zadejte uživatelské jméno a heslo zadejte v uživatelském rozhraní a klikněte na tlačítko **upravit**.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. Nahraďte **název zprostředkovatele** z T01122 k `http://T01122` a klikněte na **Uložit**.

    > [!NOTE]
    > Ve výchozím nastavení název zprostředkovatele pochází jako <sid> <client> formátu, ale Azure AD očekává název ve formátu <protocol>://<name>, chcete-li zachovat název zprostředkovatele jako https:// doporučenými<sid> <client> povolit více SAP Moduly NetWeaver ABAP nakonfigurovat ve službě Azure AD.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **Generování metadat zprostředkovatelů služby**: - Když jsme se vším hotovi s konfigurací **místního zprostředkovatele** a **důvěryhodných zprostředkovatelů** nastavení na SAML 2.0 uživatelské rozhraní, dalším krokem je Generovat soubor metadat poskytovatele služeb, (který bude obsahovat všechna nastavení, kontext ověřování a další konfigurace v SAP). Po vygenerování tohoto souboru musíte odeslat ho ve službě Azure AD.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. Přejděte na **karta místní zprostředkovatel**.

    b. Klikněte na **metadat**.

    c. Uložit generované **soubor metadat XML** ve vašem počítači a nahrajte ho v **základní konfiguraci SAML** oddíl automatické polulate **identifikátor** a  **Adresa URL pro odpověď** hodnoty na webu Azure portal.

8. V [webu Azure portal](https://portal.azure.com/)na **SAP NetWeaver** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

9. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

10. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

11. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat** k nahrání **soubor metadat poskytovatele služeb** které jste získali už dřív.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části textového pole, jak je znázorněno níže:

    ![SAP NetWeaver domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<your company instance of SAP NetWeaver>`

12. Aplikace SAP NetWeaver očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

13. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na tlačítko **ikonu pro úpravu** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/sapnetweaver-tutorial/nameidattribute.png)

    ![image](./media/sapnetweaver-tutorial/nameidattribute1.png)

    b. Z **transformace** seznamu vyberte **ExtractMailPrefix()**.

    c. Z **parametr 1** seznamu vyberte **user.userprinicipalname**.

    d. Klikněte na **Uložit**.

14. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

15. Na **nastavit SAP NetWeaver** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-sap-netweaver-single-sign-on"></a>Konfigurace SAP NetWeaver jednotné přihlašování

1. Přihlášení k systému SAP a přejít ke kódu transakce typu SAML2. Otevře se nové okno prohlížeče s obrazovku pro konfiguraci SAML.

2. Konfigurace koncových bodů pro důvěryhodného Identity zprostředkovatele (Azure AD) v části **důvěryhodných zprostředkovatelů** kartu.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

3. Stisknutím klávesy **přidat** a vyberte **nahrát soubor metadat** v místní nabídce.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

4. Nahrajte soubor metadat, který jste si stáhli z portálu Azure portal.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

5. Na další obrazovce zadejte název aliasu. Například aadsts a stiskněte klávesu **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

6. Ujistěte se, že vaše **algoritmus Digest** by měl být **SHA-256** a nevyžadují žádné změny a stiskněte klávesu **Další**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

7. Na **jednotné přihlašování – koncové body**, použijte **HTTP POST** a klikněte na tlačítko **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

8. Na **koncové body jednotné odhlašování** vyberte **HTTPRedirect** a klikněte na tlačítko **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

9. Na **koncové body artefaktů**, stiskněte klávesu **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

10. Na **požadavky na ověřování**, klikněte na tlačítko **Dokončit**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

11. Přejděte na kartu **důvěryhodného zprostředkovatele** > **federaci identit** (v dolní části obrazovky). Klikněte na **Upravit**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

12. Klikněte na tlačítko **přidat** pod **federaci identit** kartu (dolní části okna).

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

13. V automaticky otevíraném okně vyberte **nespecifikovaný** z **formáty podporované NameID** a klikněte na tlačítko OK.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

14. Všimněte si, že **uživatelské ID zdroje** a **režim mapování id uživatele** hodnoty určují propojení mezi uživatel systému SAP a deklarace identity Azure AD.  

    #### <a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scénář: Uživatel systému SAP pro mapování uživatele Azure AD.

    a. Snímek obrazovky podrobností NameID od SAPU.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Snímek obrazovky, že zmíníte požadované deklarace identit z Azure AD.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/claimsaad1.png)

    #### <a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scénář: Vyberte nakonfigurovaný e-mailovou adresu v SU01 podle id uživatele SAP. Id e-mailu v tomto případě musí být nakonfigurovaný v su01 pro každý uživatel, který vyžaduje jednotného přihlašování.

    a.  Snímek obrazovky podrobností NameID od SAPU.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. snímek obrazovky, že zmíníte požadované deklarace identit z Azure AD.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/claimsaad2.png)

15. Klikněte na tlačítko **Uložit** a potom klikněte na tlačítko **povolit** povolit zprostředkovatele identity.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/configuration1.png)

16. Klikněte na tlačítko **OK** po zobrazení výzvy.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k SAP NetWeaver.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SAP NetWeaver**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SAP NetWeaver**.

    ![SAP NetWeaver odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-sap-netweaver-test-user"></a>Vytvořit testovacího uživatele SAP NetWeaver

V této části vytvoříte uživatele v SAP NetWeaver jako Britta Simon. Fungovat interně odborné tým SAP nebo spolupráci s partnerem SAP organizace přidat uživatele na platformě SAP Netweaveru.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

1. Jakmile poskytovatel identity Azure AD se aktivoval, zkuste níže uvedenou adresu URL ke kontrole jednotné přihlašování (nebudou se výzva pro uživatelské jméno a heslo)

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (nebo) použijte níže uvedenou adresu URL

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > Nahraďte sapurl skutečný název hostitele SAP.

2. Výše uvedené adresy URL by vám neměl zabrat na níže uvedené obrazovce. Pokud je to možné dosáhnout až následující stránce nastavení jednotného přihlašování k Azure AD úspěšně provést.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/testingsso.png)

3. Pokud dojde k řádku uživatelské jméno a heslo, prosím problém Diagnostikujte, povolit trasování pomocí níže uvedenou adresu URL

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

