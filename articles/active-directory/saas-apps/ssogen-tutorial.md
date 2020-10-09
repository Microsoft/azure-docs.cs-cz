---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) k bráně SSOGEN-Azure AD SSO pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SSOGEN – bránu Azure AD SSO pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 72b91be9875abf07e5f6f828ed32483a5d61eadd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524394"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE

V tomto kurzu se dozvíte, jak integrovat SSOGEN-Azure AD SSO pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE pomocí Azure Active Directory (Azure AD). Když integrujete SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SSOGEN – brána pro jednotné přihlašování Azure AD pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SSOGEN – brána pro jednotné přihlašování Azure AD pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE podporuje **SP a IDP** iniciované jednotné přihlašování.
* Po konfiguraci brány SSOGEN-Azure AD SSO pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE můžete vynutili řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Přidání SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE z Galerie

Pokud chcete nakonfigurovat integraci SSOGEN-AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE do Azure AD, musíte přidat SSOGEN – Azure AD SSO pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE z Galerie do vašeho seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a jde** .
1. Vyberte **SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a jde** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE SSOGEN bránu Azure AD SSO pro Oracle E Business Suite EBS, PeopleSoft spouštěných místně a jde SSO](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte si SSOGEN bránu Azure AD SSO pro Oracle e Business Suite EBS, PeopleSoft spouštěných místně a jde Test User](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** – abyste měli protějšek B. Simon v SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PEOPLESOFT SPOUŠTĚNÝCH místně a jde, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a jde** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a adresy URL Sign-On. Pokud chcete získat tyto hodnoty, kontaktujte [SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a jde tým podpory klientů](mailto:support@ssogen.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Vaše SSOGEN brána jednotného přihlašování Azure AD pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje, abyste přidali mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. SSOGEN – Brána služby Azure AD SSO pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE očekává, že **NameIdentifier** mají být mapovány pomocí **User. onpremisessamaccountname**, aby bylo nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k bráně SSOGEN-Azure AD SSO pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SSOGEN – brána Azure AD jednotného přihlašování pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a jde**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>Konfigurace brány SSOGEN pro jednotné přihlašování Azure AD pro Oracle E Business Suite EBS, PeopleSoft spouštěných místně a JDE SSO

Pokud chcete nakonfigurovat jednotné přihlašování na **SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a jde** , Najděte níže dokumentaci pro registraci jednotného přihlašování pro konkrétní aplikaci:

* Oracle EBS – integrace služby Azure AD SSO: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft spouštěných místně – integrace služby Azure AD SSO: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* ŘEŠENÍ JD Edwards – integrace služby Azure AD SSO: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Integrace Apache-Azure AD SSO: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>Vytvoření SSOGEN brány pro jednotné přihlašování Azure AD pro Oracle E Business Suite EBS, PeopleSoft spouštěných místně a JDE Test User

Po úspěšném ověření pošle služba Azure AD jedinečný identifikátor uživatele (ID názvu) k uživatelské aplikaci.  Ujistěte se prosím, že jedinečný identifikátor uživatele (ID názvu) odpovídá záznamu uživatele v aplikaci, FND_USER. Například USER_NAME v Oracle EBS.

Kontaktujte prosím [info@ssogen.com](mailto:info@ssogen.com) a [support@ssogen.com](mailto:support@ssogen.com) vyhledejte všechny podpory.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici SSOGEN-Azure AD SSO pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE na přístupovém panelu, měli byste se automaticky přihlásit ke službě SSOGEN – Azure AD SSO pro Oracle E-Business Suite-EBS, PeopleSoft spouštěných místně a JDE, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte SSOGEN – Azure AD SSO Gateway pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit SSOGEN bránu Azure AD SSO pro Oracle E-Business Suite – EBS, PeopleSoft spouštěných místně a JDE s pokročilými viditelnostmi a ovládacími prvky](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
