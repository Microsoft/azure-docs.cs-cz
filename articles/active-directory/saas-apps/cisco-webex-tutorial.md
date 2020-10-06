---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s schůzkami Cisco Webex | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex schůzky.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.openlocfilehash: 466e69ece993ca34f8f6770c0465471d01b9c3ed
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760933"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s WebEx schůzkami Cisco

V tomto kurzu se dozvíte, jak integrovat schůzky Cisco Webex s Azure Active Directory (Azure AD). Když integrujete schůzky Cisco Webex s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k WebEx schůzkám společnosti Cisco
* Umožněte, aby se vaši uživatelé automaticky přihlásili k WebEx schůzce Cisco s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Cisco Webex schůzky s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Schůzky Cisco Webex, které podporují jednotné přihlašování (SSO) **SP a IDP**

* Schůzky Cisco Webex podporují zřizování uživatelů **jenom v čase**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Přidávání schůzek Cisco Webex z Galerie

Pokud chcete nakonfigurovat integraci schůzek Cisco Webex do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat schůzky Cisco Webex z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **WebEx schůzky Cisco** .
1. Na panelu výsledků vyberte **schůzky Cisco Webex** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Konfigurace a testování jednotného přihlašování Azure AD pro schůzky Cisco Webex

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí setkání Cisco Webex s využitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v WebEx schůzce Cisco.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí setkání Cisco Webex, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[NAKONFIGURUJTE jednotné přihlašování pro schůzky Cisco Webex](#configure-cisco-webex-meetings-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte si uživatele WebEx schůzky Cisco](#create-cisco-webex-meetings-test-user)** , abyste měli protějšek B. Simon v setkáních Cisco Webex, které jsou propojené s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **schůzky Cisco Webex** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** můžete nakonfigurovat aplikaci v režimu iniciované **IDP** tím, že nahrajete soubor **metadat poskytovatele služby** následujícím způsobem:

    a. Klikněte na **nahrát soubor metadat**.

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném dokončení nahrávání souboru metadat poskytovatele **služby se v** části **základní konfigurace SAML** automaticky naplní hodnoty **adresy URL pro odpovědi** .

    >[!Note]
    >Soubor metadat poskytovatele služeb získáte v části **Konfigurace jednotného přihlašování pro Cisco Webex schůzky** , které jsou vysvětleny dále v tomto kurzu. 

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , proveďte následující kroky:  

    a. V části **základní konfigurace SAML** klikněte na ikonu Upravit/pero.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)
    
    b. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: ` https://<customername>.my.webex.com`

5. Aplikace WebEx pro schůzky Cisco očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

6. Kromě výše očekává aplikace WebEx schůzky Cisco několik atributů, které se vrátí zpátky v odpovědi SAML. V části deklarace identity uživatelů v dialogovém okně atributy uživatele proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce: 

    | Název | Zdrojový atribut|
    | ---------------|  --------- |
    |   FirstName    | User. křestní jméno |
    |   polím    | User. příjmení |
    |   e-mail       | uživatel. pošta |
    |   UID    | uživatel. pošta |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** vyberte v rozevíracím seznamu hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **Uložit**.

4. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení schůzek Cisco Webex** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
    1. Do pole **Název** zadejte `B.Simon`.  
    1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
    1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k schůzkám Cisco Webex.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **schůzky Cisco Webex**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurace jednotného přihlašování pro schůzky Cisco Webex

1. V rámci `https://<customername>.webex.com/admin` svých přihlašovacích údajů pro správu použijte adresu URL.

2. Přejděte na **běžné nastavení webu** a přejděte na **Konfigurace jednotného přihlašování**.
 
    ![Snímek obrazovky se správou Cisco Webex s vybranými běžnými nastaveními lokality a konfigurací S. O.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na stránce **Správa WebEx** proveďte následující kroky:

    ![Snímek obrazovky se stránkou pro správu WebEx, která obsahuje informace popsané v tomto kroku.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. jako **federační protokol**vyberte **SAML 2,0** .

    b. Kliknutím na odkaz **importovat metadata SAML** nahrajte soubor metadat, který jste stáhli z Azure Portal.

    c. Kliknutím na tlačítko **exportovat** stáhnete soubor metadat poskytovatele služby a nahrajete ho do části **základní konfigurace SAML** na Azure Portal.

    d. Do textového pole **AuthContextClassRef** zadejte `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` a pokud chcete povolit MFA pomocí Azure AD, zadejte dvě hodnoty jako `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Vyberte **Automatické vytváření účtů**.

    >[!NOTE]
    >Pokud chcete povolit zřizování uživatelů za **běhu** , musíte si ověřit **Automatické vytváření účtů**. Kromě toho je potřeba předat atributy tokenu SAML v odpovědi SAML.

    f. Klikněte na **Uložit**.

    >[!NOTE]
    >Tato konfigurace je určena pouze pro zákazníky, kteří používají WebEx UserID ve formátu e-mailu.

### <a name="create-cisco-webex-meetings-test-user"></a>Vytvoření testovacího uživatele pro schůzky Cisco Webex

Cílem této části je vytvořit uživatele s názvem B. Simon na schůzkách Cisco Webex. Schůzky Cisco Webex podporují zřizování za **běhu** , které je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel na schůzkách Cisco Webex ještě neexistuje, vytvoří se nový, když se pokusíte o přístup k schůzkám Cisco Webex.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici WebEx schůzky Cisco na přístupovém panelu, měli byste se automaticky přihlásit k schůzkám Cisco Webex, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si ServiceNow s Azure AD](https://aad.portal.azure.com)
