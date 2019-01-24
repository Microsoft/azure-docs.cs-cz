---
title: 'Kurz: Integrace Azure Active Directory s Asana | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: b687f45221d968b3a75e6466ccb316273b9e0182
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822854"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Kurz: Integrace Azure Active Directory s Asaně

V tomto kurzu se dozvíte, jak integrovat Asana s Azure Active Directory (Azure AD).
Asana integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Asana.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Asana (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Asana, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Asana jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Asana **SP** jednotné přihlašování zahájené pomocí

* Podporuje Asana [ **automatizovaná** zřizování uživatelů](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Přidání Asana z Galerie

Konfigurace integrace Asana do služby Azure AD, budete muset přidat z Galerie Asana pro váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Asana z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Asana**vyberte **Asana** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Asana v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Asana podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Asaně.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Asana, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Asana Single Sign-On](#configure-asana-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Asana](#create-asana-test-user)**  – Pokud chcete mít protějšek Britta Simon v Asaně, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Asana, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Asana** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Asana domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textového pole zadat adresu URL: `https://app.asana.com/`

    b. V **identifikátor (Entity ID)** textového pole zadat adresu URL: `https://app.asana.com/`

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Asana** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-asana-single-sign-on"></a>Konfigurace Asana jednotné přihlašování

1. V jiném okně prohlížeče přihlašování k aplikaci Asana. Pokud chcete nakonfigurovat jednotné přihlašování v Asaně, přístup k nastavení pracovního prostoru klikněte na název pracovního prostoru v pravém horním rohu obrazovky. Potom klikněte na  **\<názvu pracovního prostoru\> nastavení**.

    ![Nastavení jednotného přihlašování v Asaně](./media/asana-tutorial/tutorial_asana_09.png)

2. Na **nastavení organizace** okna, klikněte na tlačítko **správu**. Potom klikněte na **členy se musí přihlásit prostřednictvím SAML** umožňující konfiguraci jednotného přihlašování. Proveďte následující kroky:

    ![Konfigurace nastavení organizace jednotné přihlašování](./media/asana-tutorial/tutorial_asana_10.png)  

    a. V **přihlašovací adresa URL stránky** vložit do textového pole **přihlašovací adresa URL**.

    b. Klikněte pravým tlačítkem na certifikát stažený z webu Azure portal a pak otevřete soubor certifikátu pomocí poznámkového bloku nebo upřednostňovaném textovém editoru. Obsah mezi zahájit a název certifikátu koncovým zkopírujte a vložte ji **certifikát X.509** textového pole.

3. Klikněte na **Uložit**. Přejděte na [Asana Průvodce pro nastavení jednotné přihlašování](https://asana.com/guide/help/premium/authentication#gl-saml) Pokud potřebujete další pomoc.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Asana.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Asana**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Asana**.

    ![Odkaz Asana v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-asana-test-user"></a>Vytvoření Asana testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Asaně. Asana podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](asana-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

V této části vytvořte uživatele Britta Simon v Asaně.

1. Na **Asana**, přejděte **týmy** části na levém panelu. Klikněte na tlačítko plus.

    ![Vytváří se testovací uživatele služby Azure AD](./media/asana-tutorial/tutorial_asana_12.png)

2. Zadejte e-mailu uživatele, jako je **britta.simon@contoso.com** v textovém poli a pak vyberte **pozvat**.

3. Klikněte na tlačítko **odeslat pozvánku**. Nový uživatel obdrží e-mailu do svých e-mailový účet. uživatel bude muset vytvořit a ověřit účet.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Asana na přístupovém panelu, můžete by měl být automaticky přihlášeni k Asana, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurace zřizování uživatelů](asana-provisioning-tutorial.md)