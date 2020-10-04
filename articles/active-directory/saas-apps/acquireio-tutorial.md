---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s AcquireIO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AcquireIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: cd07370c75ac64fa8e3806fed50a64db90122c76
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708301"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s AcquireIO

V tomto kurzu se dozvíte, jak integrovat AcquireIO s Azure Active Directory (Azure AD). Když integrujete AcquireIO s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k AcquireIO.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AcquireIO svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadované součásti

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AcquireIO odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AcquireIO podporuje jednotné přihlašování **IDP** .

## <a name="adding-acquireio-from-the-gallery"></a>Přidání AcquireIO z Galerie

Pokud chcete nakonfigurovat integraci AcquireIO do služby Azure AD, musíte přidat AcquireIO z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **AcquireIO** .
1. Na panelu výsledků vyberte **AcquireIO** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Konfigurace a testování jednotného přihlašování Azure AD pro AcquireIO

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí AcquireIO pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v AcquireIO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí AcquireIO, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ACQUIREIO SSO](#configure-acquireio-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte AcquireIO Test User](#create-acquireio-test-user)** -to, abyste měli protějšek B. Simon v AcquireIO, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **AcquireIO** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Hodnota není reálné číslo. Dostanete skutečnou adresu URL odpovědi, která se vysvětluje později v části **Konfigurace AcquireIO** v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení AcquireIO** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k AcquireIO.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **AcquireIO**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-acquireio-sso"></a>Konfigurace jednotného přihlašování AcquireIO

1. Pokud chcete automatizovat konfiguraci v rámci AcquireIO, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **nastavit AcquireIO**, které vás přesměruje na aplikaci AcquireIO. Odtud zadejte přihlašovací údaje správce pro přihlášení k AcquireIO. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit AcquireIO ručně, v jiném okně webového prohlížeče se přihlaste k AcquireIO jako správce.

1. V levé části nabídky klikněte na **obchod s aplikacemi**.

     ![Snímek obrazovky, který zvýrazňuje App Store.](./media/acquireio-tutorial/config01.png)

1. Přejděte dolů na **Active Directory** a klikněte na **nainstalovat**.

    ![Snímek obrazovky, který zvýrazní oddíl služby Active Directory a tlačítko nainstalovat.](./media/acquireio-tutorial/config02.png)

1. V místní nabídce služby Active Directory proveďte následující kroky:

    ![ScrenShot, která zobrazuje obrazovku služby Active Directory.](./media/acquireio-tutorial/config03.png)

    a. Kliknutím na **Kopírovat** zkopírujte adresu URL odpovědi pro vaši instanci a vložte ji do textového pole **Adresa URL odpovědi** v **základní části Konfigurace SAML** na Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    c. Otevřete v programu Poznámkový blok certifikát kódovaný jako base64, zkopírujte jeho obsah a vložte ho do textového pole **certifikát X. 509** .

    d. Klikněte na **připojit nyní**.

### <a name="create-acquireio-test-user"></a>Vytvořit testovacího uživatele AcquireIO

Aby se uživatelé Azure AD mohli přihlašovat k AcquireIO, musí se zřídit v AcquireIO. V AcquireIO je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. V jiném okně webového prohlížeče se přihlaste k AcquireIO jako správce.

1. V levé části nabídky klikněte na **profily** a přejděte na **Přidat profil**.

     ![Snímek obrazovky, který zvýrazní profily v nabídce na levé straně obrazovky a také možnost Přidat profil](./media/acquireio-tutorial/config04.png)

1. V automaticky otevíraném okně **Přidat zákazníka** proveďte následující kroky:

    ![Konfigurace AcquireIO](./media/acquireio-tutorial/config05.png)

    a. Do textového pole **název** zadejte jméno uživatele jako **B. Simon**.

    b. Do textového pole **e-mailu** zadejte e-maily uživatele jako **B.simon@contoso.com** .

    c. Klikněte na **Odeslat**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici AcquireIO, měli byste se automaticky přihlásit k AcquireIO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si AcquireIO s Azure AD](https://aad.portal.azure.com/)
