---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Deskradar | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Deskradar.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: 071b311466869321a12626190e894287c28e707b
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91774118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-deskradar"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Deskradar

V tomto kurzu se dozvíte, jak integrovat Deskradar s Azure Active Directory (Azure AD). Když integrujete Deskradar s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Deskradar.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Deskradar svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Deskradar odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.



* Deskradar podporuje jednotné přihlašování (SSO) **a IDP** .



## <a name="adding-deskradar-from-the-gallery"></a>Přidání Deskradar z Galerie

Pokud chcete nakonfigurovat integraci Deskradar do služby Azure AD, musíte přidat Deskradar z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Deskradar** .
1. Na panelu výsledků vyberte **Deskradar** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-deskradar"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Deskradar

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Deskradar pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Deskradar.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Deskradar, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte DESKRADAR SSO](#configure-deskradar-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Deskradar Test User](#create-deskradar-test-user)** -to, abyste měli protějšek B. Simon v Deskradar, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Deskradar** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<YOURDOMAIN>.deskradar.cloud`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/consume`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<YOURDOMAIN>.deskradar.cloud/auth/sso/saml/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Nahraďte **yourdomain** doménou instance Deskradar. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Deskradar](mailto:support@deskradar.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Deskradar aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace Deskradar několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    | ---------------| --------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |
    | E-mail | User. userPrincipalName |
    | | |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Deskradar** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Deskradar.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Deskradar**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-deskradar-sso"></a>Konfigurace jednotného přihlašování Deskradar

1. Pokud chcete automatizovat konfiguraci v rámci Deskradar, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Deskradar nastavení** a nasměrujte vás na aplikaci Deskradar. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Deskradar. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit Deskradar ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Deskradar společnosti jako správce a proveďte následující kroky:

1. Kliknutím na ikonu na bočním panelu otevřete **týmový** panel.

1. Přepněte na kartu **ověřování** .

1. Na kartě **SAML 2,0** proveďte následující kroky:

    ![Snímek obrazovky zobrazující kartu S A M L 2,0 se šipkami ukazujícími na pole S A M L S S D U R L a Vystavitel zprostředkovatele identifikátorů.](./media/deskradar-tutorial/14-paste-urls.jpg)

    a. Povolte metodu ověřování **SAML** .

    b. Do textového pole **URL jednotného přihlašování SAML** zadejte hodnotu **adresy URL** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **vystavitele zprostředkovatele identity** zadejte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

1. Otevřete stažený soubor **certifikátu (Base64)** pomocí textového editoru a zkopírujte a vložte jeho obsah do pole **veřejný certifikát** v Deskradar.

    ![Konfigurace Deskradar](./media/deskradar-tutorial/15-paste-certificate.jpg)

### <a name="create-deskradar-test-user"></a>Vytvořit testovacího uživatele Deskradar

V této části vytvoříte uživatele s názvem B. Simon v Deskradar. Pokud chcete přidat uživatele na platformě Deskradar, pracujte s [týmem podpory klienta Deskradar](mailto:support@deskradar.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Deskradar, měli byste se automaticky přihlásit k Deskradar, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Deskradar s Azure AD](https://aad.portal.azure.com/)

