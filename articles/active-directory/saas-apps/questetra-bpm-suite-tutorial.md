---
title: 'Kurz: Integrace Azure Active Directory sadou BPM Questetra | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 655140fc7f8cc52adf6a13a99cef531f28d5cefc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422243"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Kurz: Integrace Azure Active Directory sadou BPM Questetra

V tomto kurzu se dozvíte, jak integrovat Questetra BPM Suite s Azure Active Directory (Azure AD).

Integrace Questetra BPM Suite s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k sadě BPM Questetra
- Uživatele, aby automaticky získat přihlášení k sadě BPM Questetra (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Questetra BPM sady, potřebujete následující položky:

- S předplatným služby Azure AD
- Sady BPM Questetra jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat sadu BPM Questetra z Galerie
1. Konfigurace a otestování služby Azure AD jednotného přihlašování

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Přidat sadu BPM Questetra z Galerie
Pokud chcete nakonfigurovat integraci sady BPM Questetra do služby Azure AD, budete muset přidat Questetra BPM Suite z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidat sadu BPM Questetra z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Questetra BPM Suite**vyberte **Questetra BPM Suite** panel výsledek a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Přidat z Galerie](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí sady Questetra BPM na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Questetra BPM Suite je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v sadě BPM Questetra potřeba navázat.

V sadě BPM Questetra, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Questetra BPM sady, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon BPM sady Questetra, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Questetra BPM Suite.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Questetra BPM Suite, proveďte následující kroky:**

1. Na webu Azure Portal na **Questetra BPM Suite** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Přihlašování založené na SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

1. Na **Questetra BPM sada domény a adresy URL** části, proveďte následující kroky:

    ![Část Questetra BPM sada domény a adresy URL](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Můžete získat tyto hodnoty z **SP informace** části na vaše **Questetra BPM Suite** společnosti webu, který je vysvětlen později v kurzu nebo kontaktujte [podpora Questetra BPM Suite klientů tým](https://www.questetra.com/contact/). 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base 64)** a uložte soubor certifikátu v počítači.

    ![Části podpisový certifikát SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

1. Na **Questetra BPM Suite konfigurace** klikněte na tlačítko **konfiguraci sady BPM Questetra** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Questetra BPM Suite konfiguračního oddílu](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

1. V okně jiné webové prohlížeče, přihlaste se do vaší **Questetra BPM Suite** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **nastavení systému**. 
   
    ![Azure AD jednotné přihlašování][10]

1. Chcete-li otevřít **SingleSignOnSAML** klikněte na **jednotné přihlašování (SAML)**. 
   
    ![Azure AD jednotné přihlašování][11]

1. Na vaše **Questetra BPM Suite** společnosti v lokalitě, **SP informace** části, proveďte následující kroky:

    a. Kopírovat **ACS URL**a vložte jej do **přihlašovací adresa URL** textového pole v **Questetra BPM sada domény a adresy URL** části webu Azure Portal.
    
    b. Kopírovat **Entity ID**a vložte jej do **identifikátor** textového pole v **Questetra BPM sada domény a adresy URL** části webu Azure Portal.

1. Na vaše **Questetra BPM Suite** společnosti serveru, proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování][15]
   
    a. Vyberte **povolit jednotné přihlašování**.
   
    b. V **Entity ID** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal.
    
    c. V **přihlašovací adresa URL stránky** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
    
    d. V **adresy URL odhlašovací stránky** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
    
    e. V **formátem** textové pole, typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Otevřete váš **Base-64** kódovaného certifikátu v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte jej do **ověření certifikátu** textového pole. 

    g. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Vytvoření zkušebního uživatele Questetra BPM Suite

Cílem této části je vytvořte uživatele Britta Simon Questetra BPM Suite.

**Vytvořte uživatele v sadě BPM Questetra volá Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Questetra BPM Suite jako správce.
1. Přejděte na **nastavení systému > seznam uživatelů > Nový uživatel**. 
1. V dialogovém okně Nový uživatel proveďte následující kroky: 
   
    ![Vytvořit testovacího uživatele][300] 
   
    a. V **název** textové pole, typ **název** uživatele **britta.simon@contoso.com**.
   
    b. V **e-mailu** textové pole, typ **e-mailu** uživatele **britta.simon@contoso.com**
   
    c. V **heslo** textového pole zadejte **heslo** uživatele.
    
    d. Klikněte na tlačítko **přidat nového uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k sadě BPM Questetra.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Questetra BPM Suite, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Questetra BPM Suite**.

    ![Sadu BPM Questetra v seznamu aplikací](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Questetra BPM Suite na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Questetra BPM Suite.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

