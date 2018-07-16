---
title: 'Kurz: Integrace Azure Active Directory pomocí Adobe Sign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe Sign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053994"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Integrace Azure Active Directory pomocí Adobe Sign

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Adobe Sign.

Adobe Sign integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Adobe Sign.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Adobe Sign (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Adobe Sign, budete potřebovat:

- S předplatným služby Azure AD
- Adobe Sign jednotného přihlašování povolená předplatného

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Adobe Sign z galerie.
2. Konfigurace a testování Azure AD jednotného přihlašování.

## <a name="add-adobe-sign-from-the-gallery"></a>Přidání Adobe Sign z Galerie
Pokud chcete nakonfigurovat integraci Adobe Sign do služby Azure AD, budete muset přidat Adobe Sign z Galerie na váš seznam spravovaných aplikací SaaS.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Snímek obrazovky ikonu Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Snímek obrazovky z Azure Active Directory nabídky, s podnikovým aplikacím a zvýrazní všechny aplikace][2]
    
3. Chcete-li přidat novou aplikaci, **novou aplikaci** v horní části dialogového okna.

    ![Snímek obrazovky s novou možností pro aplikace v horní části dialogového okna][3]

4. Do vyhledávacího pole zadejte **Adobe Sign**.

    ![Snímek obrazovky dialogového okna Vyhledat](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Na panelu výsledků vyberte **Adobe Sign**a pak vyberte **přidat**.

    ![Snímek obrazovky panel výsledků](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Adobe Sign, na základě testovací uživatele nazývá "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí rozpoznat propojené vztah mezi uživatele služby Azure AD a související uživatelské v Adobe Sign.

Vytvořit propojenou relaci v Adobe Sign, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno**.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Adobe Sign, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configuring-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Vytvořit testovacího uživatele Azure AD](#creating-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvořit testovacího uživatele Adobe Sign](#creating-an-adobe-sign-test-user) mít protějšek Britta Simon Adobe Sign, který je spojen s Azure AD reprezentace uživatele.
4. [Přiřadit uživatele Azure AD](#assigning-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Otestovat jednotné přihlašování](#testing-single-sign-on) ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Adobe Sign.

1. Na webu Azure Portal na **Adobe Sign** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Stránka integrace aplikace – snímek obrazovky Adobe Sign, pomocí jednotného přihlašování zvýrazněnou][4]

2. Na **jednotného přihlašování** dialogovém okně pro **režimu**vyberte **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Snímek obrazovky jednotné přihlašování dialogové okno, s pole režimu zvýrazněnou](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. V **Adobe Sign domény a adresy URL** části, proveďte následující kroky:

    ![Snímek obrazovky Adobe Sign domény a adresy URL](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, která používá následující vzorec: `https://<companyname>.echosign.com/`

    b. V **identifikátor** textové pole, zadejte adresu URL, která používá následující vzorec: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) k získání těchto hodnot.

4. V **podpisový certifikát SAML** vyberte **Certificate(Base64)** a poté uložte soubor certifikátu v počítači.

    ![Snímek obrazovky z podpisový certifikát SAML oddílu](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Vyberte **Uložit**.

    ![Uložte snímek obrazovky tlačítka](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. V **Adobe Sign konfigurace** vyberte **konfigurace Adobe Sign** otevřít **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka** oddílu.

    ![Snímek obrazovky z Adobe Sign konfigurační oddíl pomocí Adobe Sign konfigurace zvýrazněnou](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Před konfigurací, obraťte se [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) na seznam povolených vaši doménu v Adobe Sign. Tady je postup pro přidání domény:

    a. [Tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) odešle náhodně vygenerovaný token. Pro vaši doménu token, který bude podobný tomuto: **adobe sign ověřovací = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikování token pro ověření v textový záznam DNS a upozornit [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Může to trvat několik dnů nebo déle. Všimněte si, že zpoždění šíření DNS znamená, že hodnota publikovaný ve službě DNS nemusí být viditelná pro hodinu nebo déle. Váš správce IT by měl být informovanosti o tom, jak publikovat tento token v textový záznam DNS.
    
    c. Když upozornit [tým podpory Adobe Sign klienta](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory po publikování token se ověření domény a přidejte ke svému účtu.
    
    d. Obecně platí tady je postup pro publikování token na záznam DNS:

    * Přihlaste se ke svému účtu domény
    * Vyhledejte stránku pro aktualizaci záznamů DNS. Na této stránce může být volána, správu DNS, Správa názvového serveru nebo Upřesnit nastavení.
    * Vyhledání záznamů TXT pro vaši doménu.
    * Přidejte záznam TXT úplné token hodnotou poskytnutou Adobe.
    * Uložte provedené změny.

8. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Adobe Sign jako správce.

9. V nabídce SAML, vyberte **nastavení účtu** > **nastavení SAML**.
   
    ![Stránka nastavení snímku obrazovky z Adobe Sign SAML](./media/adobe-echosign-tutorial/ic789520.png "účtu")

10. V **nastavení SAML** části, proveďte následující kroky:
  
    ![Snímek obrazovky nastavení SAML](./media/adobe-echosign-tutorial/ic789521.png "nastavení SAML")
   
    a. V části **SAML režimu**vyberte **SAML povinné**.
   
    b. Vyberte **umožnit správci Echosign účtu přihlásit pomocí svých přihlašovacích údajů Echosign**.
   
    c. V části **vytvoření uživatele**vyberte **automaticky přidat uživatele ověřeni pomocí SAML**.

    d. Vložit **SAML Entity ID**, který jste zkopírovali z portálu Azure portal do **Entity ID-Vystavitel URL** textového pole.
    
    e. Vložit **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal do **koncový bod adresy URL/SSO** textového pole.
   
    f. Vložit **odhlašování URL**, který jste zkopírovali z portálu Azure portal do **koncový bod adresy URL/SLO odhlášení** textového pole.

    g. Otevřete váš stažené **Certificate(Base64)** soubor v poznámkovém bloku. Zkopírujte obsah ho do schránky a vložte ho do **IdP certifikát** textového pole.

    h. Vyberte **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele s názvem Britta Simon, na webu Azure Portal.

![Snímek obrazovky testovací uživatelské jméno na portálu Azure portal][100]

1. V **webu Azure portal**, v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Snímek obrazovky ikonu Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a vyberte **všichni uživatelé**.
    
    ![Snímek obrazovky s Azure AD nabídky, se uživatelé a skupiny a zvýrazní všechny uživatele](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogu **přidat**.
 
    ![Snímek obrazovky začátku všechny dialogové okno Uživatelé, se zvýrazněnou možností přidat](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno snímek obrazovky uživatele](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. V **název** textového pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, zadejte e-mailová adresa BrittaSimon.

    c. Vyberte **zobrazit heslo**a poznamenejte si hodnotu **heslo**.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-adobe-sign-test-user"></a>Vytvořit testovacího uživatele Adobe Sign

Povolení služby Azure AD uživatelům umožní přihlásit k Adobe Sign, musí být poskytnuty do Adobe Sign. Toto je ruční úloha.

>[!NOTE]
>Ke zřízení uživatelských účtů služby Azure AD můžete použít jiné nástroje pro tvorbu Adobe Sign uživatelského účtu nebo rozhraní API poskytovaných Adobe Sign. 

1. Přihlaste se k vaší **Adobe Sign** společnosti serveru jako správce.

2. V nabídce v horní části vyberte **účet**. V levém podokně vyberte **uživatelů a skupin** > **vytvořte nového uživatele**.
   
    ![Snímek obrazovky Adobe Sign podnikové lokality, pomocí účtu, uživatelé a skupiny a vytvořte nového uživatele zvýrazněný](./media/adobe-echosign-tutorial/ic789524.png "účtu")
   
3. V **vytvořit nového uživatele** části, proveďte následující kroky:
   
    ![Snímek obrazovky vytvoření nového uživatele části](./media/adobe-echosign-tutorial/ic789525.png "vytvořit uživatele")
   
    a. Typ **e-mailovou adresu**, **křestní jméno**, a **příjmení** platný Azure AD účet ke zřízení do související textových polí.
   
    b. Vyberte **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mailu, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon pomocí Azure jednotného přihlašování, udělení přístupu k Adobe Sign.

![Snímek obrazovky Azure portal jednotného přihlašování][200] 

1. Na webu Azure Portal otevřete zobrazení aplikace. Pak přejděte do zobrazení adresáře, přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Snímek obrazovky Azure portálovými aplikacemi zobrazení s podnikovým aplikacím a zvýrazní všechny aplikace][201] 

2. V seznamu aplikací vyberte **Adobe Sign**.

    ![Snímek obrazovky aplikace seznam zvýrazní Adobe Sign](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Snímek obrazovky nabídky s uživateli a skupinami zvýrazněnou][202] 

4. Vyberte **Přidat**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Snímek obrazovky uživatele a skupiny stránku a přidejte část přiřazení][203]

5. V **uživatelů a skupin** dialogové okno, v seznamu uživatelů vyberte **Britta Simon**.

6. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte**.

7. V **přidat přiřazení** dialogu **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Když vyberete dlaždici Adobe Sign na přístupovém panelu, vám by měl získat automaticky přihlášený k aplikaci Adobe Sign. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
