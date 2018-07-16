---
title: 'Kurz: Integrace Azure Active Directory s Cezanne HR softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cezanne HR softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 60133dd6d541500db448cf107dd3c0ab193a03f7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048683"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Kurz: Integrace Azure Active Directory s Cezanne HR softwaru

V tomto kurzu se dozvíte, jak integrovat Cezanne HR Software s Azure Active Directory (Azure AD).

Integrace softwaru Cezanne HR s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k softwaru Cezanne HR.
- Uživatele, aby automaticky získat přihlášení k softwaru HR Cezanne (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cezanne HR softwaru, potřebujete následující položky:

- S předplatným služby Azure AD
- Cezanne HR jednotné přihlašování povoleno předplatné softwaru

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cezanne HR softwaru z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Přidání Cezanne HR softwaru z Galerie
Konfigurace integrace Cezanne HR softwaru do služby Azure AD, budete muset přidat Cezanne HR Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Cezanne HR softwaru z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cezanne HR softwaru**vyberte **Cezanne HR softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Software HR Cezanne v seznamu výsledků](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Cezanne HR Software založený na uživateli test "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek Cezanne HR softwaru je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské Cezanne HR softwaru je potřeba navázat.

V softwaru HR Cezanne, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cezanne HR softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele HR softwaru Cezanne](#create-a-cezannehrsoftware-test-user)**  – Pokud chcete mít protějšek Britta Simon Cezanne HR Software, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Cezanne HR softwaru.

**Ke konfiguraci Azure AD jednotné přihlašování s Cezanne HR softwaru, proveďte následující kroky:**

1. Na webu Azure Portal na **Cezanne HR softwaru** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. Na **Cezanne HR softwaru domény a adresy URL** části, proveďte následující kroky:

    ![Cezanne HR softwaru domény a adresy URL jednotného přihlašování – informace](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. V **identifikátor** textového pole zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a adresy URL odpovědi. Kontakt [tým podpory Cezanne HR softwarového klienta](https://cezannehr.com/services/support/) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

6. Na **konfigurace softwaru HR Cezanne** klikněte na tlačítko **konfigurace softwaru HR Cezanne** otevřete **nakonfigurovat přihlašování** okna.

    ![Konfigurace softwaru Cezanne HR](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. Přejděte dolů k položce **Stručná referenční příručka** oddílu. Kopírovat **SAML jednotné přihlašování – adresa URL služby a SAML Entity ID** z **Stručná referenční příručka oddílu.**

    ![Konfigurace softwaru Cezanne HR](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi Cezanne HR softwaru jako správce.

9. V levém navigačním podokně klikněte na tlačítko **nastavení systému**. Přejděte na **nastavení zabezpečení**. Přejděte na **Konfigurace jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. V **umožnit uživatelům přihlášení pomocí následující jednotné přihlašování (SSO) služby** panel, zkontrolujte **SAML 2.0** a vyberte **Upřesnit konfiguraci** možnost.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. Klikněte na tlačítko **přidat nový** tlačítko.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. Následující postup proveďte **poskytovatele IDENTITY SAML 2.0** oddílu.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. Zadejte název vašeho zprostředkovatele Identity, jako **zobrazovaný název**.

    b. V **identifikátor Entity** textového pole vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure portal. 

    c. Změnit **SAML vazby** na "POST".

    d. V **koncový bod služby tokenu zabezpečení** textového pole vložte hodnotu **SAML pro jednotné přihlašování – adresa URL služby** který jste zkopírovali z portálu Azure portal.

    e. Do textového pole Název atributu ID uživatele, zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    f. Klikněte na tlačítko **nahrát** ikonu a nahrát na server certifikát stažený z webu Azure portal.
    
    g. Klikněte na tlačítko **OK**. 

13. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Vytvoření zkušebního uživatele Cezanne HR softwaru

Chcete-li povolit uživatele Azure AD k přihlášení do Cezanne HR softwaru, musí být poskytnuty do Cezanne HR softwaru. V případě Cezanne HR softwaru je zřizování úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1.  Přihlaste se ke serveru vaší společnosti Cezanne HR softwaru jako správce.

2.  V levém navigačním podokně klikněte na tlačítko **nastavení systému**. Přejděte na **spravovat uživatele**. Přejděte na **Add New User**.

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "nového uživatele")

3.  Na **podrobnosti o osobě** části, proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "nového uživatele")
    
    a. Nastavte **interního uživatele** jako OFF.
    
    b. V **křestní jméno** , jako je textové pole, typ křestní jméno uživatele **Britta**.  
 
    c. V **příjmení** , jako je textové pole, typ příjmení uživatele **Simon**.
    
    d. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

4.  Na **informace o účtu** části, proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "nového uživatele")
    
    a. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.
    
    b. V **heslo** textového pole zadejte heslo uživatele.
    
    c. Vyberte **HR Professional** jako **Role zabezpečení**.
    
    d. Klikněte na **OK**.

5. Přejděte do **Single Sign-On** kartě a vyberte **přidat nový** v **SAML 2.0 identifikátory** oblasti.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "uživatele")

6. Vyberte zprostředkovatele Identity pro **zprostředkovatele Identity** a v textovém poli **identifikátor uživatele**, zadejte e-mailovou adresu účtu Britta Simon.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "uživatele")
    
7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "uživatele")

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k softwaru Cezanne HR.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit Cezanne HR softwaru, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Cezanne HR softwaru**.

    ![Odkaz Cezanne HR Software v seznamu aplikací](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Cezanne HR softwaru na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Cezanne HR softwaru.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

