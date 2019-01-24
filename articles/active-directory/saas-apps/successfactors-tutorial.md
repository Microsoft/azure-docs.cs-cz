---
title: 'Kurz: Integrace Azure Active Directory s SuccessFactors | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SuccessFactors.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.openlocfilehash: 465bdf3aaa8ab6fecddef6784bf1adead7f0c585
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822208"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Kurz: Integrace Azure Active Directory s SuccessFactors

V tomto kurzu se dozvíte, jak integrovat SuccessFactors s Azure Active Directory (Azure AD).
SuccessFactors integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SuccessFactors.
* Můžete povolit uživatelům být automaticky přihlášeni k SuccessFactors (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SuccessFactors, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* SuccessFactors jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje SuccessFactors **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-successfactors-from-the-gallery"></a>Přidání SuccessFactors z Galerie

Konfigurace integrace SuccessFactors do služby Azure AD, budete muset přidat SuccessFactors z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SuccessFactors z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SuccessFactors**vyberte **SuccessFactors** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![SuccessFactors v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí SuccessFactors podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v SuccessFactors.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SuccessFactors, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace SuccessFactors Single Sign-On](#configure-successfactors-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele SuccessFactors](#create-successfactors-test-user)**  – Pokud chcete mít protějšek Britta Simon SuccessFactors, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s SuccessFactors, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **SuccessFactors** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![SuccessFactors domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory SuccessFactors klienta](https://www.successfactors.com/content/ssf-site/en/support.html) k získání těchto hodnot.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení SuccessFactors** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-successfactors-single-sign-on"></a>Konfigurace SuccessFactors jednotné přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu SuccessFactors** jako správce.

2. Navštivte **zabezpečení aplikací** a nativní pro **jednotné přihlašování na funkci**.

3. Umístit libovolné hodnotě v **obnovit Token** a klikněte na tlačítko **uložit Token** pro povolení jednotného přihlašování SAML.

    ![Konfigurace jednotného přihlašování na straně aplikace][11]

    > [!NOTE]
    > Tato hodnota se používá jako přepínač zapnuto/vypnuto. Pokud je libovolná hodnota uložen, jednotné přihlašování SAML nastavená na ON. Pokud je uložen prázdnou hodnotu jednotného přihlašování SAML je vypnuto.

4. Nativní pro následující snímek obrazovky a proveďte následující akce:

    ![Konfigurace jednotného přihlašování na straně aplikace][12]
  
    a. Vyberte **SAML v2 SSO** přepínač
  
    b. Nastavte **název uplatnění strany SAML**(například SAML vystavitele + název společnosti).

    c. V **URL vystavitele** vložit do textového pole **Azure AD identifikátor** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Vyberte **kontrolní výraz** jako **vyžadují povinné podpis**.

    e. Vyberte **povolené** jako **povolit příznak SAML**.

    f. Vyberte **ne** jako **podpisu požadavku přihlášení (SF generované/SP/RP)**.

    g. Vyberte **profilu prohlížeče nebo Pozálohovacího** jako **profilu SAML**.

    h. Vyberte **ne** jako **vynutit období platný certifikát**.

    i. Zkopírujte obsah souboru staženého certifikátu z webu Azure portal a vložte jej do **ověření certifikátu SAML** textového pole.

    > [!NOTE] 
    > Obsah certifikátu musí začínat certifikátu a koncovou značkou certifikátu.

5. Přejděte na SAML V2 a pak proveďte následující kroky:

    ![Konfigurace jednotného přihlašování na straně aplikace][13]

    a. Vyberte **Ano** jako **podporují iniciovaného Zprostředkovatelem přihlašování globální odhlášení**.

    b. V **globální odhlašovací adresa URL služby (LogoutRequest cíl)** vložit do textového pole **odhlašování URL** hodnotu, která jste zkopírovali formuláře na webu Azure portal.

    c. Vyberte **ne** jako **vyžadují sp musíte používat šifrování všech element NameID**.

    d. Vyberte **neurčené** jako **formátem**.

    e. Vyberte **Ano** jako **povolit sp iniciované přihlášení (AuthnRequest)**.

    f. V **požadavků na odeslání jako vystavitel pořádaného microsoftem** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

6. Tyto kroky provést, pokud chcete, aby uživatelská jména přihlášení malá a velká písmena.

    ![Konfigurace jednotného přihlašování][29]

    a. Navštivte **nastavení společnosti**(u dolního).

    b. Zaškrtněte políčko vedle **povolit uživatelské jméno bez písmen**.

    c. Klikněte na **Uložit**.

    > [!NOTE]
    > Pokud se pokusíte tuto možnost povolte, systém ověří, pokud vytvoří duplicitní název přihlašovacího SAML. Například pokud má zákazník uživatelských jmen uživatel1 tak pro uživatele user1. Trvá daleko rozlišování velikosti písmen díky tyto duplikáty. Systém zobrazí chybovou zprávu a neumožňuje tuto funkci. Zákazník musí změňte jednu z uživatelská jména, proto je napsaný různé.

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

V této části je povolit Britta Simon k udělení přístupu k SuccessFactors použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **SuccessFactors**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **SuccessFactors**.

    ![Odkaz SuccessFactors v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-successfactors-test-user"></a>Vytvoření SuccessFactors testovacího uživatele

Přihlaste se k SuccessFactors Azure AD uživatelům umožnit, musí být poskytnuty do SuccessFactors. V případě SuccessFactors zřizování se ruční úlohy.

Chcete-li získat uživatelé vytvoření ve SuccessFactors, budete muset požádat [tým podpory SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici SuccessFactors na přístupovém panelu, můžete by měl být automaticky přihlášeni k SuccessFactors, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png