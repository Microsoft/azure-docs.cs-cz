---
title: 'Kurz: Integrace Azure Active Directory s použitím ServiceNow | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba516aa2c3d2decaa4962f1ccd0394ebe9a4a62
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706125"
---
# <a name="tutorial-integrate-servicenow-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory ServiceNow

V tomto kurzu se dozvíte, jak integrovat ServiceNow s Azure Active Directory (Azure AD). Když integrujete ServiceNow s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k ServiceNow.
* Aby uživatelé mohli být automaticky přihlášeni k ServiceNow přes svoje účty Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ServiceNow jednotné přihlašování (SSO) povolené předplatné.
* Pro ServiceNow, instanci nebo tenanta ServiceNow, Calgary verze nebo novější
* Pro ServiceNow Express, instance ServiceNow Express, Helsinki verze nebo novější
* ServiceNow klient musí mít [více jednotné přihlašování na modul Plugin poskytovatele](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) povolena. To můžete udělat [odesílá se žádost o službu](https://hi.service-now.com).
* Pro automatickou konfiguraci povolení modulu plug-in více zprostředkovatele pro ServiceNow.
* K instalaci aplikace ServiceNow Classic (mobilní verze), které budete muset přejít na příslušné úložiště a hledat ServiceNow klasické aplikace a klikněte na tlačítko Stáhnout.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. ServiceNow podporuje **SP** iniciované jednotné přihlašování a podporuje [ **automatizovaná** zřizování uživatelů](servicenow-provisioning-tutorial.md).

Aplikace ServiceNow Classic (mobilní verze) se teď dají konfigurovat pomocí Azure AD pro povolení jednotného přihlašování a podporuje obě **Android** a **IOS** uživatelů. V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí.

## <a name="adding-servicenow-from-the-gallery"></a>Přidání ServiceNow z Galerie

Konfigurace integrace ServiceNow do služby Azure AD, budete muset přidat ServiceNow z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **ServiceNow** do vyhledávacího pole.
1. Vyberte **ServiceNow** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s použitím ServiceNow pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v ServiceNow.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s použitím ServiceNow, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ServiceNow](#configure-servicenow)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Konfigurace jednotného přihlašování k Azure AD pro ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
4. **[Konfigurace jednotného přihlašování k ServiceNow Express](#configure-servicenow-express-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
5. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování Azure AD jednotné přihlašování s B.Simon.
6. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
7. **[Vytvořit testovacího uživatele ServiceNow](#create-servicenow-test-user)**  mít protějšek B.Simon ServiceNow, který je propojený s Azure AD reprezentace uživatele.
8. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.
9. **[Otestovat jednotné přihlašování pro ServiceNow Classic (mobilní)](#test-sso-for-servicenow-classic-mobile)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **ServiceNow** stránky integrace aplikací, najdete **spravovat** a vyberte **Single Sign-On**.
1. Na **vyberte metodu jednotného přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Je potřeba aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

   a. Klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a vložte ho do poznámkového bloku, protože tato adresa Url metadat federace aplikace se použije v pozdější části kurzu.

    b. Klikněte na **Stáhnout** Stáhnout **Certificate(Base64)** a uložte soubor certifikátu v počítači.

1. Na **nastavení ServiceNow** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-servicenow"></a>Konfigurace ServiceNow

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

    * Zadejte název instance ServiceNow, uživatelské jméno admin a heslo správce **nakonfigurovat přihlašování** formuláři a klikněte na tlačítko **nakonfigurovat nyní**. Všimněte si, že musíte mít uživatelské jméno správce zadané **security_admin** v ServiceNow pro tuto práci přiřazenou roli. V opačném případě ručně nakonfigurovat ServiceNow, které používají Azure AD jako zprostředkovatele Identity SAML, klikněte na **ručně konfigurovat Single Sign-On** a zkopírujte **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z části Stručná referenční příručka.

        ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

    * Přihlaste se k ServiceNow aplikaci jako správce.

    * V Automatická konfigurace jsou všechna potřebná nastavení nakonfigurované na **ServiceNow** straně ale **certifikát X.509** není ve výchozím nastavení povolená. Budete muset ručně namapovat na poskytovatele Identity v ServiceNow. postupujte podle níže uvedeného postupu u stejné:

    * V navigačním podokně na levé straně, vyhledejte **více poskytovatele jednotného přihlašování** části z panelu hledání a potom klikněte na tlačítko **zprostředkovatelé Identity**.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na automaticky generovaný zprostředkovatele Identity

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/tutorial_servicenow_08.png "nakonfigurovat jednotné přihlašování")

    *  Na **zprostředkovatele Identity** části, proveďte následující kroky:

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/automatic_config.png "nakonfigurovat jednotné přihlašování")

        * V **název** textového pole zadejte název pro vaši konfiguraci (například **Microsoft Azure federované jednotné přihlašování**).

        * Odeberte prosím vyplněná **zprostředkovatele Identity SingleLogoutRequest** hodnotu z textového pole.

        * Kopírování **ServiceNow domovské stránky** hodnota a vložte ji **přihlašovací adresa URL** textového pole v **ServiceNow základní konfiguraci SAML** části na webu Azure portal.

            > [!NOTE]
            > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServiceNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

        * Kopírování **Entity ID / vystavitele** hodnota a vložte ji **identifikátor** textového pole v **základní konfiguraci SAML ServiceNow** části na webu Azure portal.

        * Ujistěte se prosím, která **NameID zásady** je nastavena na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu. 

    * Přejděte dolů k položce **certifikát X.509** vyberte **upravit**.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/tutorial_servicenow_09.png "nakonfigurovat jednotné přihlašování")

    * Vyberte na certifikátu a klikněte na ikonu se šipkou doprava přidejte certifikát

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/tutorial_servicenow_11.png "nakonfigurovat jednotné přihlašování")

    * Klikněte na **Uložit**.

    * Klikněte na **Test připojení** v pravém horním rohu stránky.

        ![Aktivace modulu plug-in](./media/servicenow-tutorial/tutorial_activate2.png "aktivace modulu plug-in")

    * Po kliknutí na **Test připojení**, zobrazí se automaticky otevíraném okně, které je potřeba zadat přihlašovací údaje a pod stránku s výsledky se zobrazí. **Výsledky testů jednotné odhlašování** chyba očekávaná prosím chybu ignorovat a klikněte na tlačítko **aktivovat** tlačítko.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/servicenowactivate.png "nakonfigurovat jednotné přihlašování")
  
7. Pro konfiguraci **ServiceNow** ručně, postupujte následujících kroků:

    * Přihlaste se k ServiceNow aplikaci jako správce.

    * V navigačním podokně na levé straně klikněte na tlačítko **zprostředkovatelé Identity**.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/tutorial_servicenow_07.png "nakonfigurovat jednotné přihlašování")

    * Na **zprostředkovatelé Identity** dialogového okna, klikněte na tlačítko **nový**.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694977.png "nakonfigurovat jednotné přihlašování")

    * Na **zprostředkovatelé Identity** dialogového okna, klikněte na tlačítko **SAML**.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694978.png "nakonfigurovat jednotné přihlašování")

    * Na **Import metadat zprostředkovatele Identity** automaticky otevírané okno, proveďte následující kroky:

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/idp.png "nakonfigurovat jednotné přihlašování")

        * Zadejte **adresa Url federačních metadat aplikace** zkopírovanou z webu Azure portal.

        * Klikněte na **Import**.

    * Načte adresu URL metadat zprostředkovatele identity a naplní všechny informace o pole.

        ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694982.png "nakonfigurovat jednotné přihlašování")

        * V **název** textového pole zadejte název pro vaši konfiguraci (například **Microsoft Azure federované jednotné přihlašování**).

        * Odeberte prosím vyplněná **zprostředkovatele Identity SingleLogoutRequest** hodnotu z textového pole.

        * Kopírování **ServiceNow domovské stránky** hodnota a vložte ji **přihlašovací adresa URL** textového pole v **ServiceNow základní konfiguraci SAML** části na webu Azure portal.

            > [!NOTE]
            > Na domovské stránce instance ServiceNow je tvořen vaše **adresy URL tenanta ServiceNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

        * Kopírování **Entity ID / vystavitele** hodnota a vložte ji **identifikátor** textového pole v **základní konfiguraci SAML ServiceNow** části na webu Azure portal.

        * Ujistěte se prosím, která **NameID zásady** je nastavena na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu.

        * Klikněte na tlačítko **Advanced**. V **pole uživatel** textové pole, typ **e-mailu** nebo **uživatelské_jméno**podle toho, jaké pole se používá pro jednoznačnou identifikaci uživatelů ve vašem nasazení ServiceNow.

            > [!NOTE]
            > Můžete nakonfigurovat služby Azure AD ke generování ID uživatele služby Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML tak, že přejdete **ServiceNow > atributy > Single Sign-On** části webu Azure portal a mapování na požadované pole **nameidentifier** atribut. Hodnota uložená ve službě Azure AD (například hlavní název uživatele) pro vybraný atribut musí odpovídat hodnotě uložené v ServiceNow pro zadané pole (například uživatelské_jméno)

        * Klikněte na **Test připojení** v pravém horním rohu stránky.

        * Po kliknutí na **Test připojení**, zobrazí se automaticky otevíraném okně, které je potřeba zadat přihlašovací údaje a pod stránku s výsledky se zobrazí. **Výsledky testů jednotné odhlašování** chyba očekávaná prosím chybu ignorovat a klikněte na tlačítko **aktivovat** tlačítko.

          ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/servicenowactivate.png "nakonfigurovat jednotné přihlašování")

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurace jednotného přihlašování k Azure AD pro ServiceNow Express

1. V [webu Azure portal](https://portal.azure.com/)na **ServiceNow** integrace stránce aplikace vyberte **Single Sign-On**.

    ![Konfigurace jednotného přihlašování odkazu](common/select-sso.png)

2. Na **vyberte metodu jednotného přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com/navpage.do`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Je potřeba aktualizovat tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Jedním kliknutím konfigurace služby je k dispozici pro ServiceNow tedy bude Azure AD automaticky nakonfigurovat ServiceNow pro ověřování založené na SAML. Chcete-li povolit tuto službu přejděte na **nastavení ServiceNow** klikněte na **zobrazit podrobný** konfigurovat přihlašovací okno.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Zadejte název instance ServiceNow, uživatelské jméno admin a heslo správce **nakonfigurovat přihlašování** formuláři a klikněte na tlačítko **nakonfigurovat nyní**. Všimněte si, že musíte mít uživatelské jméno správce zadané **security_admin** v ServiceNow pro tuto práci přiřazenou roli. V opačném případě ručně nakonfigurovat ServiceNow, které používají Azure AD jako zprostředkovatele Identity SAML, klikněte na **ručně konfigurovat Single Sign-On** a zkopírujte **odhlašovací adresa URL, identifikátor služby Azure AD a adresa URL pro přihlášení** z Rychlý odkaz na oddíl.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/configure.png "konfigurovat adresu URL aplikace")

### <a name="configure-servicenow-express-sso"></a>Konfigurace ServiceNow Express jednotného přihlašování

1. Přihlaste se k ServiceNow Express aplikaci jako správce.

2. V navigačním podokně na levé straně klikněte na tlačítko **Single Sign-On**.

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694980ex.png "konfigurovat adresu URL aplikace")

3. Na **Single Sign-On** dialogového okna, klikněte na ikonu konfigurace v pravém horním rohu a nastavte následující vlastnosti:

    ![Konfigurovat adresy URL aplikace](./media/servicenow-tutorial/ic7694981ex.png "konfigurovat adresu URL aplikace")

    a. Přepnout **povolit více poskytovatele jednotného přihlašování** vpravo.

    b. Přepnout **protokolování pro více zprostředkovatele integraci jednotného přihlašování k povolení ladění** vpravo.

    c. V **pole na uživatele, který tabulky...**  textové pole, typ **uživatelské_jméno**.

4. Na **Single Sign-On** dialogového okna, klikněte na tlačítko **přidat nový certifikát**.

    ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694973ex.png "nakonfigurovat jednotné přihlašování")

5. Na **certifikáty X.509** dialogového okna, proveďte následující kroky:

    ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694975.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **TestSAML2.0**).

    b. Vyberte **aktivní**.

    c. Jako **formátu**vyberte **PEM**.

    d. Jako **typ**vyberte **důvěřovat certifikátu Store**.

    e. Váš certifikát kódovaný v Base64 stáhnout z webu Azure portal v poznámkovém bloku otevřete, zkopírujte obsah ho do schránky a vložte ho do **PEM certifikátu** textového pole.

    f. Klikněte na tlačítko **aktualizace**

6. Na **Single Sign-On** dialogového okna, klikněte na tlačítko **přidat nového zprostředkovatele identity**.

    ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694976ex.png "nakonfigurovat jednotné přihlašování")

7. Na **přidat nového zprostředkovatele Identity** dialogového okna, v části **konfigurace zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694982ex.png "nakonfigurovat jednotné přihlašování")

    a. V **název** textového pole zadejte název pro vaši konfiguraci (například: **SAML 2.0**).

    b. V **adresa URL zprostředkovatele Identity** pole, vložte hodnotu **ID zprostředkovatele Identity**, který jste zkopírovali z portálu Azure portal.

    c. V **zprostředkovatele Identity AuthnRequest** pole, vložte hodnotu **adresy URL požadavku ověřování**, který jste zkopírovali z portálu Azure portal.

    d. V **zprostředkovatele Identity SingleLogoutRequest** pole, vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal

    e. Jako **certifikát poskytovatele Identity**, vyberte certifikát, který jste vytvořili v předchozím kroku.

8. Klikněte na tlačítko **Upřesnit nastavení**a v části **další vlastnosti zprostředkovatele Identity**, proveďte následující kroky:

    ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694983ex.png "nakonfigurovat jednotné přihlašování")

    a. V **protokol vazby pro zprostředkovatele identity SingleLogoutRequest** textové pole, typ **urn: oasis: názvy: tc: SAML:2.0:bindings:HTTP – přesměrování**.

    b. V **NameID zásady** textové pole, typ **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: neurčené**.

    c. V **AuthnContextClassRef metoda**, typ `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Zrušte výběr **vytvořit AuthnContextClass**.

9. V části **další vlastnosti zprostředkovatele služby**, proveďte následující kroky:

    ![Konfigurovat Single Sign-On](./media/servicenow-tutorial/ic7694984ex.png "nakonfigurovat jednotné přihlašování")

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

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon používat Azure Single Sign-On díky udělení přístupu k ServiceNow.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **ServiceNow**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-servicenow-test-user"></a>Vytvořit testovacího uživatele ServiceNow

Cílem této části je vytvořte uživatele Britta Simon v ServiceNow. ServiceNow podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](servicenow-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory klienta ServiceNow](https://www.servicenow.com/support/contact-support.html)

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice ServiceNow na přístupovém panelu, vám by měl být automaticky přihlášeni k ServiceNow, pro kterou můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Test jednotného přihlašování pro ServiceNow Classic (mobilní verze)

1. Otevřete váš **ServiceNow Classic (mobilní)** aplikace a proveďte následující kroky:

    a. Klikněte na **přidat** symbol dole na obrazovce.

    ![Přihlášení](./media/servicenow-tutorial/test03.png)

    b. Zadejte název Instance ServiceNow a klikněte na tlačítko **pokračovat**.

    ![Přihlášení](./media/servicenow-tutorial/test04.png)

    c. Na **přihlášení** obrazovky, proveďte následující kroky:

    ![Přihlášení](./media/servicenow-tutorial/test01.png)

    *  Typ **uživatelské jméno** jako B.simon@contoso.com.

    *  Klikněte na **externí přihlášení pomocí** a budete přesměrováni na stránku služby Azure AD pro přihlášení.
    
    *  Zadejte svoje přihlašovací údaje a pokud neexistuje žádné ověřování třetích stran nebo jakékoli jiné funkce zabezpečení, povolený a uživatel bude mít odpovídajícím způsobem reagovat a aplikace **domovskou stránku** se zobrazí, jak je znázorněno níže:

        ![Na domovské stránce](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](servicenow-provisioning-tutorial.md)