---
title: 'Kurz: Integrace Azure Active Directory s monday.com | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c5e473c62dc6b38f0b2c2906560d6099842d49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718493"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>Kurz: Monday.com integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat monday.com s Azure Active Directory (Azure AD). Když integrujete monday.com s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k monday.com.
* Aby uživatelé mohli být automaticky přihlášeni k monday.com pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Monday.com jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. podporuje Monday.com **SP a zprostředkovatele identity** iniciované jednotné přihlašování a podporuje **JIT** zřizování uživatelů.

## <a name="adding-mondaycom-from-the-gallery"></a>Přidání monday.com z Galerie

Konfigurace integrace monday.com do služby Azure AD, budete muset přidat monday.com z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **monday.com** do vyhledávacího pole.
1. Vyberte **monday.com** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s monday.com pomocí testovacího uživatele volá **B.Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v monday.com.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s monday.com, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace monday.com](#configure-mondaycom)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s B.Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující B.Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele monday.com](#create-mondaycom-test-user)**  mít protějšek B.Simon v monday.com, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **monday.com** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. V **základní konfiguraci SAML** podokně, pokud máte soubor zprostředkovatele metadat služby a chcete provést konfiguraci v **zahájené pomocí IDP** režimu, proveďte následující kroky:

    1. Vyberte **nahrát soubor metadat**.

    1. Pokud chcete vybrat soubor metadat, vyberte ikonu složky a pak vyberte **nahrát**.

    1. Po úspěšném odeslání souboru metadat **identifikátor** a **adresy URL odpovědi** hodnoty se automaticky vyplní v **základní konfiguraci SAML** podokna:

       > [!Note]
       > Pokud **identifikátor** a **adresy URL odpovědi** hodnoty získat není vyplněné automaticky a potom zadejte hodnoty ručně. **Identifikátor** a **adresy URL odpovědi** jsou stejné a hodnota je v následujícímu vzoru: `https://<your-domain>.monday.com/saml/saml_callback`

1. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<your-domain>.monday.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory klienta monday.com](mailto:dev@food.ee) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

1. Vaše aplikace monday.com očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![Podokno atributy uživatele](common/edit-attribute.png)

1. Kromě toho výše monday.com aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Odeberte **Namespace**.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **OK**.

    g. Klikněte na **Uložit**.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení monday.com** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>Konfigurace monday.com

1. K automatizaci konfigurace v rámci monday.com, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče, klikněte na **nastavení monday.com** který nasměruje na monday.com aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do monday.com. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 6.

    ![Nastavení konfigurace](common/setup-sso.png)

1. Pokud chcete nastavit monday.com ručně, otevřete nové okno webového prohlížeče a přihlaste se k monday.com jako správce a proveďte následující kroky:

1. Přejděte **profilu** v pravém horním rohu stránky a klikněte na kartu **správce**.

    ![Konfigurace Monday.com](./media/mondaycom-tutorial/configuration01.png)

1. Vyberte **zabezpečení** a ujistěte se, že na **otevřít** vedle SAML.

    ![Konfigurace Monday.com](./media/mondaycom-tutorial/configuration02.png)

1. Zadejte níže uvedené podrobnosti ze svého zprostředkovatele identity.

    ![Konfigurace Monday.com](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >Další informace naleznete v [to](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) článku

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

V této části povolíte B.Simon používat jednotné přihlašování Azure tím, že udělíte přístup k monday.com.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **monday.com**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **B.Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-mondaycom-test-user"></a>Vytvoření monday.com testovacího uživatele

V této části se vytvoří uživateli B.Simon v monday.com. Monday.com podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi monday.com, vytvoří se při pokusu o přístup k monday.com nový.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice monday.com na přístupovém panelu, můžete by měl být automaticky přihlášeni k monday.com, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
