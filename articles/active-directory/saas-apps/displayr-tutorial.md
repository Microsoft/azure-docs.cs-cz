---
title: 'Kurz: Integrace Azure Active Directory s Displayr | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7a2d793a1fbd68d6a71f48b556a77ddcaaaf111
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66112146"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Kurz: Displayr integrovat s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Displayr s Azure Active Directory (Azure AD). Když integrujete Displayr s Azure AD, můžete:

* Ovládací prvek ve službě Azure AD, který má přístup k Displayr.
* Aby uživatelé mohli být automaticky přihlášeni k Displayr pomocí jejich účtů služby Azure AD.
* Správa účtů v jednom centrálním místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Displayr jednotné přihlašování (SSO) povolené předplatné.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurovat a otestovat jednotné přihlašování služby Azure AD v testovacím prostředí. Podporuje Displayr **SP** jednotné přihlašování zahájené.

## <a name="adding-displayr-from-the-gallery"></a>Přidání Displayr z Galerie

Konfigurace integrace Displayr do služby Azure AD, budete muset přidat Displayr z Galerie na váš seznam spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně, vyberte **Azure Active Directory** služby.
1. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, **novou aplikaci**.
1. V **přidat z Galerie** části, zadejte **Displayr** do vyhledávacího pole.
1. Vyberte **Displayr** z výsledků panelu a pak přidat aplikaci. Počkejte několik sekund, zatímco aplikace se přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Konfigurace a otestování jednotného přihlašování k Azure AD s Displayr pomocí testovacího uživatele volá **Britta Simon**. Pro jednotné přihlašování pro práci budete muset vytvořit vztah odkazu mezi uživatele služby Azure AD a související uživatel v Displayr.

Nakonfigurovat a otestovat jednotné přihlašování služby Azure AD s Displayr, proveďte následující stavebních bloků:

1. **[Konfigurace jednotného přihlašování k Azure AD](#configure-azure-ad-sso)**  aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Displayr](#configure-displayr)**  ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Displayr](#create-displayr-test-user)**  mít protějšek Britta Simon Displayr, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-sso)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování k Azure AD

Použijte následující postup povolení jednotného přihlašování Azure AD na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com/)na **Displayr** stránky integrace aplikací, najdete **spravovat** a vyberte **jednotného přihlašování**.
1. Na **vybrat jedinou metodu přihlašování** stránce **SAML**.
1. Na **nastavení jednotného přihlašování pomocí SAML** stránky, klikněte na ikonu úprav/pera **základní konfiguraci SAML** můžete upravit nastavení.

   ![Upravit konfiguraci základní SAML](common/edit-urls.png)

1. Na **základní konfiguraci SAML** části, postupujte následovně:

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<YOURDOMAIN>.displayr.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Displayr klienta](mailto:support@displayr.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají uvedené v části základní konfiguraci SAML na webu Azure Portal.

1. Na **nastavení jednotného přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** části, Najít **certifikát (Base64)** a vyberte **Stáhnout** stáhněte certifikát a uložte ho do počítače.

   ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. Displayr aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

1. Kromě toho výše Displayr aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **atributy uživatele a deklarace identity** části na **deklarace skupiny (Preview)** dialogového okna, proveďte následující kroky:

    a. Klikněte na tlačítko **pera** vedle **skupiny vrácené v deklaraci identity**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Vyberte **všechny skupiny** ze seznamu přepínač.

    c. Vyberte **zdrojového atributu** z **ID skupiny**.

    d. Zkontrolujte **přizpůsobit název deklarace skupiny**.

    e. Zkontrolujte **generování skupin jako deklarací identity rolí**.

    f. Klikněte na **Uložit**.

1. Na **nastavení Displayr** tématu, zkopírujte příslušné adresy URL na základě vašich požadavků.

   ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurace Displayr

1. K automatizaci konfigurace v rámci Displayr, je potřeba nainstalovat **Moje aplikace zabezpečené přihlašování rozšíření prohlížeče** kliknutím **nainstalovat rozšíření**.

    ![Moje aplikace rozšíření](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče, klikněte na **nastavení Displayr** nasměruje na Displayr aplikace. Odtud zadejte přihlašovací údaje správce pro přihlášení do Displayr. Rozšíření prohlížeče budou automaticky nakonfigurovat aplikaci za vás a automatizovat kroky 3 až 6.

    ![Nastavení konfigurace](common/setup-sso.png)

3. Pokud chcete nastavit Displayr ručně, otevřete nové okno webového prohlížeče a přihlaste na webu společnosti Displayr jako správce a proveďte následující kroky:

4. Klikněte na **nastavení** přejděte na **účet**.

    ![Konfigurace](./media/displayr-tutorial/config01.png)

5. Přepnout na **nastavení** v horní nabídce a přejděte dolů na stránce pro kliknutí na **nakonfigurovat jednotné přihlašování na (SAML)**.

    ![Konfigurace](./media/displayr-tutorial/config02.png)

6. Na **jednotné přihlašování na (SAML)** stránce, proveďte následující kroky:

    ![Konfigurace](./media/displayr-tutorial/config03.png)

    a. Zkontrolujte, **povolit jednotné přihlašování na (SAML)** pole.

    b. Zkopírujte skutečnou **identifikátor** hodnotu **základní konfiguraci SAML** část Azure AD a vložte ho do **vystavitele** textového pole.

    c. V **přihlašovací adresa URL** text vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL** textové pole, vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. V poznámkovém bloku otevřete certifikát (Base64), zkopírujte jeho obsah a vložte ho do **certifikát** textového pole.

    f. **Seskupit mapování** jsou volitelné.

    g. Klikněte na **Uložit**.  

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

V této části se budou moci používat jednotné přihlašování Azure díky udělení přístupu k Displayr Britta Simon.

1. Na webu Azure Portal, vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikací vyberte **Displayr**.
1. Na stránce Přehled aplikace najít **spravovat** a vyberte **uživatelů a skupin**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** ze seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.
1. Pokud očekáváte libovolná hodnota role v kontrolní výraz SAML v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.
1. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-displayr-test-user"></a>Vytvoření Displayr testovacího uživatele

Azure AD uživatelům umožnit přihlásit v k Displayr, že musí být poskytnuty do Displayr. Zřizování v Displayr, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Displayr jako správce.

2. Klikněte na **nastavení** přejděte na **účet**.

    ![Konfigurace Displayr](./media/displayr-tutorial/config01.png)

3. Přepnout na **nastavení** v horní nabídce a přejděte dolů na stránce dokud **uživatelé** části a potom klikněte na **nového uživatele**.

    ![Konfigurace Displayr](./media/displayr-tutorial/config07.png)

4. Na **nového uživatele** stránce, proveďte následující kroky:

    ![Konfigurace Displayr](./media/displayr-tutorial/config06.png)

    a. V **název** textové pole, zadejte jméno uživatele, jako je **Brittasimon**.

    b. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je `Brittasimon@contoso.com`.

    c. Vyberte vaše příslušné **členství ve skupině**.

    d. Klikněte na **Uložit**.

### <a name="test-sso"></a>Test SSO

Při výběru dlaždice Displayr na přístupovém panelu, můžete by měl být automaticky přihlášeni k Displayr, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)