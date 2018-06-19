---
title: 'Kurz: Azure Active Directory integrace s 123ContactForm | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: ca54492fd1fa68a4b81eaea431375ff58623e68b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899464"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Kurz: Azure Active Directory integrace s 123ContactForm

V tomto kurzu zjistěte, jak integrovat 123ContactForm s Azure Active Directory (Azure AD).

Integrace 123ContactForm s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k 123ContactForm
- Můžete povolit uživatelům, aby automaticky získat přihlášení k 123ContactForm (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s 123ContactForm, potřebujete následující položky:

- Předplatné služby Azure AD
- 123ContactForm jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání 123ContactForm z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-123contactform-from-the-gallery"></a>Přidání 123ContactForm z Galerie
Při konfiguraci integrace 123ContactForm do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS 123ContactForm z galerie.

**Pokud chcete přidat 123ContactForm z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **123ContactForm**.

    ![Vytváření testovacího uživatele Azure AD](./media/123contactform-tutorial/tutorial_123contactform_search.png)

5. Na panelu výsledků vyberte **123ContactForm**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s 123ContactForm podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v 123ContactForm je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v 123ContactForm musí navázat.

V 123ContactForm, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s 123ContactForm, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele 123ContactForm](#creating-a-123contactform-test-user)**  – Pokud chcete mít protějšek Britta Simon v 123ContactForm propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci 123ContactForm.

**Ke konfiguraci Azure AD jednotné přihlašování s 123ContactForm, proveďte následující kroky:**

1. Na portálu Azure na **123ContactForm** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Na **123ContactForm domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP iniciované režimu**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/url1.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/url2.png)

    a. Klikněte **zobrazit upřesňující nastavení adresy URL** možnost

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL jako: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Budete muset aktualizovat tyto hodnoty z skutečné adresy URL a identifikátor, který je vysvětlen později v tomto kurzu.
    
5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/tutorial_general_400.png)

7. Konfigurace jednotného přihlašování na **123ContactForm** straně, přejděte na [ https://www.123contactform.com/form-2709121/ ](https://www.123contactform.com/form-2709121/) a proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/submit.png) 

    a. V **e-mailu** textovému poli, zadejte e-mail jednofaktorovému uživatele **BrittaSimon@Contoso.com**.

    b. Klikněte na tlačítko **nahrát** a vyhledejte soubor XML s metadaty souboru, který jste si stáhli z portálu Azure.

    c. Klikněte na tlačítko **odeslání formuláře**.

8. Na **nakonfigurovat nastavení aplikace služby Microsoft Azure AD jednotné přihlašování –** proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/url3.png)

    a. Pokud chcete nakonfigurovat aplikace **IDP iniciované režimu**, kopie **IDENTIFIKÁTOR** instance a vložte ji v **identifikátor** textového pole v **123ContactForm domény a adresy URL** části na portálu Azure.
    
    b. Pokud chcete nakonfigurovat aplikace **IDP iniciované režimu**, kopie **adresa URL odpovědi** instance a vložte ji v **adresa URL odpovědi** textového pole v **123ContactForm domény a adresy URL** části na portálu Azure.

    c. Pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, kopie **ON PŘIHLAŠOVACÍ adresa URL** instance a vložte ji v **přihlašovací adresa URL** textového pole v **123ContactForm domény a adresy URL** části na portálu Azure.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/123contactform-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/123contactform-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/123contactform-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/123contactform-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-123contactform-test-user"></a>Vytvoření zkušebního uživatele 123ContactForm

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele automaticky se vytvoří v aplikaci.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k 123ContactForm.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon 123ContactForm, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **123ContactForm**.

    ![Konfigurovat jednotné přihlašování](./media/123contactform-tutorial/tutorial_123contactform_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici 123ContactForm na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci 123ContactForm.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

