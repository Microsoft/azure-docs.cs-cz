---
title: 'Kurz: Azure Active Directory integrace s DigiCert | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DigiCert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 1c7befc0b42cfa3460a1c9b375a95b97e3e65746
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558439"
---
# <a name="tutorial-azure-active-directory-integration-with-digicert"></a>Kurz: Azure Active Directory integrace s DigiCert

V tomto kurzu se dozvíte, jak integrovat DigiCert s Azure Active Directory (Azure AD). Když integrujete DigiCert s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k DigiCert.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k DigiCert svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* DigiCert odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* DigiCert podporuje jednotné přihlašování (SSO) iniciované **IDP** .

## <a name="add-digicert-from-the-gallery"></a>Přidání DigiCert z Galerie

Pokud chcete nakonfigurovat integraci DigiCert do služby Azure AD, musíte přidat DigiCert z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **DigiCert** .
1. Na panelu výsledků vyberte **DigiCert** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-digicert"></a>Konfigurace a testování jednotného přihlašování Azure AD pro DigiCert

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí DigiCert pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v DigiCert.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí DigiCert postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte DIGICERT SSO](#configure-digicert-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte DigiCert Test User](#create-digicert-test-user)** -to, abyste měli protějšek B. Simon v DigiCert, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **DigiCert** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    Do textového pole **identifikátor** zadejte adresu URL:  `https://www.digicert.com/sso`

5. DigiCert aplikace očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky, který zobrazuje oddíl "atributy uživatele" s vybraným tlačítkem Upravit.](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky: 

    | Název |  Zdrojový atribut|
    | ---------------| --------------- |
    | nameidentifier | User. userPrincipalName |
    | company | < companycode > |
    | digicertrole | CanAccessCertCentral |

    > [!Note]
    > Hodnota atributu **Company** není typu Real. Aktualizujte tuto hodnotu skutečným kódem společnosti. Pokud chcete získat hodnotu atributu **Company** , obraťte se na [tým podpory DigiCert](mailto:support@digicert.com).

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky zobrazující oddíl deklarace identity uživatele se zvýrazněnými tlačítky přidat novou deklaraci a uložit](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

7. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

8. V části **Nastavení DigiCert** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k DigiCert.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **DigiCert**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-digicert-sso"></a>Konfigurace jednotného přihlašování DigiCert

Ke konfiguraci jednotného přihlašování na straně **DigiCert** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory DigiCert](mailto:support@digicert.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-digicert-test-user"></a>Vytvořit testovacího uživatele DigiCert

V této části vytvoříte uživatele s názvem Britta Simon v DigiCert. Pokud chcete přidat uživatele na platformě DigiCert, pracujte s [týmem podpory DigiCert](mailto:support@digicert.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k DigiCert, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici DigiCert v okně moje aplikace, měli byste se automaticky přihlásit k DigiCert, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování DigiCert můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
