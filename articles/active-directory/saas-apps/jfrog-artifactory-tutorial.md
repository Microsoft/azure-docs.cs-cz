---
title: 'Kurz: Azure Active Directory integrace s JFrog Artifactory | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a JFrog Artifactory.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: f0fafa5c0cc2e0b1bf0f4e11db3265824feb5296
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374693"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Kurz: integrace JFrog Artifactory s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat JFrog Artifactory s Azure Active Directory (Azure AD). Když integrujete JFrog Artifactory s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k JFrog Artifactory.
* Umožněte uživatelům, aby se automaticky přihlásili k JFrog Artifactory s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné JFrog Artifactory s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* JFrog Artifactory podporuje **SP a IDP** iniciované jednotné přihlašování.
* JFrog Artifactory podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-jfrog-artifactory-from-the-gallery"></a>Přidání JFrog Artifactory z Galerie

Pokud chcete nakonfigurovat integraci JFrog Artifactory do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat JFrog Artifactory z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **JFrog Artifactory** .
1. Z panelu výsledků vyberte **JFrog Artifactory** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí JFrog Artifactory s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v JFrog Artifactory.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s JFrog Artifactory, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování JFrog Artifactory](#configure-jfrog-artifactory-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořit JFrog Artifactory Test User](#create-jfrog-artifactory-test-user)** -to znamená, že má protějšek B. Simon v JFrog Artifactory, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Artifactory JFrog** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<servername>.jfrog.io`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    
    - Pro Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Pro Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    - Pro Artifactory 6. x: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Pro Artifactory 7. x: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory JFrog Artifactory](https://support.jfrog.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace JFrog Artifactory očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na ikonu **Upravit** otevřete dialogové okno atributy uživatele.

    ![Snímek obrazovky ukazuje atributy uživatele s ovládacím prvkem pro úpravy, který se vyvolal.](common/edit-attribute.png)

1. Kromě výše uvedeného JFrog Artifactory očekává, že se v odpovědi SAML vrátí několik dalších atributů. V dialogovém okně deklarace identity v části **atributy uživatele & deklarace** v dialogu **deklarace skupiny (Preview)** proveďte následující kroky:

    a. Klikněte na **pero** vedle **skupin vrácených v deklaraci identity**.

    ![Snímek obrazovky zobrazuje atributy uživatele & deklarací identity s vybranou ikonou pro úpravy.](./media/jfrog-artifactory-tutorial/config04.png)

    ![Snímek obrazovky ukazuje oddíl deklarace skupiny se všemi vybranými skupinami.](./media/jfrog-artifactory-tutorial/config05.png)

    b. V seznamu přepínačů vyberte **všechny skupiny** .

    c. Klikněte na **Uložit**.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyhledejte v části **podpisový certifikát SAML** **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Nakonfigurujte Artifactory (název poskytovatele služeb SAML) pomocí pole identifikátor (viz krok 4). V části **Nastavení Artifactory JFrog** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   - Pro Artifactory 6. x: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Pro Artifactory 7. x: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-jfrog-artifactory-sso"></a>Konfigurace jednotného přihlašování JFrog Artifactory

Vše, co potřebujete ke konfiguraci jednotného přihlašování na straně **JFrog Artifactory** , je konfigurovatelné správcem Artifactory na obrazovce configugration SAML.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k JFrog Artifactory.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **JFrog Artifactory**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-jfrog-artifactory-test-user"></a>Vytvořit testovacího uživatele JFrog Artifactory

V této části se na JFrog Artifactory vytvoří uživatel s názvem B. Simon. JFrog Artifactory podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v JFrog Artifactory neexistuje, vytvoří se po ověření nový.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici JFrog Artifactory, měli byste být automaticky přihlášeni k JFrog Artifactory, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)