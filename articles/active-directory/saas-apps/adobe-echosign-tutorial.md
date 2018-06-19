---
title: 'Kurz: Azure Active Directory integrace s Adobe přihlašovací | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Adobe přihlášení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d91a64762181fd4c261f1ba964d366253912f009
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35901638"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Azure Active Directory integrace s Adobe přihlášení

V tomto kurzu zjistěte, jak integrovat Adobe přihlašovací službou Azure Active Directory (Azure AD).

Integrace Adobe přihlášení s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup na Adobe přihlašování.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k přihlášení Adobe (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě – portál Azure.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD znakem Adobe, potřebujete:

- Předplatné služby Azure AD
- Přihlášení Adobe jednotné přihlašování povolené předplatné

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání značka Adobe z galerie.
2. Konfigurace a testování Azure AD jednotného přihlašování.

## <a name="add-adobe-sign-from-the-gallery"></a>Přidání Adobe z Galerie
Při konfiguraci Integrace Adobe registrace do služby Azure AD, potřebujete přidat přihlašovací Adobe z Galerie si na seznam spravovaných aplikací SaaS.

1. V [portál Azure](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Snímek obrazovky ikonu Azure Active Directory][1]

2. Přejděte do **podnikové aplikace, které** > **všechny aplikace**.

    ![Snímek obrazovky nástroje Azure Active Directory nabídky, podnikové aplikace a všechny aplikace, které se zvýrazněnou][2]
    
3. Chcete-li přidat novou aplikaci, vyberte **novou aplikaci** v horní části dialogových oken.

    ![Snímek obrazovky nová možnost aplikace v horní části dialogových oken][3]

4. Do vyhledávacího pole zadejte **Adobe přihlašovací**.

    ![Snímek obrazovky vyhledávacího pole](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Na panelu výsledků vyberte **Adobe přihlašovací**a potom vyberte **přidat**.

    ![Snímek obrazovky panelu výsledky](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování znakem Adobe podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí rozpoznat propojené vztah mezi uživatele Azure AD a související uživatelské v Adobe přihlášení.

Vytvoření propojené relace, ve Adobe přihlášení, přiřaďte mu hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno**.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování znakem Adobe, proveďte následující stavební bloky:

1. [Konfigurovat Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on) umožňující uživatelům používat tuto funkci.
2. [Vytvořit testovací uživatele Azure AD](#creating-an-azure-ad-test-user) k testování Azure AD jednotné přihlašování s Britta Simon.
3. [Vytvoření testovacího uživatele přihlášení Adobe](#creating-an-adobe-sign-test-user) tak, aby měl protějšek Britta Simon v Adobe znak, který je spojen s Azure AD reprezentace daného uživatele.
4. [Přiřadit testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user) povolit Britta Simon používat Azure AD jednotné přihlašování.
5. [Test jednotného přihlašování](#testing-single-sign-on) ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Adobe přihlášení.

1. Na portálu Azure na **Adobe přihlašovací** stránky integrace aplikací, vyberte **jednotného přihlašování**.

    ![Snímek obrazovky Adobe přihlašovací stránka integrace aplikace, s jednotné přihlašování zvýrazněná][4]

2. Na **jednotného přihlašování** dialogové okno, pro **režimu**, vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Snímek obrazovky jeden přihlašování dialogové okno, se zvýrazněnou pole režimu](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. V **Adobe přihlašovací domény a adresy URL** část, proveďte následující kroky:

    ![Snímek obrazovky Adobe přihlašovací domény a adresy URL v tématu](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL, která používá následující vzorec: `https://<companyname>.echosign.com/`

    b. V **identifikátor** textového pole zadejte adresu URL, která používá následující vzorec: `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) k získání těchto hodnot.

4. V **SAML podpisový certifikát** vyberte **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Snímek obrazovky z SAML certifikát pro podpis části](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Vyberte **Uložit**.

    ![Snímek obrazovky Uložit tlačítko](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. V **Adobe přihlašovací konfigurace** vyberte **nakonfigurovat přihlašovací Adobe** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out URL**, **SAML Entity ID**, a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka** části.

    ![Snímek obrazovky z Adobe přihlašovací konfigurační oddíl znakem nakonfigurovat Adobe zvýrazněná](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Před konfigurací, obraťte se [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) do seznamu povolených IP adres vaší doméně v přihlašovací Adobe. Tady je postup přidání domény:

    a. [Tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) vám pošle náhodně generované token. Pro vaši doménu, bude daný token takto: **adobe-sign ověření = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikovat tokenu ověření v záznamu DNS text a upozornit [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Tato akce může zabrat několik dnů nebo déle. Všimněte si, že zpoždění šíření DNS znamená, že hodnota publikovaná ve službě DNS, nemusí být viditelné pro hodinu nebo déle. Váš správce IT by měl být seznámeni o tom, jak publikovat tento token v záznamu DNS text.
    
    c. Při upozornění [tým podpory Adobe přihlašovací klienta](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory po publikování token se ověřit doménu a přidejte ji do vašeho účtu.
    
    d. Obecně platí Chcete-li publikovat token na záznam DNS:

    * Přihlaste se k účtu domény
    * Najděte stránku pro aktualizaci záznamu DNS. Tato stránka mohla mít název, správu DNS, Správa název serveru nebo Upřesnit nastavení.
    * Vyhledání záznamů TXT pro vaši doménu.
    * Přidejte záznam TXT s hodnotou úplné tokenu poskytl Adobe.
    * Uložte provedené změny.

8. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Adobe přihlášení jako správce.

9. V nabídce SAML vyberte **nastavení účtu** > **SAML nastavení**.
   
    ![Snímek obrazovky z Adobe přihlašování SAML nastavení stránky](./media/adobe-echosign-tutorial/ic789520.png "účtu")

10. V **SAML nastavení** část, proveďte následující kroky:
  
    ![Snímek obrazovky nastavení SAML](./media/adobe-echosign-tutorial/ic789521.png "nastavení SAML")
   
    a. V části **SAML režimu**, vyberte **SAML povinné**.
   
    b. Vyberte **povolit správci účtu Echosign přihlásit pomocí svých přihlašovacích údajů Echosign**.
   
    c. V části **vytvoření uživatele**, vyberte **automaticky přidat uživatele ověřeného pomocí SAML**.

    d. Vložení **SAML Entity ID**, který jste zkopírovali z portálu Azure do **Entity ID-Vystavitel URL** textové pole.
    
    e. Vložení **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure do **koncový bod adresy URL/jednotného přihlášení** textové pole.
   
    f. Vložení **Sign-Out URL**, který jste zkopírovali z portálu Azure do **koncový bod adresy URL/SLO odhlášení** textové pole.

    g. Otevřete váš stažené **Certificate(Base64)** souboru v poznámkovém bloku. Zkopírujte obsah ho do schránky a vložte jej do **IdP certifikát** textové pole.

    h. Vyberte **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele s názvem Britta Simon na portálu Azure.

![Snímek obrazovky testovací uživatelské jméno na portálu Azure][100]

1. V **portál Azure**, v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Snímek obrazovky ikonu Azure AD](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a vyberte **všichni uživatelé**.
    
    ![Snímek obrazovky Azure AD nabídek s použitím uživatelé a skupiny a všech uživatelů zvýrazněných](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, vyberte **přidat**.
 
    ![Snímek obrazovky začátku všechny dialogové okno Uživatelé, se zvýrazněnou možností přidat](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno pole, proveďte následující kroky:
 
    ![Dialogové okno snímek obrazovky uživatele](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. V **název** textového pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, zadejte e-mailovou adresu BrittaSimon.

    c. Vyberte **zobrazit hesla**a poznamenejte si hodnotu **heslo**.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-adobe-sign-test-user"></a>Vytvořit uživatele s Adobe přihlašovací testu

Povolit Azure AD přihlášení uživatelů k podepsání aplikace Adobe, musí být zřízená do Adobe přihlášení. Toto je ruční úloha.

>[!NOTE]
>Další nástroje pro tvorbu Adobe přihlášení uživatele účtu nebo rozhraní API poskytovaných Adobe přihlašovací můžete použít ke zřízení uživatelských účtů Azure AD. 

1. Přihlaste se k vaší **Adobe přihlašovací** společnosti lokality jako správce.

2. V nabídce nahoře vyberte **účet**. V levém podokně vyberte **uživatelé a skupiny** > **vytvořte nového uživatele**.
   
    ![Snímek obrazovky přihlašovací Adobe společnosti, lokality, pomocí účtu, uživatelé a skupiny a vytvořit nového uživatele zvýrazněná](./media/adobe-echosign-tutorial/ic789524.png "účtu")
   
3. V **vytvořit nového uživatele** část, proveďte následující kroky:
   
    ![Snímek obrazovky vytvoření nového uživatele části](./media/adobe-echosign-tutorial/ic789525.png "vytvořit uživatele")
   
    a. Typ **e-mailovou adresu**, **křestní jméno**, a **příjmení** platný Azure AD účet chcete zřídit do související textových polí.
   
    b. Vyberte **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mail, který obsahuje odkaz pro potvrzení účtu, pak se změní na aktivní. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování, tak, že udělíte přístup na Adobe přihlašování.

![Snímek obrazovky Azure portálu jednotné přihlašování][200] 

1. Na portálu Azure otevřete zobrazení aplikace. Pak přejděte do zobrazení adresáře, přejděte na **podnikové aplikace, které**a vyberte **všechny aplikace**.

    ![Zobrazení portálu aplikace – snímek obrazovky Azure, s podnikové aplikace a všechny aplikace, které se zvýrazněnou][201] 

2. V seznamu aplikací vyberte **Adobe přihlašovací**.

    ![Snímek obrazovky seznam aplikací, znakem Adobe zvýrazněná](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Snímek obrazovky nabídky s uživateli a skupinami zvýrazněná][202] 

4. Vyberte **Přidat**. Potom v **přidat přiřazení** vyberte **uživatelů a skupin**.

    ![Snímek obrazovky uživatelů a skupin stránku a přiřazení přidat oddíl][203]

5. V **uživatelů a skupin** dialogové okno, v seznamu uživatelů vyberte **Britta Simon**.

6. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte**.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit**.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Když vyberete dlaždici Adobe přihlašovací na přístupovém panelu, můžete by měl získat automaticky přihlášeni do vaší aplikace Adobe přihlášení. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
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
