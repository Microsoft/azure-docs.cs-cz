---
title: 'Kurz: Integrace Azure Active Directory s Freedcamp | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aabdba16b334aa957e1e8109d1e16d22e01dc7
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987831"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Kurz: Freedcamp integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Freedcamp s Azure Active Directory (Azure AD). Když integrujete Freedcamp s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Freedcamp.
* Aby uživatelé mohli být automaticky přihlášeni k Freedcamp pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Freedcamp jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Freedcamp **SP a zprostředkovatele identity** jednotné přihlašování zahájené.

## <a name="adding-freedcamp-from-the-gallery"></a>Přidání Freedcamp z Galerie

Konfigurace integrace Freedcamp do služby Azure AD, budete muset přidat Freedcamp z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Freedcamp** do vyhledávacího pole.
1. Vyberte **Freedcamp** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Freedcamp pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Freedcamp.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Freedcamp, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Freedcamp](#configure-freedcamp)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Freedcamp](#create-freedcamp-test-user)**  mít protějšek Britta Simon Freedcamp, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Freedcamp** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    1. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Uživatele můžete také zadat hodnoty adresy url s ohledem na své vlastní domény, zákazníků a nemusí být nutně vzoru `freedcamp.com`, mohou zadat jakékoli domény konkrétní hodnotu zákazníka, specifické pro jejich instance aplikace. Můžete také kontaktovat [tým podpory Freedcamp klienta](mailto:devops@freedcamp.com) pro další informace o vzorech adres url.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení Freedcamp** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurace Freedcamp

1. K automatizaci konfigurace v rámci Freedcamp, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Freedcamp** nasměruje na Freedcamp aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do Freedcamp. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 5.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit Freedcamp ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Freedcamp jako správce a proveďte následující kroky:

4. V pravém horním rohu stránky, klikněte na **profilu** a přejděte na **Můj účet**.

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config01.png)

5. V levé části řádku nabídek klikněte na **jednotného přihlašování** a na **si jednotné přihlašování připojení** stránku, proveďte následující kroky:

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config02.png)

    a. V **název** textové pole, zadejte název.

    b. V **Entity ID** textového pole, Vložit **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **přihlašovací adresa URL** textového pole, Vložit **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. V poznámkovém bloku otevřete certifikát kódovaný v Base64, zkopírujte jeho obsah a vložte ho do **certifikát** textového pole.

    e. Klikněte na **Submit** (Odeslat).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá Britta Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části se budou moci používat jednotné přihlašování Azure díky udělení přístupu k Freedcamp Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Freedcamp**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-freedcamp-test-user"></a>Vytvoření Freedcamp testovacího uživatele

Azure AD uživatelům umožnit přihlásit v k Freedcamp, že musí být poskytnuty do Freedcamp. Zřizování v Freedcamp, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. V okně jiné webové prohlížeče Přihlaste se k Freedcamp jako správce zabezpečení.

2. V horní části toright horním rohu stránky, klikněte na **profilu** a přejděte na **spravovat systém**.

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Na pravé straně stránky Správa systému proveďte následující kroky:

    ![Konfigurace Freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Klikněte na **přidat nebo pozvání uživatelů**.

    b. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je `Brittasimon@contoso.com`.

    c. Klikněte na tlačítko **přidat uživatele**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Freedcamp na přístupovém panelu, můžete by měl být automaticky přihlášeni k Freedcamp, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)