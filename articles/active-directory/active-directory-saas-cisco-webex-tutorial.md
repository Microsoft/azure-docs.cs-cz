---
title: 'Kurz: Azure Active Directory integrace s Cisco Webex | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 527d74910244058506e8435d431e239968896cd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Kurz: Azure Active Directory integrace s Cisco Webex

V tomto kurzu zjistěte, jak integrovat Cisco Webex s Azure Active Directory (Azure AD).

Integrace Cisco Webex s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Cisco Webex.
- Můžete povolit uživatelům automaticky získat přihlášení k Cisco Webex s účty služby Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cisco Webex, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné Cisco Webex jeden znak na povoleno

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat bezplatnou zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cisco Webex z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie
Při konfiguraci integrace Cisco Webex do služby Azure AD potřebujete přidat Cisco Webex z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Cisco Webex z galerie, proveďte následující kroky:**

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Cisco Webex**. 

5. Vyberte **Cisco Webex** z panelu výsledků. Vyberte **přidat** tlačítko Přidat aplikaci.

    ![Cisco Webex v seznamu výsledků](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, kdo příslušného uživatele v Cisco Webex je pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele Azure AD a související uživatelské v Cisco Webex.

V Cisco Webex poskytnout hodnota **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dvěma uživateli. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření zkušebního uživatele Cisco Webex](#create-a-cisco-webex-test-user) tak, aby měl protějšek Britta Simon v Cisco Webex propojeném s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Cisco Webex.

**Ke konfiguraci Azure AD jednotné přihlašování s Cisco Webex, proveďte následující kroky:**

1. Na portálu Azure na **Cisco Webex** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogovém **režimu** rozevíracího seznamu vyberte **na základě SAML přihlašování**.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. V **Cisco Webex domény a adresy URL** část, proveďte následující kroky:

    ![Cisco Webex domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí vzoru následující: `https://<subdomain>.webex.com`

    b. V **identifikátor** pole, zadejte adresu URL `http://www.webex.com`.

    c. V **adresa URL odpovědi** pole, zadejte adresu URL pomocí vzoru následující: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizovat pomocí adresy URL skutečné odpovědi a adresa URL přihlašování. Obraťte se na [tým podpory Cisco Webex klienta](https://www.webex.co.in/support/support-overview.html) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** vyberte **soubor XML s metadaty**a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Vyberte **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. V **konfigurace Cisco Webex** vyberte **konfigurace Cisco Webex** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka** části.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Cisco Webex jako správce.

8. V nabídce nahoře vyberte **Správa webu**.

    ![Správa lokalit](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "lokality správy")

9. V **spravovat lokality** vyberte **Konfigurace jednotného přihlašování k**.
   
    ![Konfigurace jednotného přihlašování k](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "Konfigurace jednotného přihlašování")

10. V **federovaného jednotného přihlašování k konfigurace webové** část, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování federovaného](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "federovaného jednotného přihlašování k konfigurace")  

    a. V **Federation protokol** seznamu, vyberte **SAML 2.0**.

    b. Pro **profil jednotného přihlašování k**, vyberte **iniciované SP**.

    c. Otevřete stažený certifikát v programu Poznámkový blok a zkopírujte obsah.

    d. Vyberte **importovat Metadata SAML**a potom vložte zkopírovaný obsahu certifikátu.

    e. V **vystavitele pro SAML (IdP ID)** pole, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.

    f. V **adresy URL zákazníka jednotného přihlašování služby přihlášení** pole, vložte **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    g. Z **NameID formátu** seznamu, vyberte **e-mailová adresa**.

    h. V **AuthnContextClassRef** zadejte **urn: oasis: názvy: tc: SAML:2.0:ac:classes:Password**.

    i. V **adresy URL odhlašovací zákazníka jednotného přihlašování služby** pole, vložte **Sign-Out URL**, který jste zkopírovali z portálu Azure.
   
    j. Vyberte **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com) při k nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace, které** vyberte **jednotné přihlašování** kartě a potom přejdete embedded dokumentace prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace v [Azure AD vložených dokumentaci](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom vyberte **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-cisco-webex-test-user"></a>Vytvoření zkušebního uživatele Cisco Webex

Pokud chcete povolit uživatelům Azure AD přihlášení k Cisco Webex, musí být zřízená v Cisco Webex. V případě Cisco Webex zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Cisco Webex** klienta.

2. Přejděte na **Správa uživatelů** > **přidat uživatele**.
   
    ![Přidání uživatelů](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "přidat uživatele")

3. V **přidat uživatele** část, proveďte následující kroky:
   
    ![Přidat uživatele](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "přidat uživatele")   

    a. Pro **typ účtu**, vyberte **hostitele**.

    b. V **křestní jméno** zadejte jméno uživatele (v tomto případě **Britta**).

    c. V **příjmení** zadejte příjmení uživatele (v tomto případě **Simon**).

    d. V **uživatelské jméno** zadejte e-mailu uživatele (v tomto případě **Brittasimon@contoso.com**).

    e. V **e-mailu** zadejte e-mailovou adresu uživatele (v tomto případě **Brittasimon@contoso.com**).

    f. V **heslo** zadejte heslo uživatele.

    g. V **potvrdit** heslo pole, zadejte znovu heslo uživatele.

    h. Vyberte **Přidat**.

>[!NOTE]
>Můžete použít jiné nástroje pro tvorbu Cisco Webex uživatelského účtu nebo rozhraní API, které jsou poskytovány Cisco Webex ke zřízení uživatelských účtů Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části můžete zajistit, aby uživatel Britta Simon chcete použít Azure jednotné přihlašování je k udělení přístupu k Cisco Webex.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Cisco Webex, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikace. Další, přejděte do zobrazení adresáře a pak na **podnikové aplikace, které**.  

2. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

3. V seznamu aplikací vyberte **Cisco Webex**.

    ![V seznamu aplikací na odkaz Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v **uživatelé** seznamu.

6. V **uživatelů a skupin** dialogové okno, klikněte **vyberte** tlačítko.

7. Vyberte **přiřadit** v tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části otestovat vaše konfigurace Azure AD jeden přihlašování pomocí přístupového panelu.

Když vyberete dlaždici Cisco Webex na přístupovém panelu, budete automaticky získat přihlášení k aplikaci Cisco Webex.

Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

