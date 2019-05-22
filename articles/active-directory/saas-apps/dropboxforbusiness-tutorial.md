---
title: 'Kurz: Integrace Azure Active Directory s Dropbox for Business | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2109c3f67a666d0c379cc188c07968c8b31d6a1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989671"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Kurz: Integrace Dropboxu pro firmy s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Dropboxu pro firmy s Azure Active Directory (Azure AD). Při integraci Dropboxu pro firmy s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup na Dropbox for Business.
* Aby uživatelé mohli být automaticky přihlášeni k Dropboxu pro firmy pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat měsíční zkušební verze [tady](https://azure.microsoft.com/pricing/free-trial/).
* Z Dropboxu obchodní jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

* V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Dropbox pro firmy podporuje **SP** jednotné přihlašování zahájené pomocí

* Dropbox pro firmy podporuje **JIT** zřizování uživatelů

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Dropbox for Business přidat z Galerie

Pokud chcete nakonfigurovat integraci Dropbox for Business do služby Azure AD, budete muset Dropbox for Business přidat z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Dropbox for Business** do vyhledávacího pole.
1. Vyberte **Dropbox for Business** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD se z Dropboxu podnikání díky informacím testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Dropboxu pro firmy.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Dropbox for Business, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Dropboxu pro jednotné přihlašování obchodní](#configure-dropbox-for-business-sso)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Dropboxu pro obchodní testovacího uživatele](#create-dropbox-for-business-test-user)**  – Pokud chcete mít protějšek Britta Simon v Dropboxu pro podniky, které souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Dropbox for Business** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** stránky, zadejte hodnoty pro následující pole:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.dropbox.com/sso/<id>`

    b. V **identifikátor (Entity ID)** textového pole zadejte hodnotu: `Dropbox`

    > [!NOTE]
    > Předchozí hodnota přihlašovací adresa URL není skutečné hodnoty. Hodnota bude aktualizován skutečné přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

1. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Na **nastavení Dropbox for Business** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-dropbox-for-business-sso"></a>Konfigurace Dropbox for Business jednotného přihlašování

1. K automatizaci konfigurace v rámci Dropbox for Business, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **Dropboxu instalační program pro firmy** nasměruje na Dropbox podnikových aplikací. Odtud zadejte přihlašovací údaje správce pro přihlášení na Dropbox for Business. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 – 8.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete ručně nastavit Dropbox for Business, otevřete nové okno webového prohlížeče a přejděte na váš Dropbox pro obchodní tenanta a přihlášení do vaší schránky pro obchodní tenanta. a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/ic769509.png "nakonfigurovat jednotné přihlašování")

4. Klikněte na **ikonu uživatele** a vyberte **nastavení** kartu.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure1.png "nakonfigurovat jednotné přihlašování")

5. V navigačním podokně na levé straně klikněte na tlačítko **konzoly pro správu**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure2.png "nakonfigurovat jednotné přihlašování")

6. Na **konzoly pro správu**, klikněte na tlačítko **nastavení** v levém navigačním podokně.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure3.png "nakonfigurovat jednotné přihlašování")

7. Vyberte **jednotného přihlašování** v části **ověřování** oddílu.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure4.png "nakonfigurovat jednotné přihlašování")

8. V **jednotného přihlašování** části, proveďte následující kroky:  

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure5.png "nakonfigurovat jednotné přihlašování")

    a. Vyberte **vyžaduje** jako možnost z rozevíracího seznamu **jednotného přihlašování**.

    b. Klikněte na **přidat přihlašovací adresu URL** a **přihlašovací adresa URL zprostředkovatele Identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z Azure portal a potom vyberte  **Provádí**.

    ![Konfigurace jednotného přihlašování](./media/dropboxforbusiness-tutorial/configure6.png "nakonfigurovat jednotné přihlašování")

    c. Klikněte na tlačítko **nahrát certifikát**a potom přejděte k vaší **soubor certifikátu v kódování Base64** který jste si stáhli z portálu Azure portal.

    d. Klikněte na **Kopírovat odkaz** a vložte zkopírovaný hodnotu do **přihlašovací adresa URL** textové pole z **Dropboxu obchodní domény a adresy URL** části na webu Azure portal.

    e. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na Dropbox for Business.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Dropbox for Business**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-dropbox-for-business-test-user"></a>Vytvoření Dropboxu pro obchodní testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Dropboxu pro firmy. Dropbox for Business podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Dropboxu pro firmy, se po ověření vytvoří nový.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory obchodních klienta z Dropboxu](https://www.dropbox.com/business/contact)

### <a name="test-sso"></a>Test SSO

Když vyberete Dropboxu pro obchodní dlaždici na přístupovém panelu, můžete by měl být automaticky přihlášeni na Dropbox for Business, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)