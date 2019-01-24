---
title: 'Kurz: Integrace Azure Active Directory s Freshservice | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 2621918896131864b6183df420f51bab50f0e474
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811226"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Kurz: Integrace Azure Active Directory s Freshservice

V tomto kurzu se dozvíte, jak integrovat Freshservice s Azure Active Directory (Azure AD).
Freshservice integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Freshservice.
* Můžete povolit uživatelům být automaticky přihlášeni k Freshservice (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Freshservice, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Freshservice jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Freshservice **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-freshservice-from-the-gallery"></a>Přidání Freshservice z Galerie

Konfigurace integrace Freshservice do služby Azure AD, budete muset přidat Freshservice z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Freshservice z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Freshservice**vyberte **Freshservice** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Freshservice v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Freshservice podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Freshservice.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Freshservice, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Freshservice Single Sign-On](#configure-freshservice-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Freshservice](#create-freshservice-test-user)**  – Pokud chcete mít protějšek Britta Simon Freshservice, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Freshservice, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Freshservice** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Freshservice domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<democompany>.freshservice.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Freshservice klienta](https://support.freshservice.com/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. V **podpisový certifikát SAML** klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogového okna.

    ![Upravit podpisového certifikátu SAML](common/edit-certificate.png)

6. V **podpisový certifikát SAML** tématu, zkopírujte **kryptografický otisk** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. Na **nastavení Freshservice** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-freshservice-single-sign-on"></a>Konfigurace Freshservice jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Freshservice jako správce.

2. V nabídce v horní části klikněte na tlačítko **správce**.

    ![Správce](./media/freshservice-tutorial/ic790814.png "správce")

3. V **zákaznického portálu služeb**, klikněte na tlačítko **zabezpečení**.

    ![Zabezpečení](./media/freshservice-tutorial/ic790815.png "zabezpečení")

4. V **zabezpečení** části, proveďte následující kroky:

    ![Jednotné přihlašování](./media/freshservice-tutorial/ic790816.png "jednotné přihlašování")

    a. Přepínač **jednotné přihlašování**.

    b. Vyberte **SAML SSO**.

    c. V **SAML přihlašovací adresa URL** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. V **otisku certifikátu zabezpečení** vložit do textového pole **kryptografický OTISK** hodnota certifikátu, který jste zkopírovali z portálu Azure portal.

    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Freshservice.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Freshservice**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Freshservice**.

    ![Odkaz Freshservice v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-freshservice-test-user"></a>Vytvoření Freshservice testovacího uživatele

Přihlaste se k FreshService Azure AD uživatelům umožnit, musí být poskytnuty do FreshService. V případě FreshService zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **FreshService** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **správce**.

    ![Správce](./media/freshservice-tutorial/ic790814.png "správce")

3. V **Správa uživatelů** klikněte na tlačítko **žadatelů o deklaraci**.

    ![Žadatelů o deklaraci](./media/freshservice-tutorial/ic790818.png "žadatele")

4. Klikněte na tlačítko **nový žadatel**.

    ![Nové žadatelů o deklaraci](./media/freshservice-tutorial/ic790819.png "nové žadatele")

5. V **nový žadatel** části, proveďte následující kroky:

    ![Nový žadatel](./media/freshservice-tutorial/ic790820.png "nový žadatel")  

    a. Zadejte **křestní jméno** a **e-mailu** atributy platný účet služby Azure Active Directory ke zřízení do související textových polí.

    b. Klikněte na **Uložit**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory získá e-mailu včetně odkaz pro potvrzení účtu, pak se změní na aktivní
    >  

> [!NOTE]
> Můžete použít jakékoli jiné FreshService uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných FreshService uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Freshservice na přístupovém panelu, můžete by měl být automaticky přihlášeni k Freshservice, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)