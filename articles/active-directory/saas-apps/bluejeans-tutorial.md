---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s BlueJeans pro Azure AD | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BlueJeans pro Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2020
ms.author: jeedes
ms.openlocfilehash: 43189d40c57ce6c300904207edc35011b0581ecc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91740442"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s BlueJeans pro Azure AD

V tomto kurzu se dozvíte, jak integrovat BlueJeans pro Azure AD s Azure Active Directory (Azure AD). Při integraci BlueJeans pro Azure AD se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k BlueJeans pro Azure AD.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k BlueJeans pro Azure AD pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* BlueJeans pro odběr Azure AD s povoleným jednotným přihlašováním (SSO).

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* BlueJeans pro Azure AD podporuje jednotné přihlašování (SSO) spouštěné v **SP**

* BlueJeans pro Azure AD podporuje [ **automatizované** zřizování uživatelů.](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Přidání BlueJeans pro Azure AD z Galerie

Pokud chcete nakonfigurovat integraci BlueJeans pro Azure AD do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat BlueJeans pro Azure AD z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **BLUEJEANS pro Azure AD** .
1. Z panelu výsledků vyberte **BlueJeans pro Azure AD** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurace a testování jednotného přihlašování Azure AD pro BlueJeans pro Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí BlueJeans pro Azure AD pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v BlueJeans pro Azure AD.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí BlueJeans pro Azure AD, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte BlueJeans pro jednotné přihlašování Azure AD](#configure-bluejeans-for-azure-ad-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte BlueJeans pro testovacího uživatele Azure AD](#create-bluejeans-for-azure-ad-test-user)** – abyste měli protějšek B. Simon v BlueJeans pro Azure AD, který se odkazuje na reprezentaci uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **BLUEJEANS pro Azure AD** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.bluejeans.com`

    a. Do textového pole **identifikátor (ID entity)** zadejte adresu URL: `http://samlsp.bluejeans.com`

    a. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://bluejeans.com/sso/saml2/`

    > [!NOTE]
    > Hodnota URL Sign-On není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, kontaktujte [BlueJeans pro tým podpory klienta Azure AD](https://support.bluejeans.com/contact) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. BlueJeans aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace BlueJeans několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |  Zdrojový atribut|
    | ---------| --------- |
    | Rozložení | User. telephoneNumber |
    | title | User. jobtitle |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení BlueJeans pro Azure AD** zkopírujte na základě vašeho požadavku příslušné adresy URL (y).

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k BlueJeans pro Azure AD.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **BlueJeans pro Azure AD**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Konfigurace BlueJeans pro jednotné přihlašování Azure AD

1. V jiném okně webového prohlížeče se přihlaste k **BlueJeans pro web společnosti Azure AD** jako správce.

2. Přejít na ** \> Nastavení skupiny správců \> zabezpečení**.

    ![Snímek obrazovky zobrazuje část okna prohlížeče s vybranou kartou správce s nastavením skupiny a vybraným zabezpečením.](./media/bluejeans-tutorial/ic785868.png "Správce")

3. V části **zabezpečení** proveďte následující kroky:

    ![Jednotné přihlašování SAML](./media/bluejeans-tutorial/ic785869.png "Jednotné přihlašování SAML")

    a. Vyberte **jednotné přihlašování SAML**.

    b. Vyberte **Povolit automatické zřizování**.

4. Přejděte k následujícímu postupu:

    ![Cesta k certifikátu](./media/bluejeans-tutorial/ic785870.png "Cesta k certifikátu")

    a. Klikněte na **zvolit soubor**a nahrajte certifikát s kódováním base-64, který jste stáhli z Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL změny hesla** vložte hodnotu **Adresa URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

5. Přejděte k následujícímu postupu:

    ![Uložit změny](./media/bluejeans-tutorial/ic785874.png "Uložit změny")

    a. Do textového pole **ID uživatele** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    b. Do textového pole **e-mail** zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` .

    c. Klikněte na **Uložit změny**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Vytvořit BlueJeans pro testovacího uživatele Azure AD

Cílem této části je vytvořit uživatele s názvem B. Simon v BlueJeans pro Azure AD. BlueJeans pro Azure AD podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](bluejeans-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k **BlueJeans pro web společnosti Azure AD** jako správce.

2. Přejít na **správce \> Správa uživatelů \> Přidat uživatele**

    ![Snímek obrazovky zobrazuje část okna prohlížeče se zvolenou kartou správce s možností spravovat uživatele a přidat vybrané uživatele.](./media/bluejeans-tutorial/ic785877.png "Správce")

    > [!IMPORTANT]
    > Karta **Přidat uživatele** je k dispozici pouze v případě, že na **kartě SECUTIRY**není zaškrtnuto zaškrtávací políčka **Povolit automatické zřizování** .

3. V části **Přidat uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí část přidat uživatele, do které zadáte informace popsané v tomto kroku.](./media/bluejeans-tutorial/ic785886.png "Přidání uživatele")

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako **B**.

    b. Do textového pole **příjmení** zadejte příjmení uživatele, jako je **Simon**.

    c. Do textového pole **Vybrat BlueJeans pro uživatelské jméno Azure AD** zadejte uživatelské jméno uživatele, jako je **Brittasimon** .

    d. Do textového pole **vytvořit heslo** zadejte své heslo.

    e. Do textového pole **Společnost** zadejte svou společnost.

    f. Do textového pole **e-mailová adresa** zadejte e-maily uživatele jako `b.simon\@contoso.com` .

    například V textovém poli **vytvořit BlueJeans pro schůzku služby Azure AD** zadejte ID schůzky.

    h. Do textového pole **Vybrat heslo moderátora** zadejte heslo.

    i. Klikněte na **pokračovat**.

    ![Snímek obrazovky se zobrazí část přidat uživatele, kde můžete zobrazit nastavení a funkce pomocí vybraného tlačítka Přidat uživatele.](./media/bluejeans-tutorial/ic785887.png "Přidání uživatele")

    J. Klikněte na **Přidat uživatele**.

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jiné BlueJeans pro nástroje pro vytváření uživatelských účtů Azure AD nebo rozhraní API poskytovaná BlueJeans pro Azure AD.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici BlueJeans for Azure AD na přístupovém panelu, měli byste se automaticky přihlásit ke službě BlueJeans pro Azure AD, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si BlueJeans pro Azure AD s Azure AD](https://aad.portal.azure.com/)
