---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu ScaleX Enterprise | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScaleX Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: fecb7381786c36b077d329f68ca48ab10f65a984
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642968"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí programu ScaleX Enterprise

V tomto kurzu se dozvíte, jak integrovat řešení ScaleX Enterprise s Azure Active Directory (Azure AD). Když ve službě Azure AD integrujete ScaleX Enterprise, můžete:

* Řízení ve službě Azure AD, která má přístup k ScaleX Enterprise.
* Umožněte uživatelům, aby se automaticky přihlásili k programu ScaleX Enterprise pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné standardu ScaleX Enterprise jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ScaleX Enterprise podporuje **SP a IDP** iniciované jednotné přihlašování

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Přidání ScaleX Enterprise z Galerie

Pokud chcete nakonfigurovat integraci ScaleX Enterprise do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ScaleX Enterprise z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **scaleX Enterprise** .
1. Na panelu výsledků vyberte **scaleX Enterprise** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Konfigurace a testování jednotného přihlašování Azure AD pro společnosti ScaleX Enterprise

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí formátu ScaleX Enterprise pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v organizaci ScaleX Enterprise.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ScaleX Enterprise, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace scaleX Enterprise SSO](#configure-scalex-enterprise-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte si uživatele scaleX Enterprise Test User](#create-scalex-enterprise-test-user)** – abyste měli protějšek B. Simon v systému scaleX Enterprise, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **scaleX Enterprise** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://platform.rescale.com/saml2/<company id>/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory scaleX Enterprise Client](https://about.rescale.com/contactus.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Podniková aplikace ScaleX očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **EmailAddress** je mapován pomocí **User. mail**. Aplikace ScaleX Enterprise Application očekává, že **EmailAddress** mají být mapovány pomocí **User. userPrincipalName**, takže je nutné upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení společnosti scaleX Enterprise** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k systému ScaleX Enterprise.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **scaleX Enterprise**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-scalex-enterprise-sso"></a>Konfigurace ScaleX Enterprise SSO

1. Pokud chcete automatizovat konfiguraci v rámci systému ScaleX Enterprise, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit společnost scaleX Enterprise** tím, že vás přesměruje na podnikovou aplikaci scaleX. Odtud zadejte přihlašovací údaje správce pro přihlášení k ScaleX Enterprise. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Chcete-li nastavit aplikaci ScaleX Enterprise ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu společnosti ScaleX Enterprise Company jako správce a proveďte následující kroky:

1. Klikněte na nabídku v pravém horním rohu a vyberte **Správa společnosti Contoso**.

    > [!NOTE]
    > Contoso je pouze příklad. Mělo by se jednat o skutečný název vaší společnosti.

    ![Snímek obrazovky, který zobrazuje ukázkový název společnosti vybraný v nabídce vpravo nahoře.](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. V horní nabídce vyberte **integrace** a vyberte **jednotné přihlašování**.

    ![Snímek obrazovky s vybraným integrací a jediným přihlašováním z rozevírací nabídky](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Vyplňte formulář následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Vyberte **vytvořit libovolného uživatele, který se může ověřit pomocí jednotného přihlašování** .

    b. **Zprostředkovatel služeb SAML**: vložte hodnotu **_urn: Oasis: names: TC: SAML: 2.0: NameId-Format: persistent_**

    c. **Název pole e-mailu zprostředkovatele identity v odpovědi ACS**: vložte hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID entity EntityDescriptor zprostředkovatele identity:** Vložte hodnotu **identifikátoru Azure AD** zkopírovanou z Azure Portal.

    e. **Adresa URL SingleSignOnService zprostředkovatele identity:** Vložte **přihlašovací adresu URL** z Azure Portal.

    f. **Certifikát veřejného certifikátu x509 zprostředkovatele identity:** Otevřete certifikát x509 stažený z Azure v poznámkovém bloku a vložte obsah do tohoto pole. Zajistěte, aby uprostřed obsahu certifikátu nedocházelo k žádným koncům řádků.

    například Zaškrtněte následující políčka: **povoleno, zašifrujte NameId a podepište AuthnRequests.**

    h. Kliknutím na **aktualizovat nastavení jednotného přihlašování** nastavení uložte.

### <a name="create-scalex-enterprise-test-user"></a>Vytvořit testovacího uživatele ScaleX Enterprise

Aby se uživatelé Azure AD mohli přihlašovat do společnosti ScaleX Enterprise, musí se zřídit v rámci společnosti ScaleX Enterprise. V případě programu ScaleX Enterprise je zřizování automatickým úkolem a nejsou potřeba žádné ruční kroky. Každý uživatel, který se může úspěšně ověřit s přihlašovacími údaji SSO, se automaticky zřídí na straně ScaleX.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ScaleX Enterprise, měli byste být automaticky přihlášeni k organizaci ScaleX, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si ScaleX Enterprise s Azure AD](https://aad.portal.azure.com/)
