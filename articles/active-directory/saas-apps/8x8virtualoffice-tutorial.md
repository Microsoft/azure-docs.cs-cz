---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s 8x8 | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 8x8.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: a5e7e46041e339646e4813a4cce61046f2a57e5e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92319066"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s 8x8

V tomto kurzu se dozvíte, jak integrovat 8x8 s Azure Active Directory (Azure AD). Když integrujete 8x8 s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k 8x8.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k 8x8 svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné 8x8

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* 8x8 podporuje jednotné přihlašování (SSO) **a IDP** .

* Po nakonfigurování 8x8 můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-8x8-from-the-gallery"></a>Přidání 8x8 z Galerie

Pokud chcete nakonfigurovat integraci 8x8 do služby Azure AD, musíte přidat 8x8 z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **8x8** .
1. Na panelu výsledků vyberte **8x8** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-8x8"></a>Konfigurace a testování jednotného přihlašování Azure AD pro 8x8

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí 8x8 pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v 8x8.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí 8x8, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte 8X8 SSO](#configure-8x8-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte 8x8 Test User](#create-8x8-test-user)** -to, abyste měli protějšek B. Simon v 8x8, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **8X8** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://sso.8x8.com/saml2`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL: `https://sso.8x8.com/saml2`

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače. Certifikát budete používat později v tomto kurzu v části **Konfigurace jednotného přihlašování 8x8** .

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení 8x8** zkopírujte adresy URL a tyto hodnoty URL budete používat později v tomto kurzu.

    ![Kopírovat adresy URL konfigurace](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k 8x8.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **8x8**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-8x8-sso"></a>Konfigurace jednotného přihlašování 8x8

V další části kurzu záleží na tom, jaký typ předplatného máte v 8x8.

* Zákazníci s 8x8 edicemi a X Series, kteří používají Configuration Manager pro správu, najdete v tématu [Konfigurace 8x8 Configuration Manager](#configure-8x8-configuration-manager).
* Pro zákazníky s Virtual Office, kteří používají správce účtů pro správu, najdete v tématu [Konfigurace Správce účtů 8x8](#configure-8x8-account-manager).

### <a name="configure-8x8-configuration-manager"></a>Konfigurace Configuration Manager 8x8

1. Pokud chcete automatizovat konfiguraci v rámci 8x8, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit 8x8** , které vás přesměruje do aplikace 8x8. Odtud zadejte přihlašovací údaje správce, které se přihlásí k 8x8. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit 8x8 ručně, přihlaste se k 8x8 [Configuration Manager](https://vo-cm.8x8.com/) jako správce.

1. Z domovské stránky klikněte na **Správa identit**.

    ![Snímek obrazovky, který zvýrazní dlaždici Správa identit.](./media/8x8virtualoffice-tutorial/configure1.png)

1. Zaškrtněte políčko **jednotného přihlašování (SSO)** a pak vyberte **Microsoft Azure AD**.

    ![Snímek obrazovky, který zvýrazní jednotné přihlašování (SSO) a Microsoft Azure AD možnosti.](./media/8x8virtualoffice-tutorial/configure2.png)

1. Zkopírujte tři adresy URL a podpisový certifikát ze stránky **nastavit jeden Sign-On se** stránkou SAML ve službě Azure AD do části **Microsoft Azure AD nastavení SAML** v 8x8 Configuration Manager.

    ![8x8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Zkopírujte **přihlašovací adresu** URL pro **přihlašovací adresu URL pro IDP**.

    b. Zkopírujte **identifikátor Azure AD** na **adresu URL nebo název URN vystavitele IDP**.

    c. Kopírovat **adresu URL pro odhlášení** na **adresu URL pro odhlášení IDP**

    d. Stáhněte si **certifikát (Base64)** a nahrajte ho do **certifikátu**.

    e. Klikněte na **Uložit**.

### <a name="configure-8x8-account-manager"></a>Konfigurace správce účtů 8x8

1. Přihlaste se ke svému tenantovi 8x8 Virtual Office jako správce.

1. Na panelu aplikace vyberte **správce účtu Virtual Office** .

    ![Snímek obrazovky, který zvýrazní dlaždici Správce účtu Virtual Office.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Vyberte **obchodní** účet, který chcete spravovat, a klikněte na tlačítko **Přihlásit** .

    ![Snímek obrazovky, který zvýrazní možnost firmy a tlačítko pro přihlášení](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. V seznamu nabídek klikněte na kartu **účty** .

    ![Snímek obrazovky, který zvýrazní kartu účty v seznamu nabídek.](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. V seznamu účtů klikněte na **jednotné přihlašování** .

    ![Snímek obrazovky, který zvýrazní možnost jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. V části metody ověřování vyberte **jednotné přihlašování** a klikněte na **SAML**.

    ![Snímek obrazovky, který zvýrazní protokol SAML v rámci jednotného přihlašování](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. V části **jednotné přihlašování SAML** proveďte následující kroky:

    ![Konfigurovat na straně aplikace](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    b. V textovém poli **Adresa URL** pro odhlášení vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Klikněte na tlačítko **Procházet** a nahrajte certifikát, který jste stáhli z Azure Portal.

    e. Klikněte na tlačítko **Uložit**.

### <a name="create-8x8-test-user"></a>Vytvořit testovacího uživatele 8x8

V této části vytvoříte uživatele s názvem Britta Simon v 8x8. Pokud chcete přidat uživatele na platformě 8x8, pracujte s [týmem podpory 8x8](https://www.8x8.com/about-us/contact-us) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici 8x8, měli byste se automaticky přihlásit k 8x8, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si 8x8 s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit 8x8 pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)