---
title: 'Kurz: Integrace Azure Active Directory ScaleX Enterprise | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: 3b2da2680adbc92655030351cc9e1269a4cccccd
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040974"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Kurz: Integrace Azure Active Directory ScaleX Enterprise

V tomto kurzu se dozvíte, jak integrovat ScaleX organizace Azure Active Directory (Azure AD).

ScaleX podnikové integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do firemní sítě ScaleX
- Uživatele, aby automaticky získat přihlášení k ScaleX Enterprise (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma. Co je přístup k aplikaci a jednotné přihlašování s [Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ScaleX Enterprise, budete potřebovat následující položky:

- S předplatným služby Azure AD
- ScaleX podnikové jednotné přihlašování v předplatném povolené

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání organizace ScaleX z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Přidání organizace ScaleX z Galerie
Ke konfiguraci integrace ScaleX Enterprise v Azure AD, budete muset přidat ScaleX Enterprise na váš seznam spravovaných aplikací SaaS z galerie.

**Přidání organizace ScaleX z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **ScaleX Enterprise**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

5. Na panelu výsledků vyberte **ScaleX Enterprise**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a test Azure AD jednotné přihlašování s ScaleX Enterprise podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v ScaleX Enterprise je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v ScaleX organizace je potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v ScaleX Enterprise.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ScaleX organizace, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele organizace ScaleX](#creating-a-scalex-enterprise-test-user)**  – Pokud chcete mít protějšek Britta Simon ScaleX organizace, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci ScaleX Enterprise.

**Ke konfiguraci Azure AD jednotné přihlašování s ScaleX organizace, proveďte následující kroky:**

1. Na webu Azure Portal na **ScaleX Enterprise** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

3. Na **ScaleX podnikové domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. V **identifikátor** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://platform.rescale.com/saml2/<company id>/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://platform.rescale.com/saml2/<company id>/acs/`

4. Zkontrolujte **zobrazit pokročilé nastavení URL**, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    V **přihlašovací adresa URL** textového pole zadejte hodnotu pomocí následujícího vzorce: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Ty nejsou skutečné hodnoty. Tyto hodnoty aktualizujte pomocí skutečné identifikátor, adresa URL odpovědi nebo přihlašovací adresa URL. Kontakt [tým podpory ScaleX Enterprise Client](http://info.rescale.com/contact_sales) k získání těchto hodnot. 

5. Vaše aplikace ScaleX očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba upravit mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** příslušných políček otevřete vlastní atributy nastavení.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Klikněte pravým tlačítkem na atribut **název** a klikněte na příkaz odstranit.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Klikněte na tlačítko **emailaddress** atributu otevřete okno Úprava atributů. Změňte svou hodnotu z **user.mail** k **user.userprincipalname** a klikněte na tlačítko Ok.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
5. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
7. Na **ScaleX podniková konfigurace** klikněte na tlačítko **konfigurace ScaleX Enterprise** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

8. Ke konfiguraci jednotného přihlašování na **ScaleX Enterprise** straně, přihlaste se k webu společnosti ScaleX Enterprise jako správce.

9. Klikněte na možnost z nabídky pravém horním rohu a vyberte **Contoso správu**.

    > [!NOTE] 
    > Contoso je uvedené jenom jako příklad. To by měl být skutečný název vaší společnosti. 

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/Test_Admin.png) 

10. Vyberte **integrace** v horní nabídce a vyberte **Single Sign-On**.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/admin_sso.png) 

11. Vyplňte formulář následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Vyberte **"Vytvořit libovolný uživatel, který může ověřit s jednotným Přihlašováním."**

    b. **Poskytovatel služeb saml**: vložte hodnotu ***urn: oasis: názvy: tc: SAML:2.0:nameid – formát: trvalé***

    c. **Název pole zprostředkovatele Identity e-mailu v odpovědi ACS**: vložte tuto hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID EntityDescriptor Entity zprostředkovatele identity:** vložit **SAML Entity ID** hodnota zkopírována z portálu Azure portal.

    e. **Adresa URL SingleSignOnService zprostředkovatele identity:** vložit **SAML jednotné přihlašování – adresa URL služby** z portálu Azure portal.

    f. **Poskytovatel veřejného X509 certifikát identity:** otevřít X509 certifikátu si stáhli z Azure v programu Poznámkový blok a vložte obsah do tohoto pole. Zajistěte, že žádná zalomení řádků uprostřed obsahu certifikátu.
    
    g. Zaškrtněte následující políčka: **povoleno, šifrovat NameID a AuthnRequests přihlašování.**

    h. Klikněte na tlačítko **aktualizace nastavení jednotného přihlašování** uložte nastavení.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

2. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

3. V horní části dialogového okna, klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Vytvoření zkušebního uživatele ScaleX Enterprise

Povolit uživatele Azure AD se přihlaste k ScaleX Enterprise, se musí být zřízená v na ScaleX Enterprise. V případě ScaleX Enterprise zřizování se automatická úloha a nejsou požadované žádné ruční kroky. Každý uživatel, který můžete provádět ověření pomocí přihlašovacích údajů pro jednotné přihlašování se automaticky zřídí na straně ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup uživatelů k ScaleX Enterprise.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit ScaleX organizace, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ScaleX Enterprise**.

    ![Konfigurace jednotného přihlašování](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Kliknutím na dlaždici ScaleX Enterprise na přístupovém panelu, můžete se získat automaticky přihlášení k aplikaci ScaleX Enterprise. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

