---
title: 'Kurz: Integrace Azure Active Directory se službou Optimizely | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: be56218e174e5d8b0e6bde394f2dfd40fc91e87d
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42059062"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Kurz: Integrace Azure Active Directory se službou Optimizely

V tomto kurzu se dozvíte, jak integrovat Optimizely s Azure Active Directory (Azure AD).

Optimizely integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Optimizely
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Optimizely (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Optimizely, potřebujete následující položky:

- Předplatné Azure AD
- Optimizely jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Optimizely z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-optimizely-from-the-gallery"></a>Přidání Optimizely z Galerie

Konfigurace integrace Optimizely do služby Azure AD, budete muset přidat Optimizely z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Optimizely z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Optimizely**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/tutorial_optimizely_search.png)

5. Na panelu výsledků vyberte **Optimizely**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování s Optimizely podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Optimizely je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Optimizely potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Optimizely.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Optimizely, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se testovací uživatelské jméno Optimizely](#creating-an-optimizely-test-user)**  – Pokud chcete mít protějšek Britta Simon Optimizely, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Optimizely.

**Ke konfiguraci Azure AD jednotné přihlašování s Optimizely, proveďte následující kroky:**

1. Na webu Azure Portal na **Optimizely** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Na **Optimizely domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_optimizely_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://app.optimizely.net/<instance name>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:  `urn:auth0:optimizely:contoso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Hodnota aktualizuje o skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu.

4. Optimizely aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_optimizely_attribute.png)
    
5. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtávací políčko ve **atributy uživatele** rozbalte atributy. Proveďte následující kroky na všechny zobrazené atributy-

    | Název atributu | Hodnota atributu |
    | ---------------| --------------- |
    | e-mail | User.Mail |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_attribute_05.png)

    b. V **název** textové pole, typ **název atributu** zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Klikněte na tlačítko **OK**.

6. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_optimizely_certificate.png)

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_general_400.png)

8. Na **Optimizely konfigurace** klikněte na tlačítko **nakonfigurovat Optimizely** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_optimizely_configure.png)

9. Ke konfiguraci jednotného přihlašování na **Optimizely** straně, kontaktujte vašeho Account manažera Optimizely a poskytovat na stažený **certifikát (Base64)**, a **SAML jednotné přihlašování – adresa URL služby**.

10. V reakci na vaši e-mailu Optimizely poskytuje adresu URL na přihlášení (SSO iniciovaného Zprostředkovatelem přihlašování) a hodnot identifikátoru (ID Entity poskytovatele služby).

    a. Kopírovat **iniciovaného Zprostředkovatelem přihlašování adresu URL jednotného přihlašování** Optimizely a vložit do zadané **přihlašovací adresa URL** textového pole v **Optimizely domény a adresy URL** části na webu Azure portal.

    b. Kopírovat **ID Entity poskytovatele služby** Optimizely a vložit do zadané **identifikátor** textového pole v **Optimizely domény a adresy URL** části na webu Azure portal.

11. V jiném okně prohlížeče přihlašování k aplikaci Optimizely.

12. Klikněte na účet pravému hornímu název v horní části a pak **nastavení účtu**.

    ![Azure AD jednotné přihlašování](./media/optimizely-tutorial/tutorial_optimizely_09.png)

13. Na kartě účet, zaškrtněte políčko **povolit jednotné přihlašování** v rámci jednotného přihlašování v **přehled** oddílu.
  
    ![Azure AD jednotné přihlašování](./media/optimizely-tutorial/tutorial_optimizely_10.png)

14. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** Britta Simon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-an-optimizely-test-user"></a>Vytvoření Optimizely testovacího uživatele

V této části vytvoříte uživatele v Optimizely jako Britta Simon.

1. Na domovské stránce vyberte **spolupracovníci** kartu.

2. Přidat nový spolupracovník do projektu, klikněte na **nové spolupracovníka**.
   
    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_10.png)

3. Zadejte e-mailovou adresu a přiřadit jim roli. Klikněte na tlačítko **pozvat**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/optimizely-tutorial/create_aaduser_11.png)

4. Obdrží tento uživatel e-mailová pozvánka. Pomocí e-mailovou adresu, mají se přihlaste k Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Optimizely použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Optimizely Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **Optimizely**.

    ![Konfigurace jednotného přihlašování](./media/optimizely-tutorial/tutorial_optimizely_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Optimizely na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Optimizely.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/optimizely-tutorial/tutorial_general_01.png
[2]: ./media/optimizely-tutorial/tutorial_general_02.png
[3]: ./media/optimizely-tutorial/tutorial_general_03.png
[4]: ./media/optimizely-tutorial/tutorial_general_04.png

[100]: ./media/optimizely-tutorial/tutorial_general_100.png

[200]: ./media/optimizely-tutorial/tutorial_general_200.png
[201]: ./media/optimizely-tutorial/tutorial_general_201.png
[202]: ./media/optimizely-tutorial/tutorial_general_202.png
[203]: ./media/optimizely-tutorial/tutorial_general_203.png