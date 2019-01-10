---
title: 'Kurz: Integrace Azure Active Directory se SAP Netweaverem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP NetWeaver.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: 607d05818966e62407795640d223f1aed2f59bbb
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156744"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Kurz: Integrace Azure Active Directory se SAP Netweaverem

V tomto kurzu se dozvíte, jak integrovat SAP NetWeaver pomocí Azure Active Directory (Azure AD).

SAP NetWeaver integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SAP Netweaveru.
- Uživatele, aby automaticky získat přihlášení k SAP NetWeaver (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SAP NetWeaver, potřebujete následující položky:

- Předplatné Azure AD
- SAP NetWeaver jednotného přihlašování povolená předplatného
- SAP NetWeaver V7.20 vyžaduje alespoň

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SAP NetWeaver v galerii
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sap-netweaver-from-the-gallery"></a>Přidání SAP NetWeaver v galerii

Konfigurace integrace SAP NetWeaver do služby Azure AD, budete muset přidat SAP NetWeaver na váš seznam spravovaných aplikací SaaS z galerie.

**SAP NetWeaver přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SAP NetWeaver**vyberte **SAP NetWeaver** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SAP NetWeaver v seznamu výsledků](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí SAP NetWeaver na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v SAP NetWeaver je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatelem v systému SAP NetWeaver potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SAP NetWeaver, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SAP NetWeaver](#creating-sapnetweaver-test-user)**  – Pokud chcete mít protějšek Britta Simon v SAP NetWeaver, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování jednotného přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SAP NetWeaver.

**Ke konfiguraci Azure AD jednotného přihlašování se SAP Netweaverem, proveďte následující kroky:**

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

8. Na webu Azure Portal na **SAP NetWeaver** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

9. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](common/tutorial_general_301.png)

10. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](common/editconfigure.png)

11. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. Klikněte na tlačítko **nahrát soubor metadat** k nahrání **soubor metadat poskytovatele služeb** které jste získali už dřív.

    ![Nahrát soubor metadat](common/editmetadataupload.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Nahrát soubor metadat](common/uploadmetadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části textového pole, jak je znázorněno níže :

    ![SAP NetWeaver domény a adresy URL jednotného přihlašování – informace](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<your company instance of SAP NetWeaver>`

12. Aplikace SAP NetWeaver očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![Atribut oddílu](./media/sapnetweaver-tutorial/edit_attribute.png)

13. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na **upravit** ikony otevřete **spravovat deklarace identity uživatelů** dialogového okna.
    
    ![Atribut oddílu](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. Na **spravovat deklarace identity uživatelů** kartu, proveďte následující kroky:

    ![Atribut oddílu](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * Vyberte **transformace**.
  
    * Z **transformace** seznamu vyberte `ExtractMailPrefix()`.
  
    * Z **parametr 1** seznamu vyberte `user.userprincipalname`.

    * Klikněte na **Uložit**.

14. Na **podpisový certifikát SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. Na **nastavit SAP NetWeaver** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

    ![Konfigurace SAP NetWeaver](common/configuresection.png)

16. Přihlášení k systému SAP a přejít ke kódu transakce typu SAML2. Otevře se nové okno prohlížeče s obrazovku pro konfiguraci SAML.

17. Konfigurace koncových bodů pro důvěryhodného Identity zprostředkovatele (Azure AD) v části **důvěryhodných zprostředkovatelů** kartu.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. Stisknutím klávesy **přidat** a vyberte **nahrát soubor metadat** v místní nabídce.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Nahrajte soubor metadat, který jste si stáhli z portálu Azure portal.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. Na další obrazovce zadejte název aliasu. Například aadsts a stiskněte klávesu **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. Ujistěte se, že vaše **algoritmus Digest** by měl být **SHA-256** a nevyžadují žádné změny a stiskněte klávesu **Další**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. Na **jednotné přihlašování – koncové body**, použijte **HTTP POST** a klikněte na tlačítko **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. Na **koncové body jednotné odhlašování** vyberte **HTTPRedirect** a klikněte na tlačítko **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. Na **koncové body artefaktů**, stiskněte klávesu **Další** pokračujte.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. Na **požadavky na ověřování**, klikněte na tlačítko **Dokončit**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. Přejděte na kartu **důvěryhodného zprostředkovatele** > **federaci identit** (v dolní části obrazovky). Klikněte na **Upravit**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. Klikněte na tlačítko **přidat** pod **federaci identit** kartu (dolní části okna).

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. V automaticky otevíraném okně vyberte **nespecifikovaný** z **formáty podporované NameID** a klikněte na tlačítko OK.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. Všimněte si, že **uživatelské ID zdroje** a **režim mapování id uživatele** hodnoty určují propojení mezi uživatel systému SAP a deklarace identity Azure AD.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>Scénář: Uživatel systému SAP pro mapování uživatele Azure AD.

    a. Snímek obrazovky podrobností NameID od SAPU.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Snímek obrazovky zmiňuje požadované deklarace identit z Azure AD.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>Scénář: Vyberte nakonfigurovaný e-mailovou adresu v SU01 podle id uživatele SAP. Id e-mailu v tomto případě musí být nakonfigurovaný v su01 pro každý uživatel, který vyžaduje jednotného přihlašování.

    a.  Snímek obrazovky podrobností NameID od SAPU.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Snímek obrazovky zmiňuje požadované deklarace identit z Azure AD.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/claimsaad2.png)

30. Klikněte na tlačítko **Uložit** a potom klikněte na tlačítko **povolit** povolit zprostředkovatele identity.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/configuration1.png)

31. Klikněte na tlačítko **OK** po zobrazení výzvy.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/configuration2.png)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_01.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](common/create_aaduser_02.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-sap-netweaver-test-user"></a>Vytváření SAP NetWeaver testovacího uživatele

V této části vytvoříte uživatele v SAP NetWeaver jako Britta Simon. Fungovat interně odborné tým SAP nebo spolupráci s partnerem SAP organizace přidat uživatele na platformě SAP Netweaveru.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k SAP NetWeaver.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **SAP NetWeaver**.

    ![Konfigurace jednotného přihlašování](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

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

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
