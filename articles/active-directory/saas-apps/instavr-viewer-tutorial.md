---
title: 'Kurz: Integrace Azure Active Directory pomocí prohlížeče InstaVR | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InstaVR prohlížeč.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13ffa29f-d0a5-4b21-b296-cfd76f380940
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 1f7162e4be1392a8b5b3228a5338fd470fdcc525
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389313"
---
# <a name="tutorial-azure-active-directory-integration-with-instavr-viewer"></a>Kurz: Integrace Azure Active Directory s InstaVR prohlížeč

V tomto kurzu se dozvíte, jak integrovat InstaVR prohlížeč s Azure Active Directory (Azure AD).
Prohlížeč InstaVR integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k InstaVR prohlížeč.
* Uživatelům se automaticky přihlášeni do prohlížeče InstaVR (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s InstaVR prohlížeč, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Prohlížeč InstaVR jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Prohlížeč InstaVR podporuje **SP** jednotné přihlašování zahájené pomocí
* Prohlížeč InstaVR podporuje **JIT** zřizování uživatelů

## <a name="adding-instavr-viewer-from-the-gallery"></a>Přidání InstaVR prohlížeče z Galerie

Ke konfiguraci integrace InstaVR prohlížeče do služby Azure AD, budete muset přidat InstaVR prohlížeče z Galerie na váš seznam spravovaných aplikací SaaS.

**Prohlížeč InstaVR přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **InstaVR prohlížeč**vyberte **InstaVR prohlížeč** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Prohlížeč InstaVR v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování pomocí prohlížeče InstaVR podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v prohlížeči InstaVR.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s InstaVR prohlížeče, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace InstaVR prohlížeč Single Sign-On](#configure-instavr-viewer-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele InstaVR prohlížeč](#create-instavr-viewer-test-user)**  – Pokud chcete mít protějšek Britta Simon InstaVR prohlížeč, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s InstaVR prohlížeč, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **InstaVR prohlížeč** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![InstaVR prohlížeč domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://console.instavr.co/auth/saml/login/<WEBPackagedURL>`

    > [!NOTE]
    > Neexistuje žádná pevná vzor pro přihlášení na adrese URL. To se vygeneruje, když zákazník InstaVR prohlížeč webové balení. Je jedinečný pro všechny zákazníky a balíčku. Pro získání znaménka přesnou adresu URL, musíte se přihlásit do prohlížeče InstaVR instance a webových balení.

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://console.instavr.co/auth/saml/sp/<WEBPackagedURL>`

    > [!NOTE]
    > Hodnota identifikátoru není skutečný. Aktualizujte tuto hodnotu Skutečná hodnota identifikátor, který je vysvětlen později v tomto kurzu.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a **soubor metadat federace** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadata-certificatebase64.png)

6. Na **nastavení prohlížeče InstaVR** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-instavr-viewer-single-sign-on"></a>Nakonfigurujte prohlížeč InstaVR jednotného přihlašování

1. Otevřete nové okno webového prohlížeče a přihlaste na webu společnosti InstaVR prohlížeč jako správce.

2. Klikněte na **ikonu uživatele** a vyberte **účet**.

    ![Konfigurace prohlížeče InstaVR ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-account.png)

3. Přejděte dolů k položce **ověřování SAML** a proveďte následující kroky:

    ![Konfigurace prohlížeče InstaVR ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-configure.png)

    a. V **adresu URL jednotného přihlašování** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    b. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    c. V **Entity ID** vložit do textového pole **Azure Ad identifikátor** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    d. Pokud chcete uložit stažený soubor certifikátu, klikněte na tlačítko **aktualizace**.

    e. K odeslání staženého souboru federačních metadat, klikněte na tlačítko **aktualizace**.

    f. Kopírovat **Entity ID** hodnotu a vložte do **identifikátor (Entity ID)** textového pole na **základní konfiguraci SAML** části webu Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do InstaVR prohlížeče.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **InstaVR prohlížeč**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **InstaVR prohlížeč**.

    ![Odkaz InstaVR prohlížeče v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-instavr-viewer-test-user"></a>Vytvoření InstaVR prohlížeč testovacího uživatele

V této části se v prohlížeči InstaVR vytvoří uživateli Britta Simon. InstaVR prohlížeč podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatele v prohlížeči InstaVR ještě neexistuje, vytvoří se nový po ověření. Pokud budete mít jakékoli problémy, obraťte se prosím na [tým podpory InstaVR prohlížeč](mailto:contact@instavr.co).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

1. Otevřete nové okno webového prohlížeče a přihlaste na webu společnosti InstaVR prohlížeč jako správce.

2. Vyberte **balíčku** v levém navigačním panelu a vyberte **vytvořit balíček pro Web**.

    ![Konfigurace prohlížeče InstaVR ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing1.png)

3. Vyberte **Download** (Stáhnout).

    ![Konfigurace prohlížeče InstaVR ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing2.png)

4. Vyberte **otevřít stránku hostované** po, který ho budete přesměrováni do služby Azure AD pro přihlášení.

    ![Konfigurace prohlížeče InstaVR ](media/instavr-viewer-tutorial/tutorial-instavr-viewer-testing3.png)

5. Zadejte svoje přihlašovací údaje Azure AD k úspěšnému přihlášení k Azure AD prostřednictvím jednotného přihlašování.

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)