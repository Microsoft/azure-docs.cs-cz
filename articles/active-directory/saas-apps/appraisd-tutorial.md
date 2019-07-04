---
title: 'Kurz: Integrace Azure Active Directory s Appraisd | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561200"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Kurz: Appraisd integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Appraisd s Azure Active Directory (Azure AD). Když integrujete Appraisd s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Appraisd.
* Aby uživatelé mohli být automaticky přihlášeni k Appraisd pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Appraisd jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Appraisd **SP a zprostředkovatele identity** jednotné přihlašování zahájené.

## <a name="adding-appraisd-from-the-gallery"></a>Přidání Appraisd z Galerie

Konfigurace integrace Appraisd do služby Azure AD, budete muset přidat Appraisd z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Appraisd** do vyhledávacího pole.
1. Vyberte **Appraisd** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Appraisd pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Appraisd.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Appraisd, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Appraisd](#configure-appraisd)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Appraisd](#create-appraisd-test-user)**  mít protějšek B. Simon Appraisd, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Appraisd** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části aplikace je předem nakonfigurovaný a potřebné adresy URL se už předem vyplní s Azure. Uživatel musí po kliknutí na tlačítko Uložit uložte konfiguraci a proveďte následující kroky:

    a. Klikněte na tlačítko **nastavit další adresy URL**.

    b. V **stav přenosu** textové pole, zadejte adresu URL: `<TENANTCODE>`

    c. Pokud chcete nakonfigurovat aplikace v **SP** zahájeno v režimu **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Na stránce Konfigurace jednotného přihlašování Appraisd, který je vysvětlen později v tomto kurzu získáte skutečnou hodnotu přihlašovací adresu URL a stav přenosu.

1. Appraisd aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace Appraisd **nameidentifier** namapovat s **user.mail**, takže budete muset upravit mapování atributů po kliknutí na **upravit** ikonu a změňte mapování atributů.

    ![image](common/edit-attribute.png)

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení Appraisd** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurace Appraisd

1. K automatizaci konfigurace v rámci Appraisd, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Appraisd** nasměruje na Appraisd aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do Appraisd. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 – 7.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit Appraisd ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Appraisd jako správce a proveďte následující kroky:

4. V horní části stránky, klikněte na **nastavení** ikonu, přejděte na **konfigurace**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. V levé nabídce klikněte na **SAML jednotného přihlašování**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na **konfigurace SAML 2.0 Single Sign-On** stránce, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopírovat **výchozí stav přenosu** hodnotu a vložte ji **stav přenosu** textového pole v **základní konfiguraci SAML** na portálu Azure portal.

    b. Kopírovat **spouštěných službou přihlašovací adresa URL** hodnotu a vložte ji **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** na portálu Azure portal.

7. Přejděte dolů na stejné stránce v části **identifikace uživatelů**, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. V **jednotné přihlašování – adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z webu Azure portal a klikněte na tlačítko **Uložit**.

    b. V **adresa URL Vystavitel zprostředkovatele Identity** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal a klikněte na **Uložit**.

    c. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah a vložte jej do **certifikát X.509** pole a klikněte na tlačítko **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B. Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `B. Simon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon k udělení přístupu k Appraisd použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Appraisd**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-appraisd-test-user"></a>Vytvoření Appraisd testovacího uživatele

Povolení služby Azure AD uživatelé přihlašují k Appraisd, se musí být poskytnuty do Appraisd. Zřizování v Appraisd, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Appraisd jako správce zabezpečení.

2. V horní části stránky, klikněte na **nastavení** ikonu, přejděte na **Centrum správy**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na panelu nástrojů v horní části stránky klikněte na tlačítko **lidé**, přejděte na **přidat nového uživatele**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na **přidat nového uživatele** stránce, proveďte následující kroky:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    c. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je `B. Simon@contoso.com`.

    d. Klikněte na **Přidat uživatele**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Appraisd na přístupovém panelu, můžete by měl být automaticky přihlášeni k Appraisd, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
