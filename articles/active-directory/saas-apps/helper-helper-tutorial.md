---
title: 'Kurz: Integrace Azure Active Directory s pomocné rutiny pomocné rutiny | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pomocné rutiny pomocné rutiny.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5f42e4d7-4d92-4096-a0d5-02fa438a5dfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d70cdade93b028c90c9f62374cd6b997556dd52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101290"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Kurz: Integrace s Azure Active Directory pomocné rutiny pomocné rutiny

V tomto kurzu se dozvíte, jak zajistit integraci s Azure Active Directory (Azure AD) pomocné rutiny pomocné rutiny. Při integraci pomocné rutiny pomocné rutiny s využitím Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k pomocné rutiny pomocné rutiny.
* Aby uživatelé mohli být automaticky přihlášeni do pomocné rutiny pomocné rutiny pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Pomocné rutiny pomocné rutiny jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Pomocné rutiny pomocné rutiny podporuje **SP a zprostředkovatele identity** iniciované jednotné přihlašování a podporuje **JIT** zřizování uživatelů.

## <a name="adding-helper-helper-from-the-gallery"></a>Přidání pomocné rutiny pomocné rutiny z Galerie

Pokud chcete nakonfigurovat integrace pomocné rutiny pomocné rutiny do služby Azure AD, potřebujete přidat pomocné rutiny pomocné rutiny z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **pomocné rutiny pomocné rutiny** do vyhledávacího pole.
1. Vyberte **pomocné rutiny pomocné rutiny** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s pomocné rutiny pomocné rutiny pomocí testovacího uživatele volá **B. Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v pomocné pomocné rutiny.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s pomocné rutiny pomocné rutiny, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte pomocné rutiny pomocné rutiny](#configure-helper-helper)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B. Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B. Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele pomocné rutiny pomocné rutiny](#create-helper-helper-test-user)**  mít protějšek B. Simon v pomocné rutiny pomocné rutiny, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **pomocné rutiny pomocné rutiny** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, pokud máte **soubor metadat poskytovatele služeb** a chcete nakonfigurovat v **IDP** iniciované režimu proveďte následující kroky:

    >[!NOTE]
    >Přejděte na adresu url `https://sso.helperhelper.com/saml/<customer_id>` získat metadata souboru poskytovatele služeb. Kontakt [tým podpory pomocné rutiny pomocné rutiny klienta](mailto:info@helperhelper.com) pro `<customer_id>`.

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v části základní konfiguraci SAML.

    > [!Note]
    > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty nechcete získat polulated automaticky, a potom zadejte hodnoty ručně podle vašich požadavků.

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory pomocné rutiny pomocné rutiny klienta](mailto:info@helperhelper.com) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** v Azure portal.l části.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ho do vašeho programu Poznámkový blok .

   ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

1. Na **nastavení pomocného pomocné rutiny** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Nakonfigurujte pomocné rutiny pomocné rutiny

Ke konfiguraci jednotného přihlašování na **pomocné rutiny pomocné rutiny** straně, je nutné odeslat **adresa Url federačních metadat aplikace** k [tým podpory pomocné rutiny pomocné rutiny](mailto:info@helperhelper.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části povolíte B. Simon používat jednotné přihlašování Azure tím, že uděluje přístup do pomocné rutiny pomocné rutiny.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **pomocné rutiny pomocné rutiny**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B. Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-helper-helper-test-user"></a>Vytvořit testovacího uživatele pomocné rutiny pomocné rutiny

V této části se vytvoří uživateli Britta Simon v pomocné pomocné rutiny. Pomocné rutiny pomocné rutiny podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v pomocné pomocné rutiny, se po ověření vytvoří nový.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice pomocné rutiny pomocné rutiny na přístupovém panelu, vám by měl být automaticky přihlášeni do pomocné rutiny pomocné rutiny, pro kterou můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)