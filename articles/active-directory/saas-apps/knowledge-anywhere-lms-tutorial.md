---
title: 'Kurz: Integrace Azure Active Directory s LMS znalostní báze kdekoli | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LMS znalostní báze kdekoli.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: 0ac3522d4182ef7cbf35eb8505dede2aff233937
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56302659"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Kurz: Integrace Azure Active Directory s LMS kdekoli znalostní báze

V tomto kurzu se dozvíte, jak integrovat LMS znalostní báze odkudkoli pomocí služby Azure Active Directory (Azure AD).
Integrace LMS kdekoli znalostní báze s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LMS znalostní báze kdekoli.
* Uživatelům se automaticky přihlášeni do znalostní báze kdekoli LMS (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LMS znalostní báze kdekoli, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* LMS kdekoli znalostní báze jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje LMS znalostní báze kdekoli **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Podporuje LMS znalostní báze kdekoli **JIT** zřizování uživatelů

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Přidání znalostní báze kdekoli LMS z Galerie

Ke konfiguraci integrace LMS znalostní báze kamkoli do služby Azure AD, budete muset přidat LMS znalostní báze kdekoli v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LMS znalostní báze odkudkoli z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LMS znalostní báze kdekoli**vyberte **LMS znalostní báze kdekoli** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Znalostní báze kdekoli LMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí znalostní báze kdekoli LMS podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v LMS znalostní báze kdekoli.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LMS kdekoli znalostní báze, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace znalostní báze kdekoli LMS Single Sign-On](#configure-knowledge-anywhere-lms-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele LMS znalostní báze kdekoli](#create-knowledge-anywhere-lms-test-user)**  – Pokud chcete mít protějšek Britta Simon ve znalostní bázi kdekoli LMS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s LMS znalostní báze kdekoli, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **LMS znalostní báze kdekoli** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Domény LMS v znalostní báze odkudkoli a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi, který je vysvětlen později v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Domény LMS v znalostní báze odkudkoli a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory klienta LMS v znalostní báze kdekoli](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) tuto výhodu získáte. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení LMS znalostní báze kdekoli** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Konfigurace znalostní báze kdekoli LMS jednotného přihlašování

1. V jiném okně prohlížeče otevřete portál pro správu LMS znalostní báze kdekoli.

2. Vyberte na **lokality** kartu.

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Vyberte na **nastavení SAML** kartu.

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Klikněte na **přidat nový**.

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. Na **přidat nebo aktualizovat nastavení SAML** stránce, proveďte následující kroky:

    ![Znalostní báze kdekoli LMS konfigurace](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Zadejte název zprostředkovatele identity podle vaší organizace. Pro příklad:- `Azure`.

    b. V **IDP Entity ID** vložit do textového pole **Azure Ad identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **adresa URL zprostředkovatele identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Do poznámkového bloku otevřete soubor stažený certifikát z portálu Azure portal, zkopírujte obsah certifikát a vložte ho do **certifikát** textového pole.

    e. V **odhlašovací adresa URL** vložit do textového pole **odhlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    f. Vyberte **hlavní lokalitu** z rozevíracího seznamu **domény**.

    g. Kopírovat **SP Entity ID** hodnotu a vložte ho do **identifikátor** textové pole **základní konfiguraci SAML** části webu Azure Portal.

    h. Kopírovat **SP Response(ACS) URL** hodnotu a vložte ho do **adresy URL odpovědi** textové pole **základní konfiguraci SAML** části webu Azure Portal.

    i. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu LMS znalostní báze kdekoli.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **LMS znalostní báze kdekoli**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LMS znalostní báze kdekoli**.

    ![Odkaz LMS znalostní báze kdekoli v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Vytvořit testovacího uživatele LMS kdekoli znalostní báze

V této části se vytvoří uživateli Britta Simon v LMS znalostní báze kdekoli. LMS kdekoli znalostní báze podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi LMS znalostní báze kdekoli, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici LMS znalostní báze kamkoli na přístupovém panelu, vám by měl být automaticky přihlášeni do znalostní báze kdekoli LMS pro kterou můžete nastavit jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)