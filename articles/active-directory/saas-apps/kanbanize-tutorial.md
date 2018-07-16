---
title: 'Kurz: Integrace Azure Active Directory se službou Kanbanize | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.openlocfilehash: 746eaadcdb9a588087367c4c70237922cf0f14bf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059596"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Kurz: Integrace Azure Active Directory se službou Kanbanize

V tomto kurzu se dozvíte, jak integrovat Kanbanize s Azure Active Directory (Azure AD).

Kanbanize integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Kanbanize.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Kanbanize (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Kanbanize, potřebujete následující položky:

- S předplatným služby Azure AD
- Kanbanize jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kanbanize z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kanbanize-from-the-gallery"></a>Přidání Kanbanize z Galerie
Konfigurace integrace Kanbanize do služby Azure AD, budete muset přidat Kanbanize z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Kanbanize z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Kanbanize**vyberte **Kanbanize** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Kanbanize v seznamu výsledků](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Kanbanize podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Kanbanize je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Kanbanize potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Kanbanize, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Kanbanize](#create-a-kanbanize-test-user)**  – Pokud chcete mít protějšek Britta Simon Kanbanize, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Kanbanize.

**Ke konfiguraci Azure AD jednotné přihlašování s Kanbanize, proveďte následující kroky:**

1. Na webu Azure Portal na **Kanbanize** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Na **Kanbanize domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Kanbanize domény a adresy URL jednotného přihlašování – informace](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.kanbanize.com/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Zkontrolujte **zobrazit pokročilé nastavení URL**.

    d.  V **stav přenosu** textového pole zadejte adresu URL: `/ctrl_login/saml_login`

    e. Pokud chcete nakonfigurovat aplikace v **SP** iniciované režim, v **přihlašovací adresa URL** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Kanbanize klienta](mailto:support@ms.kanbanize.com) k získání těchto hodnot. 

5. Kanbanize aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale Kanbanize očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace
    
    ![Konfigurace jednotného přihlašování](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Na **Kanbanize konfigurace** klikněte na tlačítko **nakonfigurovat Kanbanize** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. V jiné okno webového prohlížeče, přihlaste se k Kanbanize jako správce zabezpečení. 

10. Přejít na horní pravé části stránky, klikněte na **nastavení** logo.

    ![Nastavení Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Na stránce Správa panelu nabídky na levé straně klikněte na tlačítko **integrace** a pak povolte **Single Sign-On**. 

    ![Integrace Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. V části týkající se integrace, klikněte na **KONFIGUROVAT** otevřete **jednotné přihlašování – integrace** stránky.

    ![Kanbanize config](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Na **jednotné přihlašování – integrace** stránky **konfigurace**, proveďte následující kroky:

    ![Integrace Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. V **Idp Entity Id** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal.

    b. V **koncový bod přihlašovacího účtu Idp** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    c. V **koncový bod odhlašování zprostředkovatele identity** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    d. V **název atributu pro e-mailu** textového pole zadejte tuto hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. V **název atributu pro křestní jméno** textového pole zadejte tuto hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. V **název atributu pro příjmení** textového pole zadejte tuto hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Tyto hodnoty můžete získat díky kombinaci obor názvů a název hodnoty příslušných atributů z části atributy uživatele na webu Azure portal.

    g. V poznámkovém bloku otevřete base-64 kódovaných certifikát, který jste si stáhli z webu Azure portal, zkopírujte jeho obsah (bez úvodní a koncové značky) a vložte jej do **certifikát X.509 Idp** pole.

    h. Zkontrolujte **povolit přihlášení pomocí jednotného přihlašování a Kanbanize**.
    
    i. Klikněte na tlačítko **uložit nastavení**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/kanbanize-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-kanbanize-test-user"></a>Vytvoření zkušebního uživatele Kanbanize

Cílem této části je vytvořte uživatele Britta Simon v Kanbanize. Kanbanize podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Kanbanize, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Kanbanize klienta](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Kanbanize použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Kanbanize Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Kanbanize**.

    ![Odkaz Kanbanize v seznamu aplikací](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Kanbanize na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Kanbanize.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

