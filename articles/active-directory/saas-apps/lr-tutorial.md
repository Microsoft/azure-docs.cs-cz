---
title: 'Kurz: Azure Active Directory integrace s LoginRadius | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LoginRadius.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606415"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Kurz: Azure Active Directory integrace s LoginRadius

V tomto kurzu se dozvíte, jak integrovat LoginRadius s Azure Active Directory (Azure AD).

Integrace LoginRadius s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k LoginRadius.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k LoginRadius (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s LoginRadius potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným LoginRadiusm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LoginRadius podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-loginradius-from-the-gallery"></a>Přidání LoginRadius z Galerie

Pokud chcete nakonfigurovat integraci LoginRadius do služby Azure AD, musíte přidat LoginRadius z Galerie do svého seznamu spravovaných aplikací SaaS.

**Přidání LoginRadius z Galerie:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu vyberte ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. V části **podnikové aplikace** vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** :

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LoginRadius**, v panelu výsledek vyberte **LoginRadius** a pak kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![LoginRadius v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí LoginRadius na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v LoginRadius.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí LoginRadius, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování LoginRadius](#configure-loginradius-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření LoginRadius Test User](#create-loginradius-test-user)** – pro Britta Simon v LoginRadius, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí LoginRadius, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **LoginRadius** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V podokně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** vyberte ikonu **Upravit** a otevřete tak **základní podokno konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. **Základní konfigurační oddíl SAML** :

   ![Informace o jednotném přihlašování v doméně LoginRadius a adresách URL](common/sp-identifier.png)

   1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL. `https://secure.loginradius.com/login`

   1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL. `https://lr.hub.loginradius.com/`

   1. Do textového pole **Adresa URL odpovědi (adresa URL služby vyhodnocení zákazníka)** zadejte adresu URL služby LoginRadius ACS. `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` 

5. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení LoginRadius** zkopírujte příslušné adresy URL podle vašich požadavků.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

   - Přihlašovací adresa URL

   - Identifikátor Azure AD

   - Odhlašovací adresa URL

## <a name="configure-loginradius-single-sign-on"></a>Nakonfigurovat LoginRadius jednu Sign-On

V této části povolíte jednotné přihlašování Azure AD v konzole pro správu LoginRadius.

1. Přihlaste se ke svému účtu [konzoly správce](https://adminconsole.loginradius.com/login) LoginRadius.

2. V konzole pro správu [LoginRadius](https://secure.loginradius.com/account/team)otevřete oddíl **Správa týmu** .

3. Vyberte kartu **jednotné přihlašování** a pak vyberte **Azure AD**:

   ![Snímek obrazovky, který zobrazuje nabídku jednotného přihlašování v konzole pro správu LoginRadius Team](./media/loginradius-tutorial/azure-ad.png)
4. Na stránce nastavení služby Azure AD proveďte následující kroky:

   ![Snímek obrazovky zobrazující konfiguraci Azure Active Directory v konzole pro správu týmu LoginRadius](./media/loginradius-tutorial/single-sign-on.png)

    1. V **umístění poskytovatel ID** Zadejte koncový bod přihlášení, který získáte z účtu Azure AD.

    1. V poli **Adresa URL pro odhlášení zprostředkovatele ID** Zadejte koncový bod odhlášení, který získáte z účtu služby Azure AD.
 
    1. V části **certifikát poskytovatele ID** zadejte certifikát Azure AD, který získáte z účtu služby Azure AD. Zadejte hodnotu certifikátu s hlavičkou a zápatím. Příklad: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Do klíče certifikát **poskytovatele služeb** a **klíč certifikátu poskytovatele serveru** zadejte svůj certifikát a klíč. 

       Certifikát podepsaný svým držitelem můžete vytvořit tak, že na příkazovém řádku (Linux/Mac) spustíte následující příkazy:

       - Příkaz pro získání klíče certifikátu pro SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Příkaz pro získání certifikátu pro SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Nezapomeňte zadat certifikát a hodnoty klíčů certifikátu s hlavičkou a zápatím:
       > - Příklad formátu hodnoty certifikátu: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Formát ukázkové hodnoty klíče certifikátu: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. V části **mapování dat** vyberte pole (SP pole) a zadejte odpovídající pole Azure AD (pole IDP).

    Níže jsou uvedené názvy polí pro Azure AD.

    | Pole    | Klíč profilu                                                          |
    | --------- | -------------------------------------------------------------------- |
    | E-mail     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Mapování polí **e-mailu** je povinné. Mapování polí **FirstName** a **LastName** jsou volitelná.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele s názvem Britta Simon v Azure Portal.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. V části vlastnosti **uživatele** proveďte následující kroky.

   ![Uživatelský dialog](common/user-properties.png)

   1. Do pole **název** zadejte **BrittaSimon**.
  
   1. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například, BrittaSimon@contoso.com.

   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .

   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k LoginRadius.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **LoginRadius**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **LoginRadius**.

    ![Odkaz LoginRadius v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Vyberte tlačítko **Přidat uživatele** a pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V podokně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** položku **Britta Simon** a pak zvolte tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v podokně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.

7. V podokně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

### <a name="create-loginradius-test-user"></a>Vytvořit testovacího uživatele LoginRadius

1. Přihlaste se ke svému účtu [konzoly správce](https://adminconsole.loginradius.com/login) LoginRadius.

2. V konzole pro správu LoginRadius otevřete oddíl Správa týmu.

   ![Snímek obrazovky, který zobrazuje konzolu pro správu LoginRadius](./media/loginradius-tutorial/team-management.png)
3. V nabídce na straně vyberte **Přidat člena týmu** a otevřete formulář. 

4. Ve formuláři **Přidat člena týmu** vytvoříte uživatele s názvem Britta Simon na webu LoginRadius zadáním podrobností uživatele a přiřazením oprávnění, která má uživatel mít. Další informace o oprávněních na základě rolí naleznete v části [oprávnění k přístupu role](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) v dokumentu Správa LoginRadius [Spravovat členy týmu](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

1. V prohlížeči https://accounts.loginradius.com/auth.aspx Zvolte a vyberte **zaznamenání přihlášení SSO**.
2. Zadejte název aplikace LoginRadius a pak vyberte **Přihlásit**.
3. Mělo by se otevřít automaticky otevírané okno s výzvou, abyste se přihlásili ke svému účtu služby Azure AD.
4. Po ověření se místní okno zavře a budete přihlášeni ke konzole pro správu LoginRadius.

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
