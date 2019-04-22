---
title: 'Kurz: Integrace Azure Active Directory s Vidyard | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Vidyard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bed7df23-6e13-4e7c-b4cc-53ed4804664d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 94e5893caa4dc19702f45e9b42727aab4f884bcd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59273851"
---
# <a name="tutorial-azure-active-directory-integration-with-vidyard"></a>Kurz: Integrace Azure Active Directory s Vidyard

V tomto kurzu se dozvíte, jak integrovat Vidyard s Azure Active Directory (Azure AD).
Vidyard integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Vidyard.
* Můžete povolit uživatelům být automaticky přihlášeni k Vidyard (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Vidyard, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Vidyard jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Vidyard **SP** a **IDP** jednotné přihlašování zahájené pomocí

* Podporuje Vidyard **JIT** zřizování uživatelů

## <a name="adding-vidyard-from-the-gallery"></a>Přidání Vidyard z Galerie

Konfigurace integrace Vidyard do služby Azure AD, budete muset přidat Vidyard z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Vidyard z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Vidyard**vyberte **Vidyard** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Vidyard v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Vidyard podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Vidyard.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Vidyard, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Vidyard Single Sign-On](#configure-vidyard-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Vidyard](#create-vidyard-test-user)**  – Pokud chcete mít protějšek Britta Simon Vidyard, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Vidyard, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Vidyard** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Vidyard domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://secure.vidyard.com/sso/saml/<unique id>/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://secure.vidyard.com/sso/saml/<unique id>/consume`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Vidyard domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://secure.vidyard.com/sso/saml/<unique id>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizuje o skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL, který je vysvětlen později v tomto kurzu. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení Vidyard** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-vidyard-single-sign-on"></a>Konfigurace Vidyard jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Vidyard softwaru jako správce.

2. Na řídicím panelu Vidyard vyberte **skupiny** > **zabezpečení**

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure1.png)

3. Klikněte na tlačítko **nový profil** kartu.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure2.png)

4. V **konfigurace SAML** části, proveďte následující kroky:

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure3.png)

    a. Zadejte název profilu Obecné v **název profilu** textového pole.

    b. Kopírování **jednotného přihlašování uživatele přihlašovací stránku** hodnotu a vložte ho do **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    c. Kopírování **ACS URL** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    d. Kopírování **URL vystavitele nebo Metadata** hodnotu a vložte ho do **identifikátor** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    e. Otevřete soubor stažený certifikát z webu Azure portal v programu Poznámkový blok a vložte jej do **certifikát X.509** textového pole.

    f. V **adresu URL koncového bodu SAML** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanými z webu Azure portal.

    g. Klikněte na **Confirm** (Potvrdit).

5. Na kartě Single Sign On, vyberte **přiřadit** vedle existujícího profilu

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure4.png)

    > [!NOTE]
    > Jakmile vytvoříte profil jednotného přihlašování, ji přiřadíte do žádné skupiny, pro kterou budou uživatelé vyžadují přístup prostřednictvím Azure. Pokud uživatel neexistuje v rámci skupiny, ke kterému byla přiřazena, Vidyard automaticky vytvořit účet uživatele a přiřadit jejich role v reálném čase.

6. Vyberte skupiny organizace, který se zobrazí v **skupiny k dispozici k přiřazení**.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure5.png)

7. Přiřazené skupiny v části můžete zobrazit **aktuálně přiřazené skupiny**. Vyberte roli pro skupinu podle vaší organizace a klikněte na **potvrdit**.

    ![Konfigurace Vidyard](./media/vidyard-tutorial/configure6.png)

    > [!NOTE]
    > Další informace najdete v tématu [tohoto dokumentu](https://knowledge.vidyard.com/saml-single-sign-on-authentication/saml-based-single-sign-on-sso-in-vidyard).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Vidyard použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Vidyard**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Vidyard**.

    ![Odkaz Vidyard v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-vidyard-test-user"></a>Vytvoření Vidyard testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Vidyard. Vidyard podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Vidyard, vytvoří se nový po ověření.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Vidyard](mailto:support@vidyard.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Vidyard na přístupovém panelu, můžete by měl být automaticky přihlášeni k Vidyard, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

