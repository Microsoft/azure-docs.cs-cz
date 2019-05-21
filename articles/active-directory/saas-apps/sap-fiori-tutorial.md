---
title: 'Kurz: Integrace Azure Active Directory s řešení SAP Fiori | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a řešení SAP Fiori.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903960"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>Kurz: Integrace Azure Active Directory s řešení SAP Fiori

V tomto kurzu se dozvíte, jak integrovat řešení SAP Fiori s Azure Active Directory (Azure AD).

Integrace řešení SAP Fiori s Azure AD poskytuje následující výhody:

* Můžete použít Azure AD a určovat, kdo má přístup k řešení SAP Fiori.
* Uživatelům můžete být automaticky přihlášeni na řešení SAP Fiori pomocí jejich účtů služby Azure AD (jednotné přihlašování).
* Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s řešení SAP Fiori, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ještě nemáte předplatné Azure AD, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.
* Předplatné řešení SAP Fiori pomocí jednotného přihlašování povolená.
* Vyžaduje se řešení SAP Fiori 7.20 nebo novější.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování a integrace řešení SAP Fiori s Azure AD.

Řešení SAP Fiori podporuje následující funkce:

* **Iniciovaného Zprostředkovatelem přihlašování jednotného přihlašování**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Přidat řešení SAP Fiori na webu Azure Portal

Řešení SAP Fiori integrovat Azure AD, je nutné přidat na seznam spravovaných aplikací SaaS řešení SAP Fiori.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **Azure Active Directory**.

    ![Možnost Azure Active Directory](common/select-azuread.png)

1. Vyberte **podnikové aplikace** > **všechny aplikace**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. Chcete-li přidat aplikaci, vyberte **novou aplikaci**.

    ![Nová možnost aplikace](common/add-new-app.png)

1. Do vyhledávacího pole zadejte **řešení SAP Fiori**. Ve výsledcích hledání vyberte **řešení SAP Fiori**a pak vyberte **přidat**.

    ![Řešení SAP Fiori v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testování Azure AD jednotné přihlašování pomocí SAP Fiori podle testovacího uživatele s názvem **Britta Simon**. Pro jednotné přihlašování pro práci je potřeba vytvořit propojené vztah mezi uživatele služby Azure AD a související uživatel v řešení SAP Fiori.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SAP Fiori, je nutné dokončit následující stavebních bloků:

| Úkol | Popis |
| --- | --- |
| **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)** | Umožňuje uživatelům tuto funkci používat. |
| **[Konfigurace řešení SAP Fiori jednotného přihlašování](#configure-sap-fiori-single-sign-on)** | Konfiguruje nastavení jednotného přihlašování v aplikaci. |
| **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** | Zkoušky Azure AD jednotného přihlašování pro uživatele s názvem Britta Simon. |
| **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)** | Umožňuje Britta Simon používat Azure AD jednotného přihlašování. |
| **[Vytvořit řešení SAP Fiori testovacího uživatele](#create-an-sap-fiori-test-user)** | Vytvoří protějšek Britta Simon v řešení SAP Fiori, který je propojený s Azure AD zastoupení uživatele. |
| **[Otestovat jednotné přihlašování](#test-single-sign-on)** | Ověřuje, že konfigurace funguje. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části nakonfigurujete služby Azure AD jednotné přihlašování pomocí SAP Fiori na webu Azure Portal.

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu řešení SAP Fiori společnosti jako správce.

1. Ujistěte se, že **http** a **https** služby jsou aktivní a příslušné porty jsou přiřazeny k kód transakce **SMICM**.

1. Přihlaste se k SAP Business klienta pro systém SAP **T01**, kde jednotného přihlašování je povinný. Potom aktivujte Správa relací zabezpečení protokolu HTTP.

    1. Přejděte na kód transakce **SICF_SESSIONS**. Jsou uvedeny všechny parametry příslušný profil pomocí aktuálních hodnot. Vypadají jako v následujícím příkladu:

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
        > Upravte parametry na základě požadavků organizace. Předchozí parametry jsou uvedeny jenom jako příklad.

    1. V případě potřeby upravte parametry v profilu instance (výchozí) systému SAP a restartování systému SAP.

    1. Dvakrát klikněte na příslušné klienta povolit relaci zabezpečení protokolu HTTP.

        ![Na stránce aktuální hodnoty z příslušné profilu parametry v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktivujte SICF následující služby:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Přejděte na kód transakce **SAML2** obchodní klienta pro systém SAP [**T01/122**]. Konfigurace uživatelského rozhraní se otevře v novém okně prohlížeče. V tomto příkladu používáme obchodní klienta pro systém SAP 122.

    ![Klient Business SAP Fiori přihlašovací stránky](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Zadejte uživatelské jméno a heslo a pak vyberte **přihlášení**.

    ![Na stránce SAML 2.0 konfigurace z ABAP systému T01/122 v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. V **název zprostředkovatele** přepište **T01122** s **http:\//T01122**a pak vyberte **Uložit**.

    > [!NOTE]
    > Výchozí název zprostředkovatele je ve formátu \<sid >\<klienta >. Azure AD očekává, že název ve formátu \<protokolu > ://\<název >. Doporučujeme vám, že udržujete název zprostředkovatele protokolu https\://\<sid >\<klienta > tak, že ve službě Azure AD můžete nakonfigurovat několik modulů SAP Fiori ABAP.

    ![Název aktualizace zprostředkovatele na stránce SAML 2.0 konfigurace z ABAP systému T01/122 v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Vyberte **karta místní zprostředkovatel** > **metadat**.

1. V **metadat SAML 2.0** dialogového okna zadejte stažení souboru XML generovaných metadat a uložte ho do počítače.

    ![Stáhnout Metadata odkaz v dialogovém okně metadat SAML 2.0 SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. V [webu Azure portal](https://portal.azure.com/)v **řešení SAP Fiori** podokno integrace aplikací, vyberte **jednotného přihlašování**.

    ![Možnost přihlašování](common/select-sso.png)

1. V **vybrat jedinou metodu přihlašování** podokně, vyberte **SAML** nebo **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

1. V **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit** (ikonu tužky) Chcete-li otevřít **základní konfiguraci SAML** podokně.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** části, proveďte následující kroky:

    1. Vyberte **nahrát soubor metadat**.

        ![Možnosti nahrání metadat souboru](common/upload-metadata.png)

   1. Pokud chcete vybrat soubor metadat, vyberte ikonu složky a pak vyberte **nahrát**.

       ![Vyberte soubor metadat a pak vyberte tlačítko Nahrát](common/browse-upload-metadata.png)

1. Při úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty se automaticky vyplní v **základní konfiguraci SAML** podokně. V **přihlašovací adresa URL** zadejte adresu URL, která má následující vzor: https:\//\<společnosti instanci řešení SAP Fiori\>.

    ![SAP Fiori domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    > [!NOTE]
    > Několik Zákazníci hlásí chyby související s není správně nakonfigurován. **adresy URL odpovědi** hodnoty. Pokud se zobrazí tato chyba, můžete použít následující skript prostředí PowerShell k nastavení správné adresy URL odpovědi pro vaši instanci:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Můžete nastavit `ServicePrincipal` objektu ID před spuštěním skriptu, nebo jej lze předat tady.

1. Řešení SAP Fiori aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Jak spravovat tyto hodnoty atributů **nastavte si jednotné přihlašování pomocí SAML** vyberte **upravit**.

    ![Podokno atributy uživatele](common/edit-attribute.png)

1. V **atributy uživatele a deklarace identity** podokně nakonfigurovat atributy tokenu SAML, jak je znázorněno na předchozím obrázku. Potom proveďte následující kroky:

    1. Vyberte **upravit** otevřít **spravovat deklarace identity uživatelů** podokně.

    1. V **transformace** seznamu vyberte **ExtractMailPrefix()**.

    1. V **parametr 1** seznamu vyberte **user.userprinicipalname**.

    1. Vyberte **Uložit**.

       ![V podokně Správa uživatelské deklarace identity](./media/sapfiori-tutorial/nameidattribute.png)

       ![V části transformace v podokně Správa uživatelské deklarace identity](./media/sapfiori-tutorial/nameidattribute1.png)


1. V **nastavte si jednotné přihlašování pomocí SAML** podokno v **podpisový certifikát SAML** vyberte **Stáhnout** vedle **kód XML metadat federace**. Možnost stažení na základě vašich požadavků. Uložte si certifikát ve vašem počítači.

    ![Možnost stažení certifikátu](common/metadataxml.png)

1. V **nastavit řešení SAP Fiori** tématu, zkopírujte následující adresy URL na základě vašich požadavků:

    * Přihlašovací adresa URL
    * Identifikátor Azure AD
    * Adresa URL – odhlášení

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>Konfigurace řešení SAP Fiori jednotného přihlašování

1. Přihlaste se k systému SAP a přejít ke kódu transakce **SAML2**. Na stránce konfigurace SAML se otevře nové okno prohlížeče.

1. Pokud chcete nakonfigurovat koncové body pro důvěryhodného zprostředkovatele identity (Azure AD), vyberte **důvěryhodných zprostředkovatelů** kartu.

    ![Na kartě důvěryhodných zprostředkovatelů v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Vyberte **přidat**a pak vyberte **nahrát soubor metadat** v místní nabídce.

    ![Možnosti Přidat a nahrát soubor metadat v systému SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Nahrajte soubor metadat, který jste stáhli na webu Azure Portal. Vyberte **Další**.

    ![Vyberte soubor metadat k odeslání v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na další stránce v **Alias** zadejte název aliasu. Například **aadsts**. Vyberte **Další**.

    ![Do pole aliasu v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Ujistěte se, že hodnota ve **algoritmus Digest** pole **SHA-256**. Vyberte **Další**.

    ![Ověřte hodnotu algoritmus Digest ve SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. V části **jednotné přihlašování – koncové body**vyberte **HTTP POST**a pak vyberte **Další**.

    ![Volby jednotného přihlašování koncových bodů v systému SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. V části **koncové body jednotné odhlašování**vyberte **přesměrování protokolu HTTP**a pak vyberte **Další**.

    ![Volby jednotného odhlašování koncových bodů v systému SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. V části **koncové body artefaktů**vyberte **Další** pokračujte.

    ![Možnosti koncových bodů artefakt v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. V části **požadavky na ověřování**vyberte **Dokončit**.

    ![Možnosti ověřování požadavky a možnost Dokončit v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Vyberte **důvěryhodného zprostředkovatele** > **federaci identit** (v dolní části stránky). Vyberte **Upravit**.

    ![Karty důvěryhodného zprostředkovatele a federace identit v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Vyberte **Přidat**.

    ![Možnost přidat na kartě federace identit](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. V **podporované formáty NameID** dialogu **nespecifikovaný**. Vyberte **OK**.

    ![Podporované formáty NameID dialogové okno a možnosti v systému SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Hodnoty pro **uživatelského ID zdroje** a **režim mapování ID uživatele** určit propojení mezi uživateli SAP a deklarace identity Azure AD.  

    **Scénář 1**: Uživatel systému SAP pro mapování uživatele Azure AD

    1. V SAPU v rámci **podrobnosti o formátu NameID "Neuvedeno"**, poznamenejte si podrobnosti:

        ![Pole "Neurčené" dialogové okno Podrobnosti o formátu NameID v SAP](./media/sapfiori-tutorial/nameiddetails.png)

    1. Na webu Azure Portal v části **atributy uživatele a deklarace identity**, mějte na paměti požadované deklarace identit z Azure AD.

        ![Dialogové okno atributy uživatele a deklarace identity na webu Azure Portal](./media/sapfiori-tutorial/claimsaad1.png)

    **Scénář 2**: Vyberte nakonfigurovaný e-mailovou adresu v SU01 podle ID uživatele SAP. ID e-mailu v tomto případě byste měli nakonfigurovat v SU01 pro každý uživatel, který vyžaduje jednotného přihlašování.

    1.  V SAPU v rámci **podrobnosti o formátu NameID "Neuvedeno"**, poznamenejte si podrobnosti:

        ![Pole "Neurčené" dialogové okno Podrobnosti o formátu NameID v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Na webu Azure Portal v části **atributy uživatele a deklarace identity**, mějte na paměti požadované deklarace identit z Azure AD.

       ![Dialogové okno atributy uživatele a deklarace identity na webu Azure Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Vyberte **Uložit**a pak vyberte **povolit** povolit zprostředkovatele identity.

    ![Uložit a povolit možnosti v systému SAP](./media/sapfiori-tutorial/configuration1.png)

1. Vyberte **OK** po zobrazení výzvy.

    ![Možnost OK v dialogovém okně Konfigurace SAML 2.0 v SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem Britta Simon na webu Azure Portal.

1. Na webu Azure Portal, vyberte **Azure Active Directory** > **uživatelé** > **všichni uživatelé**.

    ![Uživatelé a všechny možnosti uživatele](common/users.png)

1. Vyberte **nového uživatele**.

    ![Nová možnost uživatele](common/new-user.png)

1. V **uživatele** podokno, proveďte následující kroky:

    1. V **název** zadejte **BrittaSimon**.
  
    1. V **uživatelské jméno** zadejte **brittasimon\@\<your domény společnosti >.\< Rozšíření >**. Například **brittasimon\@contoso.com**.

    1. Vyberte **zobrazit heslo** zaškrtávací políčko. Zapište hodnotu, která se zobrazí **heslo** pole.

    1. Vyberte **Vytvořit**.

    ![V podokně uživatele](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části udělíte přístup Britta Simon na řešení SAP Fiori, které můžete použít Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace** > **všechny aplikace** > **řešení SAP Fiori**.

    ![V podokně podnikových aplikací](common/enterprise-applications.png)

1. V seznamu aplikací vyberte **řešení SAP Fiori**.

    ![Řešení SAP Fiori v seznamu aplikací](common/all-applications.png)

1. V nabídce vyberte **uživatelů a skupin**.

    ![Možnost uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

1. V **uživatelů a skupin** vyberte **Britta Simon** v seznamu uživatelů. Zvolte **Vybrat**.

1. Pokud se očekává hodnotu kontrolního výrazu SAML, do role v **vybrat roli** podokně, vyberte odpovídající roli pro uživatele ze seznamu. Zvolte **Vybrat**.

1. V **přidat přiřazení** vyberte **přiřadit**.

### <a name="create-an-sap-fiori-test-user"></a>Vytvořit řešení SAP Fiori testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v řešení SAP Fiori. Spolupracovat s týmem SAP interní odborníci nebo váš partner SAP organizace přidat uživatele na platformě řešení SAP Fiori.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

1. Po aktivaci služby Azure AD zprostředkovatele identity v řešení SAP Fiori pokusu o přístup k některé z následujících adres URL, který chcete otestovat jednotné přihlašování (byste měli být vyzváni k zadání uživatelského jména a hesla):

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > Nahraďte *sapurl* skutečným názvem hostitele SAP.

1. Testovací adresa URL by vám neměl zabrat na následující stránku test aplikace v systému SAP. Pokud se otevře na stránce služby Azure AD jednotného přihlašování se úspěšně nastavili.

    ![Standardní testovací stránka aplikace v systému SAP](./media/sapfiori-tutorial/testingsso.png)

1. Pokud se zobrazí výzva k zadání uživatelského jména a hesla, povolte trasování pro pomoc s diagnostikou problému. Použijte následující adresu URL pro trasování: https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool? sap klienta = 122 & jazyka sap = cs #.

## <a name="next-steps"></a>Další postup

Další informace, projděte si tyto články:

- [Seznam kurzů integrace aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Jednotné přihlašování k aplikacím v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
