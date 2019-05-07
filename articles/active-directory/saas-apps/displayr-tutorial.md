---
title: 'Kurz: Integrace Azure Active Directory s Displayr | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fdb4cc26a435ba161532b324ae5a04fed8eb9437
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158403"
---
# <a name="tutorial-azure-active-directory-integration-with-displayr"></a>Kurz: Integrace Azure Active Directory s Displayr

V tomto kurzu se dozvíte, jak integrovat Displayr s Azure Active Directory (Azure AD).
Displayr integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Displayr.
* Můžete povolit uživatelům být automaticky přihlášeni k Displayr (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Displayr, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Displayr jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Displayr **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-displayr-from-the-gallery"></a>Přidání Displayr z Galerie

Konfigurace integrace Displayr do služby Azure AD, budete muset přidat Displayr z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Displayr z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Displayr**vyberte **Displayr** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Displayr v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Displayr podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Displayr.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Displayr, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Displayr Single Sign-On](#configure-displayr-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Displayr](#create-displayr-test-user)**  – Pokud chcete mít protějšek Britta Simon Displayr, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Displayr, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Displayr** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavení jednotného přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, postupujte následovně:

    ![Displayr domény a adresy URL jednotného přihlašování – informace](common/sp-intiated.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL:  `https://app.displayr.com/Login`

5. V **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Displayr aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

7. Kromě toho výše Displayr aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **atributy uživatele a deklarace identity** části na **deklarace skupiny (Preview)** dialogového okna, proveďte následující kroky:

    a. Klikněte na tlačítko **pera** vedle **skupiny vrácené v deklaraci identity**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Vyberte **všechny skupiny** ze seznamu přepínač.

    c. Vyberte **zdrojového atributu** z **ID skupiny**.

    d. Zkontrolujte **přizpůsobit název deklarace skupiny**.

    e. Zkontrolujte **generování skupin jako deklarací identity rolí**.

    f. Klikněte na **Uložit**.

8. Na **nastavení Displayr** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-displayr-single-sign-on"></a>Konfigurace Displayr jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k Displayr jako správce.

2. Klikněte na **nastavení** přejděte na **účet**.

    ![Konfigurace](./media/displayr-tutorial/config01.png)

3. Přepnout na **nastavení** v horní nabídce a přejděte dolů na stránce pro kliknutí na **nakonfigurovat jednotné přihlašování na (SAML)**.

    ![Konfigurace](./media/displayr-tutorial/config02.png)

4. Na **jednotné přihlašování na (SAML)** stránce, proveďte následující kroky:

    ![Konfigurace](./media/displayr-tutorial/config03.png)

    a. Zkontrolujte, **povolit jednotné přihlašování na (SAML)** pole.

    b. V **vystavitele** text vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    c. V **přihlašovací adresa URL** text vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL** textové pole, vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. V poznámkovém bloku otevřete certifikát (Raw), zkopírujte jeho obsah a vložte ho do **certifikát** textového pole.

    f. **Seskupit mapování** jsou volitelné.

    g. Klikněte na **Uložit**.  

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Displayr použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Displayr**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Displayr**.

    ![Odkaz Displayr v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

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

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Displayr na přístupovém panelu, můžete by měl být automaticky přihlášeni k Displayr, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

