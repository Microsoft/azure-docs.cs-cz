---
title: 'Kurz: Azure Active Directory integrace s jive | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: bcfc2996daeb34f357625572d39661638982ae42
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489111"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s jive

V tomto kurzu se dozvíte, jak integrovat Jive s Azure Active Directory (Azure AD). Když integrujete Jive s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Jive.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Jive svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Jive odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Jive podporuje jednotné přihlašování iniciované v **SP** .
* Jive podporuje [ **automatizované** zřizování uživatelů](jive-provisioning-tutorial.md).

## <a name="add-jive-from-the-gallery"></a>Přidání Jive z Galerie

Pokud chcete nakonfigurovat integraci Jive do služby Azure AD, musíte přidat Jive z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Jive** .
1. Na panelu výsledků vyberte **Jive** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Jive

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Jive pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Jive.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Jive postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte Jive SSO](#configure-jive-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Jive Test User](#create-jive-test-user)** -to, abyste měli protějšek B. Simon v Jive, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Jive** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<instance name>.jivecustom.com`

   b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Jive](https://www.jivesoftware.com/services-support/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Jive** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Jive.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Jive**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-jive-sso"></a>Konfigurace jednotného přihlašování Jive

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně **Jive** , přihlaste se ke svému tenantovi Jive jako správce.

1. V nabídce v horní části klikněte na **SAML**.

    ![Snímek obrazovky zobrazuje kartu SAML s povoleným vybraným.](./media/jive-tutorial/jive-2.png)

    a. Na kartě **Obecné** vyberte **povoleno** .

    b. Klikněte na tlačítko **uložit všechna nastavení SAML** .

1. Přejděte na kartu **metadata IDP** .

    ![Snímek obrazovky ukazuje vybranou METADATA karta SAML I D P.](./media/jive-tutorial/jive-3.png)

    a. Zkopírujte obsah staženého souboru XML metadat a vložte jej do textového pole **metadat poskytovatele identity (IDP)** .

    b. Klikněte na tlačítko **uložit všechna nastavení SAML** .

1. Vyberte kartu **mapování atributu uživatele** .

    ![Snímek obrazovky s vybraným MAPOVÁNÍm atributu uživatele zobrazuje kartu SAML.](./media/jive-tutorial/jive-4.png)

    a. Do textového pole **e-mail** zkopírujte a vložte název atributu hodnoty **pošty** .

    b. Do textového pole **jméno v prvním názvu** zkopírujte a vložte název atributu hodnoty **daného** názvu.

    c. Do textového pole **příjmení název** zkopírujte a vložte název atributu, který je hodnota **příjmení** .

### <a name="create-jive-test-user"></a>Vytvořit testovacího uživatele Jive

Cílem této části je vytvořit uživatele s názvem Britta Simon v Jive. Jive podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](jive-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

Pokud potřebujete ručně vytvořit uživatele, pracujte s [týmem podpory klienta Jive](https://www.jivesoftware.com/services-support/) a přidejte uživatele na platformě Jive.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Jive, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Jive přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Jive v části Moje aplikace, přesměruje se na přihlašovací adresu Jive. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Jive můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).