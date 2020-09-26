---
title: 'Kurz: Azure Active Directory integrace s jednotným přihlašováním pomocí F5 | Microsoft Docs'
description: V tomto článku se dozvíte o krocích, které je třeba provést při integraci F5 s Azure Active Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: cbcbcb6a649969c5348c3ad445ff43f10372faeb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306153"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Kurz: Integrace jednotného přihlašování (SSO) Azure Active Directory (AD) s F5

V tomto kurzu se naučíte integrovat F5 s Azure Active Directory (Azure AD). Při integraci F5 se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k F5.
* Umožněte uživatelům, aby se automaticky přihlásili k F5 pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

F5 podporuje **SP a IDP** iniciované jednotné přihlašování.

Jednotné přihlašování (SSO) F5 se dá nakonfigurovat třemi různými způsoby:

- [Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](headerf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Přidávání F5 z Galerie

Pokud chcete nakonfigurovat integraci F5 na Azure AD, musíte přidat F5 z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole klávesu **F5** .
1. Vyberte **F5** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurace a testování jednotného přihlašování Azure AD pro F5

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí F5 s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v F5.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí F5, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte F5-SSO](#configure-f5-sso)** -pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele F5](#create-f5-test-user)** , aby měl protějšek B. Simon v F5, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikací **F5** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<YourCustomFQDN>.f5.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<YourCustomFQDN>.f5.com/`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory F5 pro klienty](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavit na F5** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k F5.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **F5**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-f5-sso"></a>Konfigurace F5 SSO

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](headerf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Konfigurace jednotného přihlašování F5 pro pokročilou aplikaci Kerberos

1. Otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti F5 (rozšířený protokol Kerberos) jako správce a proveďte následující kroky:

1. Certifikát metadat musíte importovat do F5 (rozšířený protokol Kerberos), který se použije později v procesu instalace. V **seznamu certifikátů protokolu SSL > Správa certifikátů v systému > Správa certifikátů >> **. Klikněte na **Import** pravého rohu.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Pokud chcete nastavit IDP SAML, přejděte na **přístup > federace > poskytovatele služeb saml > vytvořit > z metadat**.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure02.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure04.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Určení certifikátu nahraného z úlohy 3

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure06.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure07.png)

 1. Pokud chcete nastavit službu SAML SP, přejděte k části **přístup > federaci > federaci služby SAML > místní služby SP > vytvořit**.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klikněte na **OK**.

1. Vyberte konfiguraci SP a klikněte na **vytvořit vazbu nebo zrušit vazbu konektorů IDP**.

     ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klikněte na **Přidat nový řádek** a vyberte **externí konektor IDP** vytvořený v předchozím kroku.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Pro konfiguraci protokolu Kerberos SSO **přístup > jednotného přihlašování > Kerberos**

    >[!Note]
    >Budete potřebovat vytvořit a zadat účet delegování protokolu Kerberos. Odkazování na oddíl KCD (viz příloha odkazy na proměnné)

    • Uživatelské jméno zdroje  `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Zdroj sféry uživatele  `session.logon.last.domain`

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure11.png)

1. Pro konfiguraci přístupového profilu přístup **> profilování a zásady > přístup k profilu (zásady pro relace)**.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure12.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure13.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure14.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure15.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure16.png)
 
    * Session. Logon. Last. usernameUPN expr {[mcget {Session. SAML. Last. identity}]}

    * Session. AD. lastactualdomain TEXT demo. Live

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName =% {Session. Logon. Last. usernameUPN})

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure18.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure19.png)

    * Session. Logon. Last. username expr {"[mcget {Session. AD. Last. attr. sAMAccountName}]"}

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {Session. Logon. Last. username}
    * mcget {Session. Logon. Last. Password}

1. Pokud chcete přidat nový uzel, přejít na **místní provoz > uzly > seznam uzlů > +**.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Pokud chcete vytvořit nový fond, přejdete na **místní provoz > fondy > seznam fondů > vytvořit**.

     ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure22.png)

 1. Pokud chcete vytvořit nový virtuální server, přečtěte si **místní provoz > virtuální servery > seznam virtuálních serverů > +**.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure23.png)

1. Zadejte profil přístupu vytvořený v předchozím kroku.

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Nastavení delegování protokolu Kerberos 

>[!Note]
>Další podrobnosti najdete [tady](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) .

* **Krok 1: vytvoření účtu delegování**

    * Příklad
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Krok 2: nastavení hlavního názvu služby (SPN) (na účtu delegování APM)**

    *  Příklad
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Krok 3: delegování hlavního názvu služby (pro účet App Service)**

    * Nastavte příslušné delegování pro účet delegování F5.
    * V následujícím příkladu je účet delegování APM nakonfigurovaný pro KCD pro FRP-app1. demo App. Live.

        ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure25.png)

1. Zadejte podrobnosti uvedené [výše v dokumentu](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) odkazu.

1. Příloha-SAML-F5 mapování proměnných BIG-IP uvedená níže:

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure26.png)

    ![F5 (rozšířený protokol Kerberos) – konfigurace](./media/advance-kerbf5-tutorial/configure27.png) 

1. Níže je uveden celý seznam výchozích atributů SAML. Zadaný řetězec je reprezentován pomocí následujícího řetězce.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Relace | Atribut |
| -- | -- |
| eb46b6b6. Session. SAML. Last. assertionID | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssueInstant  | `<ID>` |
| eb46b6b6. Session. SAML. Last. assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/Claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/identity/claims/DisplayName | user0 |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/identity/claims/IdentityProvider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/identity/claims/ObjectIdentifier | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.Microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/identity/claims/givenName | user0 |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/identity/claims/Name | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. attr. Name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6. Session. SAML. Last. cílová skupina | `https://kerbapp.superdemo.live` |
| eb46b6b6. Session. SAML. Last. authNContextClassRef | urn: Oasis: names: TC: SAML: 2.0: AC: Classes: Password |
| eb46b6b6. Session. SAML. Last. authNInstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. identity | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. inResponseTo | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. nameIDValue | `user0@superdemo.live` |
| eb46b6b6. Session. SAML. Last. nameIdFormat | urn: Oasis: names: TC: SAML: 1.1: NameId-Format: emailAddress |
| eb46b6b6. Session. SAML. Last. responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. responseId | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. responseIssueInstant | `<ID>` |
| eb46b6b6. Session. SAML. Last. responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6. Session. SAML. Last. Result | 1 |
| eb46b6b6. Session. SAML. Last. samlVersion | 2,0 |
| eb46b6b6. Session. SAML. Last. sessionIndex | `<TENANT ID>` |
| eb46b6b6. Session. SAML. Last. statusValue | urn: Oasis: names: TC: SAML: 2.0: stav: úspěch |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6. Session. SAML. Last. subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6. Session. SAML. Last. subjectConfirmMethod | urn: Oasis: names: TC: SAML: 2.0: cm: nosič |
| eb46b6b6. Session. SAML. Last. validityNotBefore | `<ID>` |
| eb46b6b6. Session. SAML. Last. validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Vytvořit testovacího uživatele F5

V této části vytvoříte na F5 uživatele s názvem B. Simon. Pokud chcete přidat uživatele na platformě F5, pracujte s nástrojem [F5 Client Support Team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele. 

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici F5 na přístupovém panelu byste měli být automaticky přihlášeni k F5, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Zkuste F5 pomocí Azure AD](https://aad.portal.azure.com/)

- [Konfigurace jednotného přihlašování F5 pro aplikaci založenou na hlavičkách](headerf5-tutorial.md)

- [Konfigurace jednotného přihlašování F5 pro aplikaci Kerberos](kerbf5-tutorial.md)

