---
title: 'Kurz: Integrace Azure Active Directory s ExpenseIn | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ExpenseIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 6ac8053b-a216-45d8-bf5e-ecd37d808e57
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c09542013dff3a18965d1070216a938c26a144e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102842"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Kurz: ExpenseIn integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat ExpenseIn s Azure Active Directory (Azure AD). Když integrujete ExpenseIn s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k ExpenseIn.
* Aby uživatelé mohli být automaticky přihlášeni k ExpenseIn pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* ExpenseIn jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje ExpenseIn **SP a zprostředkovatele identity** jednotné přihlašování zahájené.

## <a name="adding-expensein-from-the-gallery"></a>Přidání ExpenseIn z Galerie

Konfigurace integrace ExpenseIn do služby Azure AD, budete muset přidat ExpenseIn z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **ExpenseIn** do vyhledávacího pole.
1. Vyberte **ExpenseIn** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s ExpenseIn pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v ExpenseIn.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s ExpenseIn, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace ExpenseIn](#configure-expensein)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele ExpenseIn](#create-expensein-test-user)**  mít protějšek B.Simon ExpenseIn, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **ExpenseIn** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    V **adresy URL odpovědi** textové pole, zadejte některou z adresy URL:

    | |
    |--|
    | `https://app.expensein.com/samlcallback` |
    | `https://mobileapi.expensein.com/identity/samlcallback` |

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://app.expensein.com/saml`

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Na **nastavení ExpenseIn** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-expensein"></a>Konfigurace ExpenseIn

1. K automatizaci konfigurace v rámci ExpenseIn, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení ExpenseIn** nasměruje na ExpenseIn aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do ExpenseIn. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 5.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit ExpenseIn ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti ExpenseIn jako správce a proveďte následující kroky:

4. Klikněte na **správce** přejděte do horní části stránky **Single Sign-On** a klikněte na tlačítko **přidat poskytovatele**.

     ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config01.png)

5. Na **nového zprostředkovatele Identity** rozbalovací, proveďte následující kroky:

    ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config02.png)

    a. V **název zprostředkovatele** textové pole, zadejte název, jako je např: Azure.

    b. Vyberte **Ano** jako **povolit přihlašování Intitated poskytovatele**.

    c. V **cílové adrese Url** text vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. V **vystavitele** text vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    e. V poznámkovém bloku otevřete certifikát (Base64), zkopírujte jeho obsah a vložte ji **certifikát** textového pole.

    f. Klikněte na možnost **Vytvořit**.

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

V této části povolíte B.Simon k udělení přístupu k ExpenseIn použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **ExpenseIn**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-expensein-test-user"></a>Vytvoření ExpenseIn testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k ExpenseIn, musí být poskytnuty do ExpenseIn. Zřizování v ExpenseIn, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k ExpenseIn jako správce.

2. Klikněte na **správce** přejděte do horní části stránky **uživatelé** a klikněte na tlačítko **nového uživatele**.

     ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config03.png)

3. Na **podrobnosti** rozbalovací, proveďte následující kroky:

    ![Konfigurace ExpenseIn](./media/expenseIn-tutorial/config04.png)

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **B**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **Simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je `B.Simon@contoso.com`.

    d. Klikněte na možnost **Vytvořit**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice ExpenseIn na přístupovém panelu, můžete by měl být automaticky přihlášeni k ExpenseIn, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
