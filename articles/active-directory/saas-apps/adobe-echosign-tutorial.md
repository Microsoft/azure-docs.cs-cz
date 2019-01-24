---
title: 'Kurz: Integrace Azure Active Directory pomocí Adobe Sign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.openlocfilehash: 117cd2a546128499c1f9bbc75be40034875fa76e
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54809359"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Integrace Azure Active Directory pomocí Adobe Sign

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Adobe Sign.
Adobe Sign integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Adobe Sign.
* Uživatelům se automaticky přihlášeni k Adobe Sign (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Adobe Sign, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Adobe Sign jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Adobe Sign **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-adobe-sign-from-the-gallery"></a>Přidání Adobe Sign z Galerie

Pokud chcete nakonfigurovat integraci Adobe Sign do služby Azure AD, budete muset přidat Adobe Sign z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Adobe Sign z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Adobe Sign**vyberte **Adobe Sign** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Adobe Sign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí Adobe Sign podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Adobe Sign.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Adobe Sign, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Adobe Sign Single Sign-On](#configure-adobe-sign-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Adobe Sign](#create-adobe-sign-test-user)**  – Pokud chcete mít protějšek Britta Simon v Adobe Sign, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí Adobe Sign, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Adobe Sign** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Adobe Sign domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.echosign.com/`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Adobe Sign** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-adobe-sign-single-sign-on"></a>Konfigurace Adobe Sign jednotného přihlašování

7. Před konfigurací, obraťte se [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) na seznam povolených vaši doménu v Adobe Sign. Tady je postup pro přidání domény:

    a. [Tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) odešle náhodně vygenerovaný token. Pro vaši doménu token, který bude podobný tomuto: **adobe sign ověřovací = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikování token pro ověření v textový záznam DNS a upozornit [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Může to trvat několik dnů nebo déle. Všimněte si, že zpoždění šíření DNS znamená, že hodnota publikovaný ve službě DNS nemusí být viditelná pro hodinu nebo déle. Váš správce IT by měl být informovanosti o tom, jak publikovat tento token v textový záznam DNS.
    
    c. Když upozornit [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory po publikování token se ověření domény a přidejte ke svému účtu.
    
    d. Obecně platí tady je postup pro publikování token na záznam DNS:

    * Přihlaste se ke svému účtu domény
    * Vyhledejte stránku pro aktualizaci záznamů DNS. Na této stránce může být volána, správu DNS, Správa názvového serveru nebo Upřesnit nastavení.
    * Find the TXT records for your domain.
    * Přidejte záznam TXT úplné token hodnotou poskytnutou Adobe.
    * Uložte provedené změny.

8. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Adobe Sign jako správce.

9. V nabídce SAML, vyberte **nastavení účtu** > **nastavení SAML**.
   
    ![Stránka nastavení snímku obrazovky z Adobe Sign SAML](./media/adobe-echosign-tutorial/ic789520.png "účtu")

10. V **nastavení SAML** části, proveďte následující kroky:
  
    ![Snímek obrazovky nastavení SAML](./media/adobe-echosign-tutorial/ic789521.png "nastavení SAML")
   
    ![Snímek obrazovky nastavení SAML](./media/adobe-echosign-tutorial/ic789522.png "nastavení SAML")

    a. V části **SAML režimu**vyberte **SAML povinné**.
   
    b. Vyberte **umožnit správci Echosign účtu přihlásit pomocí svých přihlašovacích údajů Echosign**.
   
    c. V části **vytvoření uživatele**vyberte **automaticky přidat uživatele ověřeni pomocí SAML**.

    d. Vložit **Azure Ad identifikátor**, který jste zkopírovali z portálu Azure portal do **Idp Entity ID** textového pole.
    
    e. Vložit **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **přihlašovací adresa URL zprostředkovatele identity** textového pole.
   
    f. Vložit **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal do **odhlašovací adresa URL zprostředkovatele identity** textového pole.

    g. Otevřete váš stažené **Certificate(Base64)** soubor v poznámkovém bloku. Zkopírujte obsah ho do schránky a vložte ho do **IdP certifikát** textového pole.

    h. Vyberte **uložit změny**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Adobe Sign.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Adobe Sign**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Adobe Sign**.

    ![Adobe Sign odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-adobe-sign-test-user"></a>Vytvořit testovacího uživatele Adobe Sign

Povolení služby Azure AD uživatelům umožní přihlásit k Adobe Sign, musí být poskytnuty do Adobe Sign. Toto je ruční úloha.

>[!NOTE]
>Ke zřízení uživatelských účtů služby Azure AD můžete použít jiné nástroje pro tvorbu Adobe Sign uživatelského účtu nebo rozhraní API poskytovaných Adobe Sign. 

1. Přihlaste se k vaší **Adobe Sign** společnosti serveru jako správce.

2. V nabídce v horní části vyberte **účet**. V levém podokně vyberte **uživatelů a skupin** > **vytvořte nového uživatele**.
   
    ![Snímek obrazovky Adobe Sign podnikové lokality, pomocí účtu, uživatelé a skupiny a vytvořte nového uživatele zvýrazněný](./media/adobe-echosign-tutorial/ic789524.png "účtu")
   
3. V **vytvořit nového uživatele** části, proveďte následující kroky:
   
    ![Snímek obrazovky vytvoření nového uživatele části](./media/adobe-echosign-tutorial/ic789525.png "vytvořit uživatele")
   
    a. Typ **e-mailovou adresu**, **křestní jméno**, a **příjmení** platný Azure AD účet ke zřízení do související textových polí.
   
    b. Vyberte **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mailu, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní. 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Adobe Sign na přístupovém panelu, můžete by měl být automaticky přihlášeni k Adobe Sign, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

