---
title: 'Kurz: Integrace Azure Active Directory s AlertOps | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107059"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Kurz: AlertOps integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat AlertOps s Azure Active Directory (Azure AD). Když integrujete AlertOps s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k AlertOps.
* Aby uživatelé mohli být automaticky přihlášeni k AlertOps pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AlertOps jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje AlertOps **SP a zprostředkovatele identity** jednotné přihlašování zahájené.

## <a name="adding-alertops-from-the-gallery"></a>Přidání AlertOps z Galerie

Konfigurace integrace AlertOps do služby Azure AD, budete muset přidat AlertOps z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **AlertOps** do vyhledávacího pole.
1. Vyberte **AlertOps** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s AlertOps pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v AlertOps.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s AlertOps, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace AlertOps](#configure-alertops)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele AlertOps](#create-alertops-test-user)**  mít protějšek Britta Simon AlertOps, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **AlertOps** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    1. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.alertops.com`

    1. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory AlertOps klienta](mailto:support@alertops.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení AlertOps** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurace AlertOps

1. K automatizaci konfigurace v rámci AlertOps, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení AlertOps** nasměruje na AlertOps aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do AlertOps. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 5.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit AlertOps ručně, otevřete nové okno webového prohlížeče a přihlaste se jako správce společnosti lokalitu AlertOps a proveďte následující kroky:

4. Klikněte na **nastavení účtu** z levého navigačního panelu.

    ![Konfigurace AlertOps](./media/alertops-tutorial/configure1.png)

5. Na **nastavení odběru** stránce vyberte **jednotného přihlašování** a proveďte následující kroky:

    ![Konfigurace AlertOps](./media/alertops-tutorial/configure2.png)

    a. Vyberte **použití jedné Sign-On(SSO)** zaškrtávací políčko.

    b. Vyberte **Azure Active Directory** jako **poskytovatele jednotného přihlašování** z rozevíracího seznamu.

    c. V **URL vystavitele** textové pole, použijte hodnotu identifikátoru, který jste použili v **základní konfiguraci SAML** části webu Azure Portal.

    d. V **adresu URL koncového bodu SAML** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **adresu URL koncového bodu objektu SLO** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    f. Vyberte **SHA256** jako **algoritmus podpisu SAML** z rozevíracího seznamu.

    g. Otevřete stažený soubor Certificate(Base64) v poznámkovém bloku. Zkopírujte obsah ho do schránky a vložte ho do textového pole certifikátu X.509.

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

V této části se budou moci používat jednotné přihlašování Azure díky udělení přístupu k AlertOps Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **AlertOps**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-alertops-test-user"></a>Vytvoření AlertOps testovacího uživatele

1. V jiném okně prohlížeče Přihlaste se k webu společnosti AlertOps jako správce.

2. Klikněte na **uživatelé** z levého navigačního panelu.

    ![Konfigurace AlertOps](./media/alertops-tutorial/user1.png)

3. Vyberte **přidat uživatele**.

    ![Konfigurace AlertOps](./media/alertops-tutorial/user2.png)

4. Na **přidat uživatele** dialogového okna, proveďte následující kroky:

    ![Konfigurace AlertOps](./media/alertops-tutorial/user3.png)

    a. V **přihlašovací uživatelské jméno** textového pole zadejte uživatelské jméno uživatele, jako je **Brittasimon**.

    b. V **oficiální e-mailu** textového pole zadejte e-mailovou adresu uživatele, jako je **Brittasimon\@contoso.com**.

    c. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    d. V **příjmení** textového pole zadejte jméno uživatele, jako je **Simon**.

    e. Vyberte **typ** hodnotu z rozevíracího seznamu podle vaší organizace.

    f. Vyberte **Role** uživatele z rozevíracího seznamu podle vaší organizace.

    g. Vyberte **Přidat**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice AlertOps na přístupovém panelu, můžete by měl být automaticky přihlášeni k AlertOps, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)