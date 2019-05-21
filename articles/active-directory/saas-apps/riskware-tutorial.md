---
title: 'Kurz: Integrace Azure Active Directory s Riskware | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 08db463c92e4ee34819f446c3827ed8dfddc02d7
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903930"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Kurz: Integrace Azure Active Directory s Riskware

V tomto kurzu se dozvíte, jak integrovat Riskware s Azure Active Directory (Azure AD).
Riskware integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Riskware.
* Můžete povolit uživatelům být automaticky přihlášeni k Riskware (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Riskware, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Riskware jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Riskware **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-riskware-from-the-gallery"></a>Přidání Riskware z Galerie

Konfigurace integrace Riskware do služby Azure AD, budete muset přidat Riskware z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Riskware z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Riskware**vyberte **Riskware** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Riskware v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Riskware podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Riskware.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Riskware, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Riskware Single Sign-On](#configure-riskware-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Riskware](#create-riskware-test-user)**  – Pokud chcete mít protějšek Britta Simon Riskware, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Riskware, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Riskware** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Riskware domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PRODUKČNÍ| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL:
    
    | Prostředí| Vzor adresy URL|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PRODUKČNÍ| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Přihlašovací adresa URL není skutečný. Aktualizujte příslušnou hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory Riskware klienta](mailto:support@pansoftware.com.au) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Riskware** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-riskware-single-sign-on"></a>Konfigurace Riskware jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Riskware jako správce.

1. V pravém horním rohu, klepněte na příkaz **údržby** otevřete stránku údržby.

    ![Udržovat Riskware konfigurace](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stránce Údržba, klikněte na **ověřování**.

    ![Konfigurace Riskware authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. V **konfigurace ověřování** stránce, proveďte následující kroky:

    ![Konfigurace Riskware authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Vyberte **typ** jako **SAML** pro ověřování.

    b. V **kód** textového pole zadejte kód jako AZURE_UAT.

    c. V **popis** textového pole zadejte váš popis, např. konfigurace AZURE pro jednotné přihlašování.

    d. V **jediné přihlášení na stránce** vložit do textového pole **přihlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    e. V **odhlásit stránky** vložit do textového pole **odhlašovací adresa URL** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    f. V **pole formuláře Post** textového pole zadejte název pole v odpověď na příspěvek, který obsahuje SAML jako SAMLResponse

    g. V **názvu značky XML Identity** textové pole, typ atributu, který obsahuje jedinečný identifikátor v odpověď SAML NameID.

    h. Otevřete na stažený **soubor Xml s metadaty** z webu Azure portal v programu Poznámkový blok, zkopírujte certifikát ze souboru metadat a vložte ho do **certifikát** textové pole

    i. V **příjemce URL** textového pole vložte hodnotu **adresy URL odpovědi**, které můžete získat na tým podpory.

    j. V **vystavitele** textového pole vložte hodnotu **identifikátor**, které můžete získat na tým podpory.

    > [!Note]
    > Kontakt [tým podpory Riskware klienta](mailto:support@pansoftware.com.au) k získání těchto hodnot

    k. Vyberte **po použití** zaškrtávací políčko.

    l. Vyberte **požadavek SAML, použijte** zaškrtávací políčko.

    m. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Riskware použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Riskware**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Riskware**.

    ![Odkaz Riskware v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-riskware-test-user"></a>Vytvoření Riskware testovacího uživatele

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Riskware, musí být poskytnuty do Riskware. Zřizování v Riskware, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Riskware jako správce zabezpečení.

1. V pravém horním rohu, klepněte na příkaz **údržby** otevřete stránku údržby. 

    ![Udržuje Riskware konfigurace](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Na stránce Údržba, klikněte na **lidé**.

    ![Konfigurace Riskware osoby](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Vyberte **podrobnosti** kartě a proveďte následující kroky:

    ![Podrobnosti o konfiguraci Riskware](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Vyberte **typu Person** zaměstnance, jako je.

    b. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

1. Na **zabezpečení** kartu, proveďte následující kroky:

    ![Riskware konfigurace zabezpečení](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. V části **ověřování** vyberte **ověřování** režimu, který jste nastavili, jako je AZURE konfiguraci pro jednotné přihlašování.

    b. V části **přihlašovací údaje** sekci **ID uživatele** textového pole zadejte e-mailu uživatele, jako je `brittasimon@contoso.com`.

    c. V **heslo** textového pole zadejte heslo uživatele.

1. Na **organizace** kartu, proveďte následující kroky:

    ![Konfigurace Riskware organizace](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Vyberte možnost jako **Level1** organizace.

    b. V části **primární síti na pracovišti uživatele** sekci **umístění** textového pole zadejte vaši polohu.

    c. V části **zaměstnance** vyberte **stavu zaměstnance** neformální, jako je.

    d. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Riskware na přístupovém panelu, můžete by měl být automaticky přihlášeni k Riskware, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
