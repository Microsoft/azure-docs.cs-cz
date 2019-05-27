---
title: 'Kurz: Integrace Azure Active Directory s LMS znalostní báze kdekoli | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LMS znalostní báze kdekoli.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34553c92f54992698a7e3930ac302ef970edd7c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236657"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Kurz: Integrace znalostní báze kdekoli LMS s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat LMS znalostní báze odkudkoli pomocí služby Azure Active Directory (Azure AD). Při integraci LMS znalostní báze odkudkoli pomocí služby Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k LMS znalostní báze kdekoli.
* Aby uživatelé mohli být automaticky přihlášeni LMS kdekoli znalostní báze pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Znalostní báze kdekoli LMS jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje LMS znalostní báze kdekoli **SP** iniciované jednotné přihlašování a podporuje **JIT** zřizování uživatelů.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Přidání znalostní báze kdekoli LMS z Galerie

Ke konfiguraci integrace LMS znalostní báze kamkoli do služby Azure AD, budete muset přidat LMS znalostní báze kdekoli v galerii na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **LMS znalostní báze kdekoli** do vyhledávacího pole.
1. Vyberte **LMS znalostní báze kdekoli** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s znalostní báze kdekoli LMS pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LMS znalostní báze kdekoli.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s LMS kdekoli znalostní báze, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace LMS znalostní báze kdekoli](#configure-knowledge-anywhere-lms)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele LMS znalostní báze kdekoli](#create-knowledge-anywhere-lms-test-user)**  mít protějšek B. Simon ve znalostní bázi kdekoli LMS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **LMS znalostní báze kdekoli** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    1. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi, který je vysvětlen později v tomto kurzu.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta LMS v znalostní báze kdekoli](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení LMS znalostní báze kdekoli** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurace znalostní báze kdekoli LMS

1. K automatizaci konfigurace v rámci LMS znalostní báze kdekoli, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení znalostní báze kdekoli LMS** nasměruje na aplikaci LMS znalostní báze kdekoli. Odtud zadejte přihlašovací údaje správce pro přihlášení do LMS znalostní báze kdekoli. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 – 7.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete ručně nastavit LMS znalostní báze kdekoli, otevřete nové okno webového prohlížeče a přihlaste se jako správce lokalitu LMS kdekoli znalostní báze společnosti a proveďte následující kroky:

4. Vyberte na **lokality** kartu.

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Vyberte na **nastavení SAML** kartu.

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klikněte na **přidat nový**.

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na **přidat nebo aktualizovat nastavení SAML** stránce, proveďte následující kroky:

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Zadejte název zprostředkovatele identity podle vaší organizace. Pro příklad:- `Azure`.

    b. V **IDP Entity ID** vložit do textového pole **Azure AD identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **adresa URL zprostředkovatele identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. Do poznámkového bloku otevřete soubor stažený certifikát z portálu Azure portal, zkopírujte obsah certifikát a vložte ho do **certifikát** textového pole.

    e. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    f. Vyberte **hlavní lokalitu** z rozevíracího seznamu **domény**.

    g. Kopírovat **SP Entity ID** hodnotu a vložte ho do **identifikátor** textové pole **základní konfiguraci SAML** části webu Azure Portal.

    h. Kopírovat **SP Response(ACS) URL** hodnotu a vložte ho do **adresy URL odpovědi** textové pole **základní konfiguraci SAML** části webu Azure Portal.

    i. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele na webu Azure Portal volá B. Simon.

1. V levém podokně webu Azure Portal vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. V **uživatele** vlastností, postupujte podle těchto kroků:
   1. Do pole **Název** zadejte `B. Simon`.  
   1. V **uživatelské jméno** zadejte username@companydomain.extension. Například, `BrittaSimon@contoso.com`.
   1. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování Azure díky udělení přístupu LMS znalostní báze kdekoli.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **LMS znalostní báze kdekoli**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Vytvořit testovacího uživatele LMS kdekoli znalostní báze

V této části se vytvoří uživateli B. Simon ve LMS znalostní báze kdekoli. LMS kdekoli znalostní báze podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi LMS znalostní báze kdekoli, vytvoří se nový po ověření.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice LMS znalostní báze kamkoli na přístupovém panelu, vám by měl být automaticky přihlášeni do znalostní báze kdekoli LMS pro kterou můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)