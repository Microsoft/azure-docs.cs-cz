---
title: 'Kurz: Integrace Azure Active Directory s Bonusly | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f605398896c93d15b7475c05c79361fdcefcfcab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442775"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Kurz: Integrace Azure Active Directory s Bonusly

V tomto kurzu se dozvíte, jak integrovat Bonusly s Azure Active Directory (Azure AD).
Bonusly integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Bonusly.
* Můžete povolit uživatelům být automaticky přihlášeni k Bonusly (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Bonusly, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Bonusly jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Bonusly podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-bonusly-from-the-gallery"></a>Přidání Bonusly z Galerie

Konfigurace integrace Bonusly do služby Azure AD, budete muset přidat Bonusly z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Bonusly z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)** , v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Bonusly**vyberte **Bonusly** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Bonusly v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Bonusly podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Bonusly.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Bonusly, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Bonusly Single Sign-On](#configure-bonusly-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Bonusly testovacího uživatele](#create-bonusly-test-user)**  – Pokud chcete mít protějšek Britta Simon Bonusly, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Bonusly, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Bonusly** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Bonusly domény a adresy URL jednotného přihlašování – informace](common/idp-reply.png)

    V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Hodnota není skutečný. Skutečná adresa URL odpovědi zaktualizujte příslušnou hodnotu. Kontakt [tým podpory Bonusly klienta](https://bonus.ly/contact) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. V **podpisový certifikát SAML** klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogového okna.

    ![Upravit podpisového certifikátu SAML](common/edit-certificate.png)

6. V **podpisový certifikát SAML** tématu, zkopírujte **kryptografický OTISK** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. Na **nastavení Bonusly** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-bonusly-single-sign-on"></a>Konfigurace Bonusly jednotného přihlašování

1. V jiném okně prohlížeče, přihlaste se k vaší **Bonusly** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení** a pak vyberte **integrace a aplikace**.

    ![Bonusly sociální části](./media/bonus-tutorial/ic773686.png "Bonusly")
1. V části **Single Sign-On**vyberte **SAML**.

1. Na **SAML** dialogového okna stránky, proveďte následující kroky:

    ![Bonusly Saml dialogového okna stránky](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. V **cílová adresa URL zprostředkovatele identity SSO** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. V **Vystavitel zprostředkovatele identity** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.
    
    d. Vložit **kryptografický otisk** hodnota zkopírována z webu Azure portal do **otisků prstů certifikátů** textového pole.
    
    e. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Bonusly použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Bonusly**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Bonusly**.

    ![Odkaz Bonusly v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-bonusly-test-user"></a>Vytvoření Bonusly testovacího uživatele

Chcete-li povolit Azure AD uživatelům umožní přihlásit k Bonusly, musí být poskytnuty do Bonusly. V případě Bonusly zřizování se ruční úlohy.

> [!NOTE]
> Můžete použít jakékoli další Bonusly uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Bonusly uživatelským účtům, zřídit AAD. 

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. V okně webového prohlížeče Přihlaste se k tenantovi Bonusly.

1. Klikněte na tlačítko **nastavení**.

    ![Nastavení](./media/bonus-tutorial/ic781041.png "nastavení")

1. Klikněte na tlačítko **uživatelů a bonusy** kartu.

    ![Uživatelé a bonusy](./media/bonus-tutorial/ic781042.png "uživatelů a bonusy")

1. Klikněte na tlačítko **spravovat uživatele**.

    ![Správa uživatelů](./media/bonus-tutorial/ic781043.png "Správa uživatelů")

1. Klikněte na tlačítko **přidat uživatele**.

    ![Přidání uživatele](./media/bonus-tutorial/ic781044.png "přidat uživatele")

1. Na **přidat uživatele** dialogového okna, proveďte následující kroky:

    ![Přidání uživatele](./media/bonus-tutorial/ic781045.png "přidat uživatele")  

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

    c. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je `brittasimon\@contoso.com`.

    d. Klikněte na **Uložit**.

    > [!NOTE]
    > Držitel účtu Azure AD obdrží e-mailu, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní.  

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Bonusly na přístupovém panelu, které by měl být automaticky přihlášeni k Bonusly, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
