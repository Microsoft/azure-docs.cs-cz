---
title: 'Kurz: Integrace Azure Active Directory se způsobem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a způsobem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 0205617198ec8e57cb891d2415d2b5413a945f1b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608931"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Kurz: integrace způsobu práce s Azure Active Directory

V tomto kurzu se naučíte, jak integrovat způsob Azure Active Directory (Azure AD). Když integruje způsob, jakým se s Azure AD pracujete, můžete:

* Řízení ve službě Azure AD, která má přístup k vašemu způsobu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k vašim účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Způsob, jakým se používá předplatné s jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Způsob podporujeme jednotné přihlašování pomocí služeb **SP** .
* Způsob, jakým podporujeme zřizování uživatelů **jenom v čase**

## <a name="adding-way-we-do-from-the-gallery"></a>Přidání způsobu, jakým provedeme z Galerie

Pokud chcete nakonfigurovat integraci způsobu, jakým se chystáme do Azure AD, musíte přidat způsob, jakým provedeme z Galerie do vašeho seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte **způsob** psaní do vyhledávacího pole.
1. Vyberte **způsob, jak provedeme** z panelu výsledků, a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí způsobu, jakým využíváme testovacího uživatele s názvem **B. Simon**. K tomu, aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem způsobem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí tohoto postupu, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte způsob](#configure-way-we-do-sso)** , jak nakonfigurovat jednotné přihlašování pro nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořením způsobu, jakým povedeme testovacího uživatele](#create-way-we-do-test-user)** , aby měl protějšek Britta Simon, který je propojený s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **způsob** integrace aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Kontaktní [způsob, jak tým podpory klientů](mailto:support@waywedo.com) potřebuje získat tyto hodnoty. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **nastavit způsob, jak** na to máme, zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurace způsobu, jakým připravujeme jednotné přihlašování

1. Chcete-li automatizovat konfiguraci v rámci své možnosti, je nutné nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na tlačítko **nainstalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Nastavení způsob, jakým** vás nasměrujeme na způsob provádění aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení k vašemu způsobu. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete nastavit způsob ručního nastavení, otevřete nové okno webového prohlížeče a přihlaste se svým způsobem jako správce a proveďte následující kroky:

1. V pravém horním rohu libovolné stránky klikněte na **ikonu osoba** a pak v rozevírací nabídce klikněte na **účet** .

    ![Způsob, jakým mám na účet](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Kliknutím na **ikonu nabídky** otevřete navigační nabídku nabízených oznámení a klikněte na **jednotné přihlašování**.

    ![Způsob, jakým máme jednu](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na stránce **instalace jednotného přihlašování** proveďte následující kroky:

    ![Způsob ukládání](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. Pokud chcete povolit jednotné přihlašování, klikněte na přepínač **zapnout jednotné přihlašování** a klikněte na **Ano** .

    1. Do textového pole **název jednotného přihlašování** zadejte své jméno.

    1. Do textového pole **ID entity** vložte hodnotu **identifikátoru služby Azure AD**, který jste zkopírovali z Azure Portal.

    1. Do textového pole **URL jednotného přihlašování SAML** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    1. Nahrajte certifikát kliknutím na tlačítko **Vybrat** vedle **certifikátu**.

    1. **Volitelná nastavení** -

        * Povolit hesla – Pokud je tato možnost zakázaná, běžné heslo funguje tak, aby uživatelé mohli používat jenom jednotné přihlašování.

        * Povolit automatické zřizování – Pokud je tato možnost povolená, e-mailová adresa použitá k přihlášení se automaticky porovná se seznamem uživatelů v cestě. Pokud se e-mailová adresa neshoduje s aktivním uživatelem, automaticky přidá nový uživatelský účet pro uživatele, který se přihlašuje, a požaduje všechny chybějící informace.

          > [!NOTE]
          > Uživatelé přidaní prostřednictvím jednotného přihlašování se přidávají jako základní uživatelé a v systému se nepřiřazují role. Správce je schopný přejít do role zabezpečení a změnit ji jako editor nebo správce a může také přiřadit jednu nebo několik rolí organizačního diagramu.

    1. Klikněte na **Uložit** a zachovejte nastavení.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k cestě.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte způsob, **jakým máme**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-way-we-do-test-user"></a>Vytvořit způsob testování uživatele

V této části se v takovém případě vytvoří uživatel s názvem Britta Simon. Způsob, jakým budeme podporovat zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel už v takovém případě neexistuje, vytvoří se po ověření nový.

> [!Note]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory klienta](mailto:support@waywedo.com).

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu kliknete na dlaždici způsob, že se přihlásíte k tomu, že jste nastavili jednotné přihlašování, musíte se automaticky přihlásit. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)