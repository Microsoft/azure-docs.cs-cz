---
title: 'Kurz: Integrace Azure Active Directory s 123ContactForm | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 450fc8df69eb5ffb99cb6548e80cd4d3628e7419
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819437"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Kurz: Integrace Azure Active Directory s 123ContactForm

V tomto kurzu se dozvíte, jak integrovat 123ContactForm s Azure Active Directory (Azure AD).

123ContactForm integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k 123ContactForm
- Můžete povolit uživatelům, aby automaticky získat přihlášení k 123ContactForm (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 123ContactForm, potřebujete následující položky:

- Předplatné Azure AD
- 123ContactForm jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání 123ContactForm z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-123contactform-from-the-gallery"></a>Přidání 123ContactForm z Galerie
Konfigurace integrace 123ContactForm do služby Azure AD, budete muset přidat 123ContactForm z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat 123ContactForm z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **123ContactForm**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/123contactform-tutorial/tutorial_123contactform_search.png)

5. Na panelu výsledků vyberte **123ContactForm**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s 123ContactForm podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v 123ContactForm je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v 123ContactForm potřeba navázat.

V 123ContactForm, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 123ContactForm, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele 123ContactForm](#creating-a-123contactform-test-user)**  – Pokud chcete mít protějšek Britta Simon v 123ContactForm, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci 123ContactForm.

**Ke konfiguraci Azure AD jednotné přihlašování s 123ContactForm, proveďte následující kroky:**

1. Na webu Azure Portal na **123ContactForm** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Na **123ContactForm domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/url1.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Pokud chcete nakonfigurovat aplikace v **SP iniciované režimu**, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/url2.png)

    a. Klikněte na tlačítko **zobrazit pokročilé nastavení URL** možnost

    b. V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Budete muset aktualizovat tyto hodnoty od skutečné adresy URL a identifikátor, který je vysvětlen později v tomto kurzu.
    
5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/tutorial_general_400.png)

7. Ke konfiguraci jednotného přihlašování na **123ContactForm** straně, přejděte na [ https://www.123contactform.com/form-2709121/ ](https://www.123contactform.com/form-2709121/) a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/submit.png) 

    a. V **e-mailu** textového pole zadejte e-mailu uživatele tj **BrittaSimon@Contoso.com**.

    b. Klikněte na tlačítko **nahrát** a vyhledejte soubor metadat XML, který jste si stáhli z webu Azure portal.

    c. Klikněte na tlačítko **odeslání formuláře**.

8. Na **Microsoft Azure AD – jednotné přihlašování – konfigurace nastavení aplikace** proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/url3.png)

    a. Pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu**, kopie **IDENTIFIKÁTOR** hodnotu pro vaši instanci a vložte ji **identifikátor** textového pole v  **123ContactForm domény a adresy URL** části na webu Azure portal.
    
    b. Pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu**, kopie **adresy URL odpovědi** hodnotu pro vaši instanci a vložte ji **adresy URL odpovědi** textového pole v  **123ContactForm domény a adresy URL** části na webu Azure portal.

    c. Pokud chcete nakonfigurovat aplikace v **SP iniciované režimu**, kopie **PŘIHLAŠOVACÍ adresu URL** hodnotu pro vaši instanci a vložte ji **přihlašovací adresa URL** textového pole v  **123ContactForm domény a adresy URL** části na webu Azure portal.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/123contactform-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/123contactform-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/123contactform-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/123contactform-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-123contactform-test-user"></a>Vytvoření zkušebního uživatele 123ContactForm

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k 123ContactForm.

![Přiřadit uživatele][200] 

**Přiřadit 123ContactForm Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **123ContactForm**.

    ![Konfigurace jednotného přihlašování](./media/123contactform-tutorial/tutorial_123contactform_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici 123ContactForm na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci 123ContactForm.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/123contactform-tutorial/tutorial_general_01.png
[2]: ./media/123contactform-tutorial/tutorial_general_02.png
[3]: ./media/123contactform-tutorial/tutorial_general_03.png
[4]: ./media/123contactform-tutorial/tutorial_general_04.png

[100]: ./media/123contactform-tutorial/tutorial_general_100.png

[200]: ./media/123contactform-tutorial/tutorial_general_200.png
[201]: ./media/123contactform-tutorial/tutorial_general_201.png
[202]: ./media/123contactform-tutorial/tutorial_general_202.png
[203]: ./media/123contactform-tutorial/tutorial_general_203.png

