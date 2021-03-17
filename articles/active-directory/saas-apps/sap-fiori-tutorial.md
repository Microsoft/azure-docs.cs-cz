---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí SAP Fiori | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP Fiori.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 547d96a9591b99318a74977106e99511c9c80507
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687103"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-fiori"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí SAP Fiori

V tomto kurzu se dozvíte, jak integrovat SAP Fiori s Azure Active Directory (Azure AD). Když integrujete SAP Fiori s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SAP Fiori.
* Umožněte uživatelům, aby se do SAP Fiori automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné SAP Fiori s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SAP Fiori podporuje jednotné přihlašování spouštěné v **SP**

> [!NOTE]
> Pro ověřování pomocí protokolu iFrame iniciované systémem SAP Fiori doporučujeme pro tiché ověřování **použít parametr v SAML** AuthnRequest. Další podrobnosti o parametru parametr- **Pass** najdete v tématu informace o [jednotném přihlašování v Azure AD SAML](../develop/single-sign-on-saml-protocol.md) .

## <a name="adding-sap-fiori-from-the-gallery"></a>Přidání SAP Fiori z Galerie

Pokud chcete nakonfigurovat integraci SAP Fiori do služby Azure AD, musíte přidat SAP Fiori z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SAP Fiori** .
1. Z panelu výsledků vyberte **SAP Fiori** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sap-fiori"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SAP Fiori

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SAP Fiori pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SAP Fiori.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SAP Fiori, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte FIORI SSO pro SAP](#configure-sap-fiori-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele SAP Fiori](#create-sap-fiori-test-user)** , abyste měli protějšek B. Simon v SAP Fioriu, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu SAP Fiori společnosti jako správce.

1. Ujistěte se, že jsou služby **http** a **https** aktivní a že příslušné porty jsou přiřazené k **SMICM** kódu transakce.

1. Přihlaste se k SAP Business Client pro SAP System **T01**, kde se vyžaduje jednotné přihlašování. Pak aktivujte správu relace zabezpečení protokolu HTTP.

    1. Přejít na kód transakce **SICF_SESSIONS**. Zobrazí se všechny relevantní parametry profilu s aktuálními hodnotami. Vypadají jako v následujícím příkladu:

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
        > Upravte parametry podle požadavků vaší organizace. Předchozí parametry jsou uvedeny pouze jako příklad.

    1. V případě potřeby upravte parametry v profilu instance (výchozí) v systému SAP a restartujte systém SAP.

    1. Dvojím kliknutím na příslušného klienta povolte relaci zabezpečení HTTP.

        ![Aktuální hodnoty příslušné stránky parametrů profilu v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. Aktivujte tyto služby SICF Services:

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Přejít na kód transakce **typu Saml2** v obchodním klientovi pro systém SAP [**T01/122**]. Uživatelské rozhraní konfigurace se otevře v novém okně prohlížeče. V tomto příkladu používáme obchodní klient pro SAP System 122.

    ![Přihlašovací stránka obchodního klienta SAP Fiori](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. Zadejte své uživatelské jméno a heslo a pak vyberte **Přihlásit** se.

    ![Konfigurace SAML 2,0 pro stránku ABAP System T01/122 v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. V poli **název poskytovatele** nahraďte **T01122** řetězcem **http: \/ /T01122** a pak vyberte **Uložit**.

    > [!NOTE]
    > Ve výchozím nastavení je název poskytovatele ve formátu \<sid> \<client> . Azure AD očekává název ve formátu \<protocol> :// \<name> . Doporučujeme, abyste název poskytovatele zachovali jako https \: // \<sid> \<client> , abyste mohli ve službě Azure AD nakonfigurovat víc Fiorich ABAP SAP.

    ![Název aktualizovaného zprostředkovatele v konfiguraci SAML 2,0 na stránce ABAP System T01/122 v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. Vyberte **metadata karty místního zprostředkovatele**  >  .

1. V dialogovém okně **metadata SAML 2,0** Stáhněte vygenerovaný soubor XML s metadaty a uložte ho do svého počítače.

    ![Odkaz pro stažení metadat v dialogovém okně SAP SAML 2,0 metadata](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. V Azure Portal na stránce integrace aplikací **SAP Fiori** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb** v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi vyplní automaticky v PODOKNĚ **Konfigurace základní konfigurace SAML** . Do pole **přihlašovací adresa URL** zadejte adresu URL, která má následující vzor: `https://<your company instance of SAP Fiori>` .

    > [!NOTE]
    > Několik zákazníků hlásí chyby související s nesprávně nakonfigurovanými hodnotami **adresy URL odpovědi** . Pokud se zobrazí tato chyba, můžete k nastavení správné adresy URL odpovědi pro vaši instanci použít následující skript prostředí PowerShell:
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > Můžete nastavit `ServicePrincipal` ID objektu sami před spuštěním skriptu, nebo ho můžete předat sem.

1. Aplikace SAP Fiori očekává, že kontrolní výrazy SAML budou v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Chcete-li tyto hodnoty atributu spravovat, vyberte v podokně **nastavit jednu Sign-On s** podoknem SAML možnost **Upravit**.

    ![Podokno atributů uživatele](common/edit-attribute.png)

1. V podokně **atributy uživatele & deklarace identity** nakonfigurujte atributy tokenu SAML, jak je znázorněno na předchozím obrázku. Pak proveďte následující kroky:

    1. Výběrem možnosti **Upravit** otevřete podokno **Spravovat deklarace identity uživatele** .

    1. V seznamu **transformace** vyberte **ExtractMailPrefix ()**.

    1. V seznamu **parametr 1** vyberte **User. userPrincipalName**.

    1. Vyberte **Uložit**.

       ![Podokno Spravovat deklarace identity uživatele](./media/sapfiori-tutorial/nameidattribute.png)

       ![Oddíl transformování v podokně spravovat deklarace identity uživatele](./media/sapfiori-tutorial/nameidattribute1.png)
    
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení SAP Fiori** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SAP Fiori.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SAP Fiori**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-fiori-sso"></a>Konfigurace protokolu SAP Fiori SSO

1. Přihlaste se k systému SAP a použijte **typu Saml2** kód transakce. Otevře se nové okno prohlížeče se stránkou konfigurace SAML.

1. Pokud chcete nakonfigurovat koncové body pro důvěryhodného zprostředkovatele identity (Azure AD), vyberte kartu **důvěryhodní zprostředkovatelé** .

    ![Karta důvěryhodní zprostředkovatelé v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. Vyberte **Přidat** a pak v místní nabídce vyberte **nahrát soubor metadat** .

    ![Možnosti přidání a nahrání souboru metadat v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Nahrajte soubor metadat, který jste stáhli v Azure Portal. Vyberte **Další**.

    ![Vyberte soubor metadat, který se má nahrát do SAP.](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. Na další stránce zadejte do pole **alias** název aliasu. Například **aadsts**. Vyberte **Další**.

    ![Pole alias v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. Ujistěte se, že hodnota v poli **algoritmus Digest** je **SHA-256**. Vyberte **Další**.

    ![Ověření hodnoty algoritmu Digest v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. V části **koncové body s jedním Sign-On** vyberte **http post** a pak vyberte **Další**.

    ![Možnosti jednotlivých koncových bodů Sign-On v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. V části **koncové body odhlašovacího** bodu vyberte **přesměrování HTTP** a pak vyberte **Další**.

    ![Možnosti koncového bodu s jediným odhlášením v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. V části **koncové body artefaktů** pokračujte výběrem **Další** .

    ![Možnosti koncových bodů artefaktu v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. V části **požadavky na ověření** vyberte **Dokončit**.

    ![Možnosti požadavků na ověření a možnost Dokončit v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. Vyberte federace identity **důvěryhodných zprostředkovatelů**  >   (ve spodní části stránky). Vyberte **Upravit**.

    ![Karty důvěryhodných zprostředkovatelů a federace identit v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. Vyberte **Přidat**.

    ![Možnost přidat na kartě federace identit](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. V dialogovém okně **podporované formáty NameId** vyberte **Neurčeno**. Vyberte **OK**.

    ![Dialogové okno podporované formáty NameID a možnosti v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    Hodnoty pro režim mapování **zdrojového ID** a **ID uživatele** URČUJÍ propojení mezi uživatelem SAP a deklarací identity Azure AD.  

    **Scénář 1**: mapování uživatele SAP na Azure AD

    1. V části SAP v části **Podrobnosti o formátu NameId "Neurčeno"** si všimněte podrobností:

        ![Snímek obrazovky, který zobrazuje dialogové okno Podrobnosti o nespecifikovaném formátu NameID v S A P](./media/sapfiori-tutorial/nameiddetails.png)

    1. V Azure Portal si v části **atributy uživatelů & deklarace identity** poznamenejte požadované deklarace identity z Azure AD.

        ![Snímek obrazovky, který zobrazuje dialogové okno atributy uživatele & deklarace identity.](./media/sapfiori-tutorial/claimsaad1.png)

    **Scénář 2**: vyberte ID uživatele SAP na základě konfigurované e-mailové adresy v SU01. V takovém případě by se mělo ID e-mailu nakonfigurovat v SU01 pro každého uživatele, který vyžaduje jednotné přihlašování.

    1.  V části SAP v části **Podrobnosti o formátu NameId "Neurčeno"** si všimněte podrobností:

        ![Dialogové okno Podrobnosti o nespecifikovaném formátu NameID v SAP](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. V Azure Portal si v části **atributy uživatelů & deklarace identity** poznamenejte požadované deklarace identity z Azure AD.

       ![Dialogové okno atributy uživatele & deklarace v Azure Portal](./media/sapfiori-tutorial/claimsaad2.png)

1. Vyberte **Uložit** a potom výběrem **Povolit** povolte poskytovatele identity.

    ![Možnosti Uložit a povolit v SAP](./media/sapfiori-tutorial/configuration1.png)

1. Po zobrazení výzvy vyberte **OK** .

    ![Možnost OK v dialogovém okně Konfigurace SAML 2,0 v SAP](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-sap-fiori-test-user"></a>Vytvořit testovacího uživatele SAP Fiori

V této části vytvoříte uživatele s názvem Britta Simon v SAP Fiori. Pokud chcete přidat uživatele na platformě SAP Fiori, spolupracujte s vaším interním týmem pro SAP a odborníky na SAP vaší organizace.

## <a name="test-sso"></a>Test SSO

1. Po aktivaci poskytovatele identity Azure AD v SAP Fiori se pokuste získat přístup k jedné z následujících adres URL pro testování jednotného přihlašování (nemůžete být vyzváni k zadání uživatelského jména a hesla):

    * https: \/ / \<sapurl\> /SAP/BC/BSP/SAP/it00/default.htm
    * https: \/ / \<sapurl\> /SAP/BC/BSP/SAP/it00/default.htm

    > [!NOTE]
    > Nahraďte *sapurl* skutečným názvem hostitele SAP.

1. Adresa URL testu by se měla přebírat na následující stránce testovací aplikace v SAP. Pokud se stránka otevře, jednotné přihlašování Azure AD se úspěšně nastaví.

    ![Stránka standardní testovací aplikace v SAP](./media/sapfiori-tutorial/testingsso.png)

1. Pokud se zobrazí výzva k zadání uživatelského jména a hesla, povolte trasování, které vám pomůžou problém diagnostikovat. Pro trasování použijte následující adresu URL: https: \/ / \<sapurl\> /SAP/BC/WebDynpro/SAP/sec_diag_tool? SAP-Client = 122&SAP-Language = en #.

## <a name="next-steps"></a>Další kroky

Po nakonfigurování SAP Fiori můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).