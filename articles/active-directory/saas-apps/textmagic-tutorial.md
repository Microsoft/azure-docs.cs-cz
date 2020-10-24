---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s TextMagic | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TextMagic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: f1db4f1ae40c72203cdd5d07c49b2f9c202ad525
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491355"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s TextMagic

V tomto kurzu se dozvíte, jak integrovat TextMagic s Azure Active Directory (Azure AD). Když integrujete TextMagic s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TextMagic.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TextMagic svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TextMagic odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* TextMagic podporuje jednotné přihlašování **IDP** .

* TextMagic podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-textmagic-from-the-gallery"></a>Přidání TextMagic z Galerie

Pokud chcete nakonfigurovat integraci TextMagic do služby Azure AD, musíte přidat TextMagic z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TextMagic** .
1. Na panelu výsledků vyberte **TextMagic** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-textmagic"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TextMagic

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí TextMagic pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TextMagic.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TextMagic, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte TEXTMAGIC SSO](#configure-textmagic-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte TextMagic Test User](#create-textmagic-test-user)** -to, abyste měli protějšek B. Simon v TextMagic, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **TextMagic** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **identifikátor** zadejte adresu URL:  `https://my.textmagic.com/saml/metadata`

5. TextMagic aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Aplikace TextMagic očekává, že **NameIdentifier** budou mapovány pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace TextMagic několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name |   Zdrojový atribut| Obor názvů  |
    | --------------- | --------------- | --------------- |
    | company | User. CompanyName | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | User. křestní jméno |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | User. příjmení |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Android               | User. telephoneNumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení TextMagic** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TextMagic.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TextMagic**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-textmagic-sso"></a>Konfigurace jednotného přihlašování TextMagic

1. Pokud chcete automatizovat konfiguraci v rámci TextMagic, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **TextMagic nastavení** a nasměrujte vás na aplikaci TextMagic. Odtud zadejte přihlašovací údaje správce, které se přihlásí k TextMagic. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit TextMagic ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu TextMagic společnosti jako správce a proveďte následující kroky:

4. V části uživatelské jméno vyberte **Nastavení účtu** .

    ![Snímek obrazovky zobrazuje nastavení účtu vybrané od uživatele.](./media/textmagic-tutorial/config1.png)

5. Klikněte na kartu **jednotné Sign-On (SSO)** a vyplňte následující pole:  

    ![Snímek obrazovky zobrazuje jednu kartu Sign-On, kde můžete zadat hodnoty, které jsou popsány.](./media/textmagic-tutorial/config2.png)

    a. V části **ID entity zprostředkovatele identity:** TextBox vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    b. V části **Adresa URL jednotného přihlašování zprostředkovatele identity:** textové pole – vložte hodnotu **přihlašovací adresy URL**, kterou jste zkopírovali z Azure Portal.

    c. V části **Adresa URL objektu slo zprostředkovatele identity:** TextBox vložte hodnotu **adresy URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Otevřete v programu Poznámkový blok **certifikát s kódováním Base-64** stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do **veřejného certifikátu x509:** TextBox.

    e. Klikněte na **Uložit**.

### <a name="create-textmagic-test-user"></a>Vytvořit testovacího uživatele TextMagic

Aplikace podporuje **zřizování uživatelů pouze v čase** a po ověření budou automaticky vytvořena v aplikaci. Po prvním přihlášení budete muset tyto informace vyplnit a aktivovat dílčí účet do systému.
V této části není žádná položka akce.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici TextMagic, měli byste se automaticky přihlásit k TextMagic, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si TextMagic s Azure AD](https://aad.portal.azure.com/)

