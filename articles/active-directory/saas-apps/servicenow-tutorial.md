---
title: 'Kurz: Integrace Azure Active Directory s použitím ServiceNow | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: e0334b0a234b8fa2e3e3535d6238af2062adb22d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54816411"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>Kurz: Integrace Azure Active Directory s použitím ServiceNow

V tomto kurzu se dozvíte, jak integrovat ServiceNow s Azure Active Directory (Azure AD).
ServiceNow integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k ServiceNow.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k ServiceNow (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s použitím ServiceNow, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* ServiceNow jednotného přihlašování povolená předplatného
* Pro ServiceNow, instanci nebo tenanta ServiceNow, Calgary verze nebo novější
* Pro ServiceNow Express, instance ServiceNow Express, Helsinki verze nebo novější
* ServiceNow klient musí mít [více jednotné přihlašování na modul Plugin poskytovatele](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) povolena. To můžete udělat [odesílá se žádost o službu](https://hi.service-now.com).
* Pro automatickou konfiguraci povolení modulu plug-in více zprostředkovatele pro ServiceNow.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* ServiceNow podporuje **SP** jednotné přihlašování zahájené pomocí

* ServiceNow podporuje [ **automatizovaná** zřizování uživatelů](servicenow-provisioning-tutorial.md)

## <a name="adding-servicenow-from-the-gallery"></a>Přidání ServiceNow z Galerie

Konfigurace integrace ServiceNow do služby Azure AD, budete muset přidat ServiceNow z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat ServiceNow z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ServiceNow**vyberte **ServiceNow** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![ServiceNow v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s použitím ServiceNow podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v ServiceNow.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s použitím ServiceNow, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On pro ServiceNow](#configure-azure-ad-single-sign-on-for-servicenow)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ServiceNow Single Sign-On](#configure-servicenow-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Konfigurovat Azure AD Single Sign-On pro ServiceNow Express](#configure-azure-ad-single-sign-on-for-servicenow-express)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
4. **[Konfigurace ServiceNow Express Single Sign-On](#configure-servicenow-express-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
5. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
6. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
7. **[Vytvořit testovacího uživatele ServiceNow](#create-servicenow-test-user)**  – Pokud chcete mít protějšek Britta Simon ServiceNow, který je propojený s Azure AD reprezentace uživatele.
8. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>Konfigurace Azure AD Single Sign-On pro ServiceNow

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s použitím ServiceNow, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **ServiceNow** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ServiceNow domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Budete muset aktualizovat tyto hodnoty od skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

    a. Klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku, protože tato adresa Url metadat federace aplikace se použije v pozdější části kurzu.

    b. Klikněte na **Stáhnout** Stáhnout **Certificate(Base64)** a uložte soubor certifikátu v počítači.

6. Na **nastavení ServiceNow** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-servicenow-single-sign-on"></a>Konfigurace přihlašování jedním ServiceNow

1. Přihlaste se k ServiceNow aplikaci jako správce.

2. Aktivovat **integrace – více jednotné přihlašování – instalační program zprostředkovatele** modulu plug-in podle následující kroků:

    a. V navigačním podokně na levé straně, vyhledejte **definice systému** části z panelu hledání a potom klikněte na tlačítko **moduly plug-in**.

    ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "aktivace modulu plug-in")

    b. Vyhledejte **integrace – více jednotné přihlašování – instalační program zprostředkovatele**.

     ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "aktivace modulu plug-in")

    c. Vyberte modul plug-in. Klikněte pravým tlačítkem a vyberte **aktivovat/Upgrade**.

     ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_activate.png "aktivace modulu plug-in")

    d. Klikněte na tlačítko **aktivovat** tlačítko.

     ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_activate1.png "aktivace modulu plug-in")

3. V navigačním podokně na levé straně, vyhledejte **více poskytovatele jednotného přihlašování** části z panelu hledání a potom klikněte na tlačítko **vlastnosti**.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/tutorial_servicenow_06.png "konfigurovat adresu URL aplikace")

4. Na **více vlastností poskytovatele jednotného přihlašování** dialogového okna, proveďte následující kroky:

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694981.png "konfigurovat adresu URL aplikace")

    * Jako **povolit více poskytovatele jednotného přihlašování**vyberte **Ano**.
  
    * Jako **povolit automatický import uživatelů ze všech poskytovatelů identity v tabulce uživatelských**vyberte **Ano**.

    * Jako **protokolování pro více zprostředkovatele integraci jednotného přihlašování k povolení ladění**vyberte **Ano**.

    * V **pole na uživatele, který tabulky...**  textové pole, typ **uživatelské_jméno**.
  
    * Klikněte na **Uložit**.

5. Existují dva způsoby, ve kterém **ServiceNow** lze nakonfigurovat - automatické a ruční.

6. Pro konfiguraci **ServiceNow** automaticky, postupujte následujících kroků:

    * Vraťte se **ServiceNow** jednotného přihlašování na stránce na webu Azure Portal.

    * Jedním kliknutím konfigurace služby je k dispozici pro ServiceNow tedy bude Azure AD automaticky nakonfigurovat ServiceNow pro ověřování založené na SAML. Pokud chcete povolit tuto službu, přejděte na **ServiceNow konfigurace** klikněte na tlačítko **nakonfigurovat ServiceNow** konfigurovat přihlašování – okno.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Zadejte název instance ServiceNow, uživatelské jméno admin a heslo správce **nakonfigurovat přihlašování** formuláři a klikněte na tlačítko **nakonfigurovat nyní**. Všimněte si, že musíte mít uživatelské jméno správce zadané **security_admin** v ServiceNow pro tuto práci přiřazenou roli. V opačném případě ručně nakonfigurovat ServiceNow, které používají Azure AD jako zprostředkovatele Identity SAML, klikněte na **ručně nakonfigurovat jednotné přihlašování** a zkopírujte **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z části Stručná referenční příručka.

        ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

    * Přihlaste se k ServiceNow aplikaci jako správce.

    * V Automatická konfigurace jsou všechna potřebná nastavení nakonfigurované na **ServiceNow** straně ale **certifikát X.509** není ve výchozím nastavení povolená. Budete muset ručně namapovat na poskytovatele Identity v ServiceNow. postupujte podle níže uvedeného postupu u stejné:

    * V navigačním podokně na levé straně, vyhledejte **více poskytovatele jednotného přihlašování** části z panelu hledání a potom klikněte na tlačítko **zprostředkovatelé Identity**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na automaticky generovaný zprostředkovatele Identity

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_08.png "nakonfigurovat jednotné přihlašování")

    *  Na **zprostředkovatele Identity** části, proveďte následující kroky:

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/automatic_config.png "nakonfigurovat jednotné přihlašování")

        * V **název** textového pole zadejte název pro vaši konfiguraci (například **Microsoft Azure federované jednotné přihlašování**).

        * Odeberte prosím vyplněná **zprostředkovatele Identity SingleLogoutRequest** hodnotu z textového pole.

        * Kopírování **domovské stránky ServiceNow** hodnota a vložte ji **přihlašovací adresa URL** textového pole v **ServiceNow domény a adresy URL** části na webu Azure portal.

            > [!NOTE]
            > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServiceNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

        * Kopírování **Entity ID / vystavitele** hodnota a vložte ji **identifikátor** textového pole v **ServiceNow domény a adresy URL** části na webu Azure portal.

        * Ujistěte se prosím, která **NameID zásady** je nastavena na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu. 

    * Přejděte dolů k položce **certifikát X.509** vyberte **upravit**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_09.png "nakonfigurovat jednotné přihlašování")

    * Vyberte na certifikátu a klikněte na ikonu se šipkou doprava přidejte certifikát

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_11.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na **Uložit**.

    * Klikněte na **Test připojení** v pravém horním rohu stránky.

        ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_activate2.png "aktivace modulu plug-in")

    * Po kliknutí na **Test připojení**, zobrazí se automaticky otevíraném okně, které je potřeba zadat přihlašovací údaje a pod stránku s výsledky se zobrazí. **Výsledky testů jednotné odhlašování** chyba očekávaná prosím chybu ignorovat a klikněte na tlačítko **aktivovat** tlačítko.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/servicenowactivate.png "nakonfigurovat jednotné přihlašování")
  
7. Pro konfiguraci **ServiceNow** ručně, postupujte následujících kroků:

    * Přihlaste se k ServiceNow aplikaci jako správce.

    * V navigačním podokně na levé straně klikněte na tlačítko **zprostředkovatelé Identity**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

    * Na **zprostředkovatelé Identity** dialogového okna, klikněte na tlačítko **nový**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694977.png "nakonfigurovat jednotné přihlašování")

    * Na **zprostředkovatelé Identity** dialogového okna, klikněte na tlačítko **SAML**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694978.png "nakonfigurovat jednotné přihlašování")

    * Na **Import metadat zprostředkovatele Identity** automaticky otevírané okno, proveďte následující kroky:

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/idp.png "nakonfigurovat jednotné přihlašování")

        * Zadejte **adresa Url federačních metadat aplikace** zkopírovanou z webu Azure portal.

        * Klikněte na **Importovat**.

    * Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694982.png "nakonfigurovat jednotné přihlašování")

        * V **název** textového pole zadejte název pro vaši konfiguraci (například **Microsoft Azure federované jednotné přihlašování**).

        * Odeberte prosím vyplněná **zprostředkovatele Identity SingleLogoutRequest** hodnotu z textového pole.

        * Kopírování **domovské stránky ServiceNow** hodnota a vložte ji **přihlašovací adresa URL** textového pole v **ServiceNow domény a adresy URL** části na webu Azure portal.

            > [!NOTE]
            > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServiceNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

        * Kopírování **Entity ID / vystavitele** hodnota a vložte ji **identifikátor** textového pole v **ServiceNow domény a adresy URL** části na webu Azure portal.

        * Ujistěte se prosím, která **NameID zásady** je nastavena na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu.

        * Klikněte na tlačítko **Advanced**. V **pole uživatel** textové pole, typ **e-mailu** nebo **uživatelské_jméno**podle toho, jaké pole se používá pro jednoznačnou identifikaci uživatelů ve vašem nasazení ServiceNow.

            > [!NOTE]
            > Můžete nakonfigurovat služby Azure AD ke generování ID uživatele služby Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML tak, že přejdete **ServiceNow > atributy > Single Sign-On** části webu Azure portal a mapování na požadované pole **nameidentifier** atribut. Hodnota uložená ve službě Azure AD (například hlavní název uživatele) pro vybraný atribut musí odpovídat hodnotě uložené v ServiceNow pro zadané pole (například uživatelské_jméno)

        * Klikněte na **Test připojení** v pravém horním rohu stránky.

        * Po kliknutí na **Test připojení**, zobrazí se automaticky otevíraném okně, které je potřeba zadat přihlašovací údaje a pod stránku s výsledky se zobrazí. **Výsledky testů jednotné odhlašování** chyba očekávaná prosím chybu ignorovat a klikněte na tlačítko **aktivovat** tlačítko.

          ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/servicenowactivate.png "nakonfigurovat jednotné přihlašování")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>Konfigurovat Azure AD Single Sign-On pro ServiceNow Express

1. V [webu Azure portal](https://portal.azure.com/)na **ServiceNow** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![ServiceNow domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Budete muset aktualizovat tyto hodnoty od skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Jedním kliknutím konfigurace služby je k dispozici pro ServiceNow tedy bude Azure AD automaticky nakonfigurovat ServiceNow pro ověřování založené na SAML. Chcete-li povolit tuto službu přejděte na **nastavení ServiceNow** klikněte na **zobrazit podrobný** konfigurovat přihlašovací okno.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Zadejte název instance ServiceNow, uživatelské jméno admin a heslo správce **nakonfigurovat přihlašování** formuláři a klikněte na tlačítko **nakonfigurovat nyní**. Všimněte si, že musíte mít uživatelské jméno správce zadané **security_admin** v ServiceNow pro tuto práci přiřazenou roli. V opačném případě ručně nakonfigurovat ServiceNow, které používají Azure AD jako zprostředkovatele Identity SAML, klikněte na **ručně nakonfigurovat jednotné přihlašování** a zkopírujte **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z části Stručná referenční příručka.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

### <a name="configure-servicenow-express-single-sign-on"></a>Konfigurace ServiceNow Express jednotného přihlašování

1. Přihlaste se k ServiceNow Express aplikaci jako správce.

2. V navigačním podokně na levé straně klikněte na tlačítko **Single Sign-On**.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694980ex.png "konfigurovat adresu URL aplikace")

3. Na **Single Sign-On** dialogového okna, klikněte na ikonu konfigurace v pravém horním rohu a nastavte následující vlastnosti:

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694981ex.png "konfigurovat adresu URL aplikace")

    a. Přepnout **povolit více poskytovatele jednotného přihlašování** vpravo.

    b. Přepnout **protokolování pro více zprostředkovatele integraci jednotného přihlašování k povolení ladění** vpravo.

    c. V **pole na uživatele, který tabulky...**  textové pole, typ **uživatelské_jméno**.

4. Na **Single Sign-On** dialogového okna, klikněte na tlačítko **přidat nový certifikát**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694973ex.png "nakonfigurovat jednotné přihlašování")

5. Na **certifikáty X.509** dialogového okna, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694975.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **TestSAML2.0**).

    b. Vyberte **aktivní**.

    c. Jako **formátu**vyberte **PEM**.

    d. Jako **typ**vyberte **důvěřovat certifikátu Store**.

    e. Váš certifikát kódovaný v Base64 stáhnout z webu Azure portal v poznámkovém bloku otevřete, zkopírujte obsah ho do schránky a vložte ho do **PEM certifikátu** textového pole.

    f. Klikněte na tlačítko **aktualizace**

6. Na **Single Sign-On** dialogového okna, klikněte na tlačítko **přidat nového zprostředkovatele identity**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694976ex.png "nakonfigurovat jednotné přihlašování")

7. Na **přidat nového zprostředkovatele Identity** dialogového okna, v části **konfigurace zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694982ex.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **SAML 2.0**).

    b. V **adresa URL zprostředkovatele Identity** pole, vložte hodnotu **ID zprostředkovatele Identity**, který jste zkopírovali z portálu Azure portal.

    c. V **zprostředkovatele Identity AuthnRequest** pole, vložte hodnotu **adresy URL požadavku ověřování**, který jste zkopírovali z portálu Azure portal.

    d. V **zprostředkovatele Identity SingleLogoutRequest** pole, vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal

    e. Jako **certifikát poskytovatele Identity**, vyberte certifikát, který jste vytvořili v předchozím kroku.

8. Klikněte na tlačítko **Upřesnit nastavení**a v části **další vlastnosti zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694983ex.png "nakonfigurovat jednotné přihlašování")

    a. V **protokol vazby pro zprostředkovatele identity SingleLogoutRequest** textové pole, typ **urn: oasis: názvy: tc: SAML:2.0:bindings:HTTP – přesměrování**.

    b. V **NameID zásady** textové pole, typ **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: neurčené**.

    c. V **AuthnContextClassRef metoda**, typ `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Zrušte výběr **vytvořit AuthnContextClass**.

9. V části **další vlastnosti zprostředkovatele služby**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694984ex.png "nakonfigurovat jednotné přihlašování")

    a. V **domovské stránky ServiceNow** textového pole zadejte adresu URL vaší domovské stránce instance ServiceNow.

    > [!NOTE]
    > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServiceNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do`).

    b. V **Entity ID / vystavitele** textového pole zadejte adresu URL vašeho tenanta ServiceNow.

    c. V **identifikátor URI cílové skupiny** textového pole zadejte adresu URL vašeho tenanta ServiceNow.

    d. V **hodin zkreslit** textové pole, typ **60**.

    e. V **pole uživatel** textové pole, typ **e-mailu** nebo **uživatelské_jméno**podle toho, jaké pole se používá pro jednoznačnou identifikaci uživatelů ve vašem nasazení ServiceNow.

    > [!NOTE]
    > Můžete nakonfigurovat služby Azure AD ke generování ID uživatele služby Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML tak, že přejdete **ServiceNow > atributy > Single Sign-On** části webu Azure portal a mapování na požadované pole **nameidentifier** atribut. Hodnota uložená ve službě Azure AD (například hlavní název uživatele) pro vybraný atribut musí odpovídat hodnotě uložené v ServiceNow pro zadané pole (například uživatelské_jméno)

    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k ServiceNow.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **ServiceNow**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **ServiceNow**.

    ![V seznamu aplikací na odkaz ServiceNow](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-servicenow-test-user"></a>Vytvořit testovacího uživatele ServiceNow

Cílem této části je vytvořte uživatele Britta Simon v ServiceNow. ServiceNow podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](servicenow-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory klienta ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici ServiceNow na přístupovém panelu, vám by měl být automaticky přihlášeni k ServiceNow, pro kterou můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](servicenow-provisioning-tutorial.md)