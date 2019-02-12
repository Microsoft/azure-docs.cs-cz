---
title: 'Kurz: Integrace Azure Active Directory s dmarcian | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.openlocfilehash: 810214ee751de575b0f27e10f2cb93fa99ad7883
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993941"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Kurz: Integrace Azure Active Directory s dmarcian

V tomto kurzu se dozvíte, jak integrovat dmarcian s Azure Active Directory (Azure AD).
Dmarcian integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k dmarcian.
* Můžete povolit uživatelům být automaticky přihlášeni k dmarcian (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s dmarcian, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* dmarcian jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* podporuje dmarcian **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-dmarcian-from-the-gallery"></a>Přidání dmarcian z Galerie

Konfigurace integrace dmarcian do služby Azure AD, budete muset přidat dmarcian z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat dmarcian z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **dmarcian**vyberte **dmarcian** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![dmarcian v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí dmarcian podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v dmarcian.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s dmarcian, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat Single Sign-On dmarcian](#configure-dmarcian-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele dmarcian](#create-dmarcian-test-user)**  – Pokud chcete mít protějšek Britta Simon v dmarcian, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s dmarcian, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **dmarcian** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![dmarcian domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![dmarcian domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, která je vysvětlen později v tomto kurzu. 

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko Kopírovat zkopírujte **adresa Url federačních metadat aplikace** a uložte ji na vaše počítač.

    ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Konfigurace dmarcian jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k dmarcian jako správce zabezpečení.

2. Klikněte na **profilu** v pravém horním rohu a přejít na **Předvolby**.

    ![Předvolby ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Posuňte se dolů a klikněte na **Single Sign-On** části a potom klikněte na **konfigurovat**.

    ![Jedné ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Na **SAML jednotného přihlašování** stránce nastavení **stav** jako **povoleno** a proveďte následující kroky:

    ![Ověřování ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * V části **přidat dmarcian ke zprostředkovateli Identity** klikněte na tlačítko **kopírování** zkopírovat **adresa URL služby Assertion příjemce** pro vaši instanci a vložte ji  **Adresa URL pro odpověď** textového pole v **části základní konfiguraci SAML** na portálu Azure portal.

    * V části **přidat dmarcian ke zprostředkovateli Identity** klikněte na tlačítko **kopírování** zkopírovat **Entity ID** pro vaši instanci a vložte ji **identifikátor**textového pole v **části základní konfiguraci SAML** na portálu Azure portal.

    * V části **nastavení ověřování** sekci **metadat zprostředkovatele Identity** vložení textového pole **adresa Url federačních metadat aplikace**, který jste zkopírovali z portálu Azure portal.

    * V části **nastavení ověřování** sekci **příkazy atributů** textového pole vložte adresu url `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * V části **nastavit adresu URL pro přihlášení** tématu, zkopírujte **přihlašovací adresa URL** pro vaši instanci a vložte ji **přihlašovací adresa URL** textového pole v **základní SAML konfigurační oddíl** na portálu Azure portal.

        > [!Note]
        > Můžete upravit **přihlašovací adresa URL** podle vaší organizace.

    * Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k dmarcian.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **dmarcian**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **dmarcian**.

    ![Odkaz dmarcian v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-dmarcian-test-user"></a>Vytvoření dmarcian testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k dmarcian, musí být poskytnuty do dmarcian. Zřizování v dmarcian, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k dmarcian jako správce zabezpečení.

2. Klikněte na **profilu** v horním pravém rohu a přejít na **spravovat uživatele**.

    ![Uživatel ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. Na pravé straně **jednotné přihlašování uživatelů** části, klikněte na **Add New User**.

    ![Přidat uživatele ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Na **Add New User** automaticky otevírané okno, proveďte následující kroky:

    ![Nový uživatel ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. V **novou e-mailu uživatele** textového pole zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    b. Pokud chcete udělit práva správce uživateli, vyberte **vytvořit uživatele správcem**.

    c. Klikněte na tlačítko **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici dmarcian na přístupovém panelu, můžete by měl být automaticky přihlášeni k dmarcian, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

