---
title: 'Kurz: Azure Active Directory integrace s KnowledgeOwl | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 1ac63d512645b2eb9627798809ce699129799c94
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-knowledgeowl"></a>Kurz: Azure Active Directory integrace s KnowledgeOwl

V tomto kurzu zjistěte, jak integrovat KnowledgeOwl s Azure Active Directory (Azure AD).

Integrace KnowledgeOwl s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k KnowledgeOwl.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k KnowledgeOwl (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s KnowledgeOwl, potřebujete následující položky:

- Předplatné služby Azure AD
- KnowledgeOwl jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání KnowledgeOwl z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-knowledgeowl-from-the-gallery"></a>Přidání KnowledgeOwl z Galerie
Při konfiguraci integrace KnowledgeOwl do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS KnowledgeOwl z galerie.

**Pokud chcete přidat KnowledgeOwl z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **KnowledgeOwl**, vyberte **KnowledgeOwl** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![KnowledgeOwl v seznamu výsledků](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s KnowledgeOwl podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v KnowledgeOwl je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v KnowledgeOwl musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s KnowledgeOwl, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele KnowledgeOwl](#create-a-knowledgeowl-test-user)**  – Pokud chcete mít protějšek Britta Simon v KnowledgeOwl propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci KnowledgeOwl.

**Ke konfiguraci Azure AD jednotné přihlašování s KnowledgeOwl, proveďte následující kroky:**

1. Na portálu Azure na **KnowledgeOwl** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_samlbase.png)

3. Na **KnowledgeOwl domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![KnowledgeOwl domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url.png)

    a. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    |||
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|
    |||

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![KnowledgeOwl domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:
    |||
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|
    |||
     
    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Budete muset aktualizovat tyto hodnoty ze skutečného identifikátoru, adresa URL odpovědi a přihlašování adresy URL, která se vysvětluje dále v tomto kurzu.

5. Aplikace KnowledgeOwl očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-knowledgeowl-tutorial/attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Obor názvů|
    | ------------------- | -------------------- | -----|
    | ssoid | User.Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.

    d.From **Namespace** seznamu, zadejte hodnotu oboru názvů, který je uvedený na příslušném řádku.
    
    e. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Raw)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_400.png)
    
9. Na **KnowledgeOwl konfigurace** klikněte na tlačítko **konfigurace KnowledgeOwl** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_configure.png)

10. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti KnowledgeOwl.

11. Klikněte na **nastavení** a pak vyberte **zabezpečení**.

    ![Konfigurace KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure1.png)

12. Projděte dolů až **integrace jednotné přihlašování SAML** a proveďte následující kroky:
    
    ![Konfigurace KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure2.png)

    a. Vyberte **povolení jednotného přihlašování SAML**.

    b. Kopírování **SP Entity ID** a vložte ji do **identifikátoru (Entity ID)** v **KnowledgeOwl domény a adresy URL** části na portálu Azure.

    c. Kopírování **SP přihlašovací adresa URL** a vložte ji do **přihlašovací adresa URL a adresa URL odpovědi** textových polí v **KnowledgeOwl domény a adresy URL** části na portálu Azure.

    d. V **IdP entityID** textovému poli, Vložit **SAML Entity ID** hodnotu, kterou jste zkopírovali z portálu Azure.

    e. V **IdP přihlašovací adresa URL** textovému poli, Vložit **SAML jeden přihlašování adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure.

    f. V **adresy URL odhlašovací IdP** textovému poli, Vložit **Sign-Out URL** hodnotu, kterou jste zkopírovali z portálu Azure

    g. Odeslání formuláře stažený certifikát webu Azure portal kliknutím **nahrát certifikát IdP**.

    h. Klikněte na **mapy SAML atributy** mapování atributů a proveďte následující kroky:
    
    ![Konfigurace KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure3.png)

    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` do **jednotného přihlašování k ID** textbox
    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` do **uživatelského jména nebo e-mailu** textové pole.
    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` do **křestní jméno** textové pole.
    * Zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` do **příjmení** textové pole.
    * Klikněte na **Uložit**.

    i. V dolní části stránky klikněte na **Uložit**.

    ![Konfigurace KnowledgeOwl](./media/active-directory-saas-knowledgeowl-tutorial/configure4.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-knowledgeowl-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-knowledgeowl-test-user"></a>Vytvoření zkušebního uživatele KnowledgeOwl

Cílem této části je vytvoření uživatele v KnowledgeOwl nazývá Britta Simon. KnowledgeOwl podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k KnowledgeOwl, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživateli, obraťte se na [tým podpory KnowledgeOwl](mailto:support@knowledgeowl.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu KnowledgeOwl.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon KnowledgeOwl, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **KnowledgeOwl**.

    ![V seznamu aplikací na KnowledgeOwl odkaz](./media/active-directory-saas-knowledgeowl-tutorial/tutorial_knowledgeowl_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici KnowledgeOwl na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci KnowledgeOwl.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-knowledgeowl-tutorial/tutorial_general_203.png

