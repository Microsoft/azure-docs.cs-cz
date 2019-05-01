---
title: 'Kurz: Integrace Azure Active Directory s RFPIO | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RFPIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 87187076-7b50-4247-814f-f217b052703f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 176e0c80b4b1c70c3f49a35ae04d6233bc080f43
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64687828"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Kurz: Integrace Azure Active Directory s RFPIO

V tomto kurzu se dozvíte, jak integrovat RFPIO s Azure Active Directory (Azure AD).
RFPIO integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k RFPIO.
* Můžete povolit uživatelům být automaticky přihlášeni k RFPIO (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s RFPIO, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* RFPIO jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje RFPIO **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-rfpio-from-the-gallery"></a>Přidání RFPIO z Galerie

Konfigurace integrace RFPIO do služby Azure AD, budete muset přidat RFPIO z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat RFPIO z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **RFPIO**vyberte **RFPIO** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![RFPIO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí RFPIO podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v RFPIO.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s RFPIO, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace RFPIO Single Sign-On](#configure-rfpio-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele RFPIO](#create-rfpio-test-user)**  – Pokud chcete mít protějšek Britta Simon RFPIO, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s RFPIO, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **RFPIO** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    ![RFPIO domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.rfpio.com`

    b. Klikněte na tlačítko **nastavit další adresy URL**.

    c. V **stav přenosu** textového pole zadejte hodnotu řetězce. Kontakt [tým podpory RFPIO](https://www.rfpio.com/contact/) tuto výhodu získáte.

    ![RFPIO domény a adresy URL jednotného přihlašování – informace](common/idp-preintegrated-relay.png)

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/both-preintegrated-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.app.rfpio.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor a přihlašování. Kontakt [tým podpory RFPIO klienta](https://www.rfpio.com/contact/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavení RFPIO** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-rfpio-single-sign-on"></a>Konfigurace RFPIO jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k **RFPIO** webu jako správce.

1. Klikněte na rozevírací seznam dolním levém rohu.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app1.png)

1. Klikněte na **nastavení organizace**. 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app2.png)

1. Klikněte na **funkce a integrace**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app4.png)

1. V **Konfigurace jednotného přihlašování SAML** klikněte na tlačítko **upravit**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app3.png)

1. V této části provedete následující akce:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app5.png)
    
    a. Zkopírujte obsah **stáhnout soubor XML s metadaty** a vložte ho do **konfigurace identity** pole.

    > [!NOTE]
    >Zkopírujte obsah stáhnout **kód XML metadat federace** použití **Poznámkový blok ++** nebo správné **editoru XML**.

    b. Klikněte na tlačítko **ověření**.

    c. Po kliknutí na tlačítko **ověření**, Převrátit **SAML(Enabled)** zapnete.

    d. Klikněte na **Submit** (Odeslat).

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

V této části je povolit Britta Simon k udělení přístupu k RFPIO použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **RFPIO**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **RFPIO**.

    ![Odkaz RFPIO v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-rfpio-test-user"></a>Vytvoření RFPIO testovacího uživatele

1. Přihlaste se k webu společnosti RFPIO jako správce.

1. Klikněte na rozevírací seznam dolním levém rohu.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app1.png)

1. Klikněte na **nastavení organizace**. 

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app2.png)

1. Klikněte na tlačítko **členové týmu**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app6.png)

1. Klikněte na **přidat členy**.

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app7.png)

1. V **přidat nové členy** oddílu. Proveďte následující akce:

    ![Konfigurace jednotného přihlašování](./media/rfpio-tutorial/app8.png)

    a. Zadejte **e-mailová adresa** v **zadejte jednu e-mailu na řádek** pole.

    b. Vyberte prosím **Role** podle vašich požadavků.

    c. Klikněte na tlačítko **přidat členy**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici RFPIO na přístupovém panelu, můžete by měl být automaticky přihlášeni k RFPIO, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

