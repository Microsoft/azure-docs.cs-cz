---
title: 'Kurz: Integrace Azure Active Directory s způsob děláme | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a způsob, jak vidíme.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310411"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Kurz: Integrace způsob, jakým jsme to Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat způsob, jak jsme se s Azure Active Directory (Azure AD). Při integraci děláme způsob, jak s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k způsob děláme.
* Aby uživatelé mohli být automaticky přihlášeni způsob, jak můžeme udělat pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Způsob, jak jsme jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí.

* Způsob, jak děláme podporuje **SP** jednotné přihlašování zahájené pomocí
* Způsob, jak děláme podporuje **JIT** zřizování uživatelů

## <a name="adding-way-we-do-from-the-gallery"></a>Přidání způsob, jak můžeme udělat z Galerie

Pokud chcete nakonfigurovat integraci děláme způsob, jak do služby Azure AD, musíte doplnit způsob, jak můžeme udělat z Galerie váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **způsob děláme** do vyhledávacího pole.
1. Vyberte **způsob děláme** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s způsob, jak můžeme udělat pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v způsob, jak jsme to.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s způsob děláme, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat způsob, jak můžeme udělat jednotného přihlašování](#configure-way-we-do-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření způsob, jak jsme testovací uživatele](#create-way-we-do-test-user)**  – Pokud chcete mít protějšek Britta Simon způsob, jak to je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **způsob děláme** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory způsob, jak můžeme udělat klienta](mailto:support@waywedo.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Raw)** a vyberte **Stáhnout**stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificateraw.png)

1. Na **nastavit způsob děláme** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurace způsobu, jakým děláme jednotného přihlašování

1. K automatizaci konfigurace v rámci způsob děláme, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče, klikněte na **nastavení způsob děláme** vás nasměrujeme na to uděláme způsob, jak aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do způsob děláme. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 6.

    ![Nastavení konfigurace](common/setup-sso.png)

1. Pokud chcete nastavit způsob, jak můžeme udělat ručně, otevřete nové okno webového prohlížeče a přihlaste se do serveru vaší společnosti způsob, jak můžeme udělat jako správce a proveďte následující kroky:

1. Klikněte na tlačítko **osoba ikonu** v pravém horním rohu jakékoli stránky v způsob, jak jsme to klikněte **účtu** v rozevírací nabídce.

    ![Způsob, jak děláme účtu](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klikněte na tlačítko **ikonu nabídky** otevřete navigační nabídky nabízených oznámení a klikněte na tlačítko **Single Sign On**.

    ![Způsob, jak můžeme udělat jeden](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na **nastavení přihlašování –** stránce, proveďte následující kroky:

    ![Způsob, jak děláme uložit](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klikněte na tlačítko **zapnout jednotné přihlašování** přepnutím **Ano** povolit jednotné přihlašování.

    b. V **jednotné přihlašování – název** textového pole zadejte své jméno.

    c. V **Entity ID** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    d. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Nahrajte certifikát po kliknutí **vyberte** vedle **certifikát**.

    f. **Volitelná nastavení** -
    
    * Hesla – povolit po tato možnost je zakázaná, pravidelné heslo funkce pro způsob, jak můžeme udělat tak, aby uživatelé mohou použít pouze jednotné přihlašování.

    * Povolení automatického zřizování – když je tato možnost povolena, bude e-mailovou adresu používá k přihlašování do seznamu uživatelů v způsob, jak jsme to automaticky porovnání. Pokud e-mailovou adresu se neshoduje s aktivní uživatel v způsob děláme, automaticky se přidá nový uživatelský účet pro osobu, která se přihlašujete, požadování všechny chybějící informace.

      > [!NOTE]
      > Přidané prostřednictvím jednotného přihlašování uživatele jsou přidány jako obecný uživatel a nejsou přiřazena role v systému. Správce se zúčastnit a změnit jejich role zabezpečení jako editor nebo správce a můžete také přiřadit organizační diagram jednoho nebo několika rolí.

    g. Klikněte na tlačítko **Uložit** k uchování vašeho nastavení.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B.Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B.Simon používat jednotné přihlašování Azure díky udělení přístupu způsob, jak můžeme udělat.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **způsob děláme**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-way-we-do-test-user"></a>Vytvoření způsob děláme testovacího uživatele

V této části se vytvoří uživateli Britta Simon v způsob, jak jsme to. Způsob, jak děláme podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi způsob děláme, vytvoří se nový po ověření.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory způsob, jak můžeme udělat klienta](mailto:support@waywedo.com).

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice způsob, jak můžeme udělat na přístupovém panelu, vám by měl být automaticky přihlášeni způsob, jak můžeme udělat u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)