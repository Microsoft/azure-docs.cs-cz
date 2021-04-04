---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Claromentis | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Claromentis.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455897"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Claromentis

V tomto kurzu se dozvíte, jak integrovat Claromentis s Azure Active Directory (Azure AD). Když integrujete Claromentis s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Claromentis.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Claromentis svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Claromentis odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Claromentis podporuje jednotné přihlašování (SSO) **a IDP** .
* Claromentis podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-claromentis-from-the-gallery"></a>Přidání Claromentis z Galerie

Pokud chcete nakonfigurovat integraci Claromentis do služby Azure AD, musíte přidat Claromentis z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Claromentis** .
1. Na panelu výsledků vyberte **Claromentis** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Claromentis

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Claromentis pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Claromentis.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Claromentis, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte CLAROMENTIS SSO](#configure-claromentis-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Claromentis Test User](#create-claromentis-test-user)** -to, abyste měli protějšek B. Simon v Claromentis, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Claromentis** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte hodnotu identifikátoru podle požadavku vaší organizace.

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL pro přihlášení, která je vysvětlena dále v turorial.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení Claromentis** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Claromentis.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Claromentis**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-claromentis-sso"></a>Konfigurace jednotného přihlašování Claromentis

1. V jiném okně prohlížeče se přihlaste k webu Claromentis jako správce.

1. Klikněte na **ikonu aplikace** a vyberte **správce**.

    ![Snímek obrazovky se zobrazí web Claromentis s vybraným správcem.](./media/claromentis-tutorial/config1.png)

1. Vyberte kartu **vlastní obslužná rutina přihlašovacích** údajů.

    ![Snímek obrazovky se stránkou pro správu s vybraným vlastním popisovačem přihlašovacích údajů.](./media/claromentis-tutorial/config2.png)

1. Vyberte položku **Konfigurace SAML**.

    ![Snímek obrazovky zobrazující konfigurační stránku SAML.](./media/claromentis-tutorial/config3.png)

1. Na kartě **Konfigurace SAML** přejděte dolů k části **Konfigurace** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí konfigurační část stránky, kde můžete zadat informace popsané v tomto kroku.](./media/claromentis-tutorial/config4.png)

    a. Do textového pole **název technického kontaktu** zadejte jméno technické kontaktní osoby.

    b. Do textového pole **kontaktní E-mail technického kontaktu** zadejte e-mailovou adresu technické kontaktní osoby.

    c. Zadejte heslo do textového pole **heslo správce ověřování** .

1. Přejděte dolů na **zdroje ověřování** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části zdroje ověřování, kde můžete zadat informace popsané v tomto kroku.](./media/claromentis-tutorial/config5.png)

    a. Do textového pole **IDP** zadejte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **ID entity** zadejte hodnotu ID entity.

    c. Nahrajte soubor **XML federačních metadat** , který jste stáhli z Azure Portal.

    d. Klikněte na **Uložit**.

1. Nyní si všimnete, že všechny adresy URL byly vyplněny v části **poskytovatel identity** v oddílu **Konfigurace SAML** .

    ![Snímek obrazovky se zobrazí stránka zprostředkovatele identity naplněná pomocí U R ls.](./media/claromentis-tutorial/config6.png)

    a. Hodnota kopírovat **identifikátor (ID entity):** tuto hodnotu vložte do textového pole **identifikátoru** v části **základní konfigurace SAML** v Azure Portal.

    b. Zkopírujte hodnotu **adresy URL odpovědi** a vložte tuto hodnotu do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    c. Kopírovat hodnotu **adresy URL pro přihlášení** vložte tuto hodnotu do textového pole **přihlašovací adresa URL** v základní části **Konfigurace SAML** v Azure Portal.

### <a name="create-claromentis-test-user"></a>Vytvořit testovacího uživatele Claromentis

V této části se v Claromentis vytvoří uživatel s názvem B. Simon. Claromentis podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Claromentis neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Claromentis, měli byste se automaticky přihlásit k Claromentis, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Claromentis s Azure AD](https://aad.portal.azure.com/)