---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s ServiceNow | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ServiceNow.
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
ms.date: 08/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431d20c4c5ae5355d456ca3453b832e590cbb199
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69558944"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s ServiceNow

V tomto kurzu se dozvíte, jak integrovat ServiceNow s Azure Active Directory (Azure AD). Když integrujete ServiceNow s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ServiceNow.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ServiceNow svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ServiceNow odběr s povoleným jednotným přihlašováním (SSO).
* Pro ServiceNow, instanci nebo tenant ServiceNow, verze Calgary nebo vyšší
* Pro ServiceNow Express je to instance ServiceNow Express, Helsinky verze nebo vyšší.
* Tenant ServiceNow musí mít povolený [modul plug-in jednotného přihlašování více zprostředkovatelů](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) . To se dá udělat [odesláním žádosti o služby](https://hi.service-now.com).
* Pro automatickou konfiguraci povolte modul plug-in více zprostředkovatelů pro ServiceNow.
* Pokud chcete nainstalovat aplikaci ServiceNow Classic (Mobile), musíte přejít na příslušné úložiště a vyhledat aplikaci ServiceNow Classic a kliknout na stáhnout.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. ServiceNow podporuje jednotné přihlašování spouštěné v **SP** a podporuje [ **automatizované** zřizování uživatelů](servicenow-provisioning-tutorial.md).

ServiceNow Classic (mobilní) aplikace se teď dají nakonfigurovat pomocí Azure AD pro povolení jednotného přihlašování a podporuje i uživatele pro **Android** i **iOS** . V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

## <a name="adding-servicenow-from-the-gallery"></a>Přidání ServiceNow z Galerie

Pokud chcete nakonfigurovat integraci ServiceNow do služby Azure AD, musíte přidat ServiceNow z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ServiceNow** .
1. Na panelu výsledků vyberte **ServiceNow** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ServiceNow

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ServiceNow pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ServiceNow.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ServiceNow, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
    1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD pro ServiceNow Express](#configure-azure-ad-sso-for-servicenow-express)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte ServiceNow](#configure-servicenow)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte uživatele ServiceNow test](#create-servicenow-test-user)** , který bude mít protějšek B. Simon v ServiceNow, který je propojený s reprezentací uživatele Azure AD.
    1. **[Nakonfigurujte ServiceNow Express SSO](#configure-servicenow-express-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.    
3. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.
4. **[Otestujte jednotné přihlašování pro ServiceNow Classic (mobilní zařízení)](#test-sso-for-servicenow-classic-mobile)** , abyste ověřili, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ServiceNow** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<instance-name>.service-now.com/navpage.do`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty je potřeba aktualizovat pomocí skutečné přihlašovací adresy URL a identifikátoru, který je vysvětlen dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

   a. Kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a vložte ji do poznámkového bloku, protože tato adresa URL federačních metadat aplikace se použije později v tomto kurzu.

    b. Kliknutím na **Stáhnout** stáhněte **certifikát (Base64)** a pak soubor certifikátu uložte do počítače.

1. V části **Nastavení ServiceNow** zkopírujte na základě vašeho požadavku příslušné adresy URL.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ServiceNow.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ServiceNow**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>Konfigurace jednotného přihlašování služby Azure AD pro ServiceNow Express

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ServiceNow** vyberte **jednotné přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<instance-name>.service-now.com/navpage.do`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`https://<instance-name>.service-now.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Tyto hodnoty je potřeba aktualizovat pomocí skutečné přihlašovací adresy URL a identifikátoru, který je vysvětlen dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Pro ServiceNow, aby služba Azure AD automaticky nakonfigurovala ServiceNow pro ověřování založené na SAML, je k dispozici jedna kliknutí konfigurace služby. Pokud chcete povolit tuto službu, přejděte do části **Nastavení ServiceNow** a kliknutím na **Zobrazit podrobné pokyny** otevřete okno Konfigurovat přihlašování.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. Ve formuláři **Konfigurovat přihlašování** zadejte název instance ServiceNow, uživatelské jméno správce a heslo správce a klikněte na **Konfigurovat hned**. Ujistěte se, že zadané uživatelské jméno správce musí mít přiřazenou roli **SECURITY_ADMIN** v ServiceNow, aby to fungovalo. Jinak, pokud chcete ručně nakonfigurovat ServiceNow pro použití Azure AD jako poskytovatele identity SAML, klikněte na **ručně konfigurovat jednotné přihlašování** a zkopírujte **adresu URL pro odhlášení, identifikátor Azure AD a přihlašovací adresu URL** z oddílu rychlé reference.

    ![Konfigurace adresy URL aplikace](./media/servicenow-tutorial/configure.png "Konfigurace adresy URL aplikace")

## <a name="configure-servicenow"></a>Konfigurace ServiceNow

1. Přihlaste se k aplikaci ServiceNow jako správce.

2. Aktivujte integraci – modul plug-in **instalačního programu jednotného přihlašování s více zprostředkovateli** pomocí následujícího postupu:

    a. V navigačním podokně na levé straně, vyhledejte v části **definice systému** z panelu hledání a pak klikněte na **moduly plug-in**.

    ![Aktivovat modul plug-in](./media/servicenow-tutorial/tutorial_servicenow_03.png "Aktivovat modul plug-in")

    b. Vyhledat **integraci – instalační program jednotného přihlašování s více poskytovateli**

     ![Aktivovat modul plug-in](./media/servicenow-tutorial/tutorial_servicenow_04.png "Aktivovat modul plug-in")

    c. Vyberte modul plug-in. Klikněte pravým tlačítkem a vyberte **aktivovat nebo upgradovat**.

     ![Aktivovat modul plug-in](./media/servicenow-tutorial/tutorial_activate.png "Aktivovat modul plug-in")

    d. Klikněte na tlačítko **aktivovat** .

     ![Aktivovat modul plug-in](./media/servicenow-tutorial/tutorial_activate1.png "Aktivovat modul plug-in")

3. V navigačním podokně na levé straně vyhledejte část **jednotného přihlašování k více zprostředkovatelům** z panelu hledání a pak klikněte na **vlastnosti**.

    ![Konfigurace adresy URL aplikace](./media/servicenow-tutorial/tutorial_servicenow_06.png "Konfigurace adresy URL aplikace")

4. V dialogu **vlastnosti jednotného přihlašování více zprostředkovatelů** proveďte následující kroky:

    ![Konfigurace adresy URL aplikace](./media/servicenow-tutorial/ic7694981.png "Konfigurace adresy URL aplikace")

    * Pokud chcete **Povolit vícenásobné přihlašování více zprostředkovatelů**, vyberte **Ano**.
  
    * Jako **Povolit automatický import uživatelů ze všech zprostředkovatelů identity do tabulky uživatelů**vyberte **Ano**.

    * Jako **Povolit protokolování ladění pro integraci více zprostředkovatelů SSO**vyberte **Ano**.

    * Do **pole v tabulce uživatel** zadejte **uživatelské_jméno**.
  
    * Klikněte na **Uložit**.

5. Existují dva způsoby, jak lze **ServiceNow** nakonfigurovat – automatické a ruční.

6. Pro automatické nakonfigurování **ServiceNow** postupujte takto:

    * Vraťte se na **ServiceNow** stránku jednotného přihlašování v Azure Portal.

    * Pro ServiceNow, aby služba Azure AD automaticky nakonfigurovala ServiceNow pro ověřování založené na SAML, je k dispozici jedna kliknutí konfigurace služby. Pokud chcete povolit tuto službu, přejděte do části **Konfigurace ServiceNow** a kliknutím na **Konfigurovat ServiceNow** otevřete okno Konfigurovat přihlašování.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * Ve formuláři **Konfigurovat přihlašování** zadejte název instance ServiceNow, uživatelské jméno správce a heslo správce a klikněte na **Konfigurovat hned**. Ujistěte se, že zadané uživatelské jméno správce musí mít přiřazenou roli **SECURITY_ADMIN** v ServiceNow, aby to fungovalo. Jinak, pokud chcete ručně nakonfigurovat ServiceNow pro použití Azure AD jako poskytovatele identity SAML, klikněte na **ručně nakonfigurovat jednotné přihlašování** a ZKOPÍRUJTE adresu URL pro odhlášení **, ID entity SAML a adresu URL služby jednotného přihlašování SAML** z oddílu rychlé reference.

        ![Konfigurace adresy URL aplikace](./media/servicenow-tutorial/configure.png "Konfigurace adresy URL aplikace")

    * Přihlaste se k aplikaci ServiceNow jako správce.

    * V případě automatické konfigurace jsou všechna potřebná nastavení nakonfigurovaná na straně **ServiceNow** , ale ve výchozím nastavení není **certifikát X. 509** povolený. Musíte ho ručně namapovat na svého poskytovatele identity v ServiceNow. Postupujte podle následujících kroků pro stejné:

    * V navigačním podokně na levé straně vyhledejte část **jednotného přihlašování k více zprostředkovatelům** z panelu hledání a pak klikněte na **Zprostředkovatelé identity**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurace jednotného přihlašování")

    * Klikněte na automaticky generovaný zprostředkovatel identity.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_08.png "Konfigurace jednotného přihlašování")

    *  V části **zprostředkovatel identity** proveďte následující kroky:

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/automatic_config.png "Konfigurace jednotného přihlašování")

        * Do textového pole **název** zadejte název vaší konfigurace (například **Microsoft Azure federované jednotné přihlašování**).

        * Odeberte prosím hodnotu SingleLogoutRequest vyplněné **identity poskytovatele identity** z textového pole.

        * Zkopírujte hodnotu **domovské stránky ServiceNow** a vložte ji do textového pole **přihlašovací adresa URL** v **ServiceNow Basic konfiguračního oddílu SAML** na Azure Portal.

            > [!NOTE]
            > Domovská stránka instance ServiceNow je zřetězením **adresy URL vašeho tenanta ServiceNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

        * Zkopírujte hodnotu **ID nebo vystavitele entity** a vložte ji do textového pole **identifikátor** v části **Konfigurace SAML ServiceNow Basic** v tématu Azure Portal.

        * Ujistěte se prosím, že je **zásada NameId** nastavená na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu. 

    * Posuňte se dolů k části **certifikát X. 509** a vyberte **Upravit**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_09.png "Konfigurace jednotného přihlašování")

    * Vyberte certifikát a kliknutím na ikonu se šipkou vpravo přidejte certifikát.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_11.png "Konfigurace jednotného přihlašování")

    * Klikněte na **Uložit**.

    * V pravém horním rohu stránky klikněte na **Test připojení** .

        ![Aktivovat modul plug-in](./media/servicenow-tutorial/tutorial_activate2.png "Aktivovat modul plug-in")

    * Po kliknutí na **Test připojení**se zobrazí místní okno, kde je třeba zadat přihlašovací údaje, a zobrazí se následující stránka s výsledky. Očekává se chyba **odhlášení SSO výsledky testů** . ignorujte prosím tuto chybu a klikněte na tlačítko **aktivovat** .

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/servicenowactivate.png "Konfigurace jednotného přihlašování")
  
7. Pro ruční konfiguraci **ServiceNow** postupujte takto:

    * Přihlaste se k aplikaci ServiceNow jako správce.

    * V navigačním podokně na levé straně klikněte na zprostředkovatelé **identity**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/tutorial_servicenow_07.png "Konfigurace jednotného přihlašování")

    * V dialogu **poskytovatelé identity** klikněte na **Nový**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694977.png "Konfigurace jednotného přihlašování")

    * V dialogu **poskytovatelé identity** klikněte na **SAML**.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694978.png "Konfigurace jednotného přihlašování")

    * V místní nabídce **metadat zprostředkovatele identity import** proveďte následující kroky:

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/idp.png "Konfigurace jednotného přihlašování")

        * Zadejte **adresu URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.

        * Klikněte na **Import**.

    * Přečte adresu URL metadat IdP a vyplní všechny informace o polích.

        ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694982.png "Konfigurace jednotného přihlašování")

        * Do textového pole **název** zadejte název vaší konfigurace (například **Microsoft Azure federované jednotné přihlašování**).

        * Odeberte prosím hodnotu SingleLogoutRequest vyplněné **identity poskytovatele identity** z textového pole.

        * Zkopírujte hodnotu **domovské stránky ServiceNow** a vložte ji do textového pole **přihlašovací adresa URL** v **ServiceNow Basic konfiguračního oddílu SAML** na Azure Portal.

            > [!NOTE]
            > Domovská stránka instance ServiceNow je zřetězením **adresy URL vašeho tenanta ServiceNow** a **/navpage.do** (například:`https://fabrikam.service-now.com/navpage.do`).

        * Zkopírujte hodnotu **ID nebo vystavitele entity** a vložte ji do textového pole **identifikátor** v části **Konfigurace SAML ServiceNow Basic** v tématu Azure Portal.

        * Ujistěte se prosím, že je **zásada NameId** nastavená na `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` hodnotu.

        * Klikněte na tlačítko **Advanced**. Do textového **pole uživatel** zadejte **e-mail** nebo **uživatelské_jméno**v závislosti na tom, které pole se používá k jednoznačné identifikaci uživatelů v nasazení ServiceNow.

            > [!NOTE]
            > Službu Azure AD můžete nakonfigurovat tak, aby vygenerovala ID uživatele Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML, a to tak, že se vrátí do části **ServiceNow > atributy > jednotné přihlašování** Azure Portal a namapuje se požadované pole pro atribut **NameIdentifier** Hodnota uložená pro vybraný atribut ve službě Azure AD (například hlavní název uživatele) se musí shodovat s hodnotou uloženou v ServiceNow pro zadané pole (například Uživatelské_jméno).

        * V pravém horním rohu stránky klikněte na **Test připojení** .

        * Po kliknutí na **Test připojení**se zobrazí místní okno, kde je třeba zadat přihlašovací údaje, a zobrazí se následující stránka s výsledky. Očekává se chyba **odhlášení SSO výsledky testů** . ignorujte prosím tuto chybu a klikněte na tlačítko **aktivovat** .

          ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/servicenowactivate.png "Konfigurace jednotného přihlašování")

### <a name="create-servicenow-test-user"></a>Vytvořit testovacího uživatele ServiceNow

Cílem této části je vytvořit uživatele s názvem Britta Simon v ServiceNow. ServiceNow podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](servicenow-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory klienta ServiceNow](https://www.servicenow.com/support/contact-support.html) .

### <a name="configure-servicenow-express-sso"></a>Konfigurace jednotného přihlašování ServiceNow Express

1. Přihlaste se k aplikaci ServiceNow Express jako správce.

2. V navigačním podokně na levé straně klikněte na **jednotné přihlašování**.

    ![Konfigurace adresy URL aplikace](./media/servicenow-tutorial/ic7694980ex.png "Konfigurace adresy URL aplikace")

3. V dialogovém okně **jednotného přihlašování** klikněte na ikonu konfigurace v pravém horním rohu a nastavte následující vlastnosti:

    ![Konfigurace adresy URL aplikace](./media/servicenow-tutorial/ic7694981ex.png "Konfigurace adresy URL aplikace")

    a. Přepněte na právo **Povolit jednotné přihlašování pro více zprostředkovatelů** .

    b. Přepněte **možnost povolit protokolování ladění pro integraci více zprostředkovatelů jednotného PŘIhlašování** napravo.

    c. Do **pole v tabulce uživatel** zadejte **uživatelské_jméno**.

4. V dialogovém okně **jednotného přihlašování** klikněte na **Přidat nový certifikát**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694973ex.png "Konfigurace jednotného přihlašování")

5. V dialogovém okně **certifikáty X. 509** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694975.png "Konfigurace jednotného přihlašování")

    a. Do textového pole **název** zadejte název vaší konfigurace (například: **TestSAML2.0**).

    b. Vyberte **aktivní**.

    c. Jako **Formát**vyberte **PEM**.

    d. Jako **typ**vyberte **certifikát důvěryhodného úložiště**.

    e. Otevřete certifikát kódovaný v kódování Base64 stažený z Azure Portal v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikátu PEM** .

    f. Kliknout na **aktualizovat**

6. V dialogovém okně **jednotného přihlašování** klikněte na **Přidat nový IDP**.

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694976ex.png "Konfigurace jednotného přihlašování")

7. V dialogovém okně **Přidat nového zprostředkovatele identity** v části **Konfigurovat zprostředkovatele identity**proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694982ex.png "Konfigurace jednotného přihlašování")

    a. Do textového pole **název** zadejte název vaší konfigurace (například: **SAML 2,0**).

    b. Do pole **Adresa URL poskytovatele identity** vložte hodnotu **ID zprostředkovatele identity**, kterou jste zkopírovali z Azure Portal.

    c. Do pole **AuthnRequest zprostředkovatele identity** vložte hodnotu **URL žádosti o ověření**, kterou jste zkopírovali z Azure Portal.

    d. Do pole **SingleLogoutRequest zprostředkovatele identity** vložte hodnotu **URL**pro odhlášení, kterou jste zkopírovali z Azure Portal

    e. Jako **certifikát poskytovatele identity**vyberte certifikát, který jste vytvořili v předchozím kroku.

8. Klikněte na **Upřesnit nastavení**a v části **Další vlastnosti zprostředkovatele identity**proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694983ex.png "Konfigurace jednotného přihlašování")

    a. V poli **vazba protokolu pro textové pole SINGLELOGOUTREQUEST IDP** zadejte **název urn: Oasis: names: TC: SAML: 2.0: vazby: http-redirect**.

    b. Do textového pole **zásady NameId** zadejte **urn: Oasis: names: TC: SAML: 1.1: NameId-Format: unspecifikovan**.

    c. Do **metody AuthnContextClassRef**zadejte `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`.

    d. Zrušte výběr **vytvořit AuthnContextClass**.

9. V části **Další vlastnosti poskytovatele služeb**proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/servicenow-tutorial/ic7694984ex.png "Konfigurace jednotného přihlašování")

    a. Do textového pole **domovské stránky ServiceNow** zadejte adresu URL domovské stránky instance ServiceNow.

    > [!NOTE]
    > Domovská stránka instance ServiceNow je zřetězením **adresy URL vašeho tenanta ServiceNow** a **/navpage.do** (například: `https://fabrikam.service-now.com/navpage.do`).

    b. Do textového pole **ID entity/** vystavitele zadejte adresu URL vašeho tenanta ServiceNow.

    c. Do textového pole **identifikátor URI cílové skupiny** zadejte adresu URL vašeho tenanta ServiceNow.

    d. Do textového pole **časové zkosení** , zadejte **60**.

    e. Do textového **pole uživatel** zadejte **e-mail** nebo **uživatelské_jméno**v závislosti na tom, které pole se používá k jednoznačné identifikaci uživatelů v nasazení ServiceNow.

    > [!NOTE]
    > Službu Azure AD můžete nakonfigurovat tak, aby vygenerovala ID uživatele Azure AD (hlavní název uživatele) nebo e-mailovou adresu jako jedinečný identifikátor v tokenu SAML, a to tak, že se vrátí do části **ServiceNow > atributy > jednotné přihlašování** Azure Portal a namapuje se požadované pole pro atribut **NameIdentifier** Hodnota uložená pro vybraný atribut ve službě Azure AD (například hlavní název uživatele) se musí shodovat s hodnotou uloženou v ServiceNow pro zadané pole (například Uživatelské_jméno).

    f. Klikněte na **Uložit**.

## <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici ServiceNow, měli byste se automaticky přihlásit k ServiceNow, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-servicenow-classic-mobile"></a>Test jednotného přihlašování pro ServiceNow Classic (mobilní zařízení)

1. Otevřete klasickou aplikaci v **ServiceNow (Mobile)** a proveďte následující kroky:

    a. Klikněte na symbol **Přidání** pod obrazovkou.

    ![Přihlášení](./media/servicenow-tutorial/test03.png)

    b. Zadejte název instance ServiceNow a klikněte na **pokračovat**.

    ![Přihlášení](./media/servicenow-tutorial/test04.png)

    c. Na obrazovce **přihlášení** proveďte následující kroky:

    ![Přihlášení](./media/servicenow-tutorial/test01.png)

    *  Zadejte **uživatelské jméno** , jako B.simon@contoso.comje.

    *  Klikněte na **použít externí přihlášení** a při přihlašování budete přesměrováni na stránku Azure AD.
    
    *  Zadejte svoje přihlašovací údaje, a pokud je povolené nějaké ověřování třetí strany nebo jakákoli jiná funkce zabezpečení, bude uživatel muset odpovídajícím způsobem odpovědět a zobrazí se **Domovská stránka** aplikace, jak je znázorněno níže:

        ![Domovská stránka](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](servicenow-provisioning-tutorial.md)

- [Vyzkoušejte si ServiceNow s Azure AD](https://aad.portal.azure.com)