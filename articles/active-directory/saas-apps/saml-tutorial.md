---
title: 'Kurz: Integrace Azure Active Directory pomocí tokenu SAML 1.1 obchodní aplikace s povoleným | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAML 1.1 Token obchodní aplikace s povolenou.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: dc07dce24152f9f58253ad96c80f6b004cd1198b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050779"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Kurz: Integrace Azure Active Directory pomocí tokenu SAML 1.1 obchodní aplikace s povoleným

V tomto kurzu se dozvíte, jak integrovat SAML 1.1 Token aplikace s povoleným LOB s Azure Active Directory (Azure AD).

Integrace tokenu SAML 1.1 obchodní aplikace s Azure AD poskytuje následující výhody:

- Můžete řídit v Azure AD, který má přístup k tokenu SAML 1.1 obchodní aplikace s povolenou.
- Můžete povolit uživatelům, aby automaticky přihlášení k získání tokenu SAML 1.1 povolené obchodní aplikace (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci Azure obchodní aplikace s povoleným integrace AD se SAML 1.1 Token, potřebujete následující položky:

- S předplatným služby Azure AD
- SAML 1.1 Token povolené obchodní aplikaci pro jedno přihlášení povolený předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tokenu SAML 1.1 povolené obchodní aplikaci z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Přidání tokenu SAML 1.1 povolené obchodní aplikaci z Galerie
Chcete-li konfigurovat integraci SAML 1.1 Token povolena obchodní aplikaci do služby Azure AD, je třeba přidat SAML 1.1 Token povolené obchodní aplikaci z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SAML 1.1 Token povolené obchodní aplikaci z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **tokenu SAML 1.1 obchodní aplikace s povoleným**vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![SAML 1.1 Token povolené obchodní aplikaci v seznamu výsledků](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SAML 1.1 Token povoleno obchodní aplikaci na základě testu uživatelem nazývá "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v SAML 1.1 tokenu je povoleno obchodní aplikaci pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v tokenu SAML 1.1 povolené obchodní aplikaci pro je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí SAML 1.1 Token obchodní aplikace s povolenou, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření SAML 1.1 Token povolené obchodní aplikaci pro testovacího uživatele](#create-a-saml-11-token-enabled-lob-app-test-user)**  – Pokud chcete mít obchodní aplikaci, který je propojený s Azure AD reprezentace uživatele povoleno protějšek Britta Simon v tokenu SAML 1.1.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal a nakonfigurovat jednotné přihlašování ve vašich SAML 1.1 Token obchodní aplikaci pro aplikace s podporou.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí SAML 1.1 Token povolena obchodní aplikaci, proveďte následující kroky:**

1. Na webu Azure Portal na **tokenu SAML 1.1 obchodní aplikace s povoleným** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/saml-tutorial/tutorial_saml_samlbase.png)

3. Na **tokenu SAML 1.1 povolené obchodní domény aplikace a adresy URL** části, proveďte následující kroky:

    ![SAML 1.1 Token povolené obchodní domény aplikace a adresy URL jednotné přihlašování – informace](./media/saml-tutorial/tutorial_saml_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://your-app-url`

    b. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://your-app-url`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Nahraďte tyto hodnoty adresy URL pro konkrétní aplikace.  

5. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/saml-tutorial/tutorial_saml_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/saml-tutorial/tutorial_general_400.png)
    
7. Na **tokenu SAML 1.1 povolená konfigurace aplikace LOB** klikněte na tlačítko **konfigurace SAML 1.1 na Token obchodní aplikace s povolenou** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![SAML 1.1 Token konfiguraci povolených obchodní aplikace](./media/saml-tutorial/tutorial_saml_configure.png) 

8. Ke konfiguraci jednotného přihlašování na **tokenu SAML 1.1 obchodní aplikace s povoleným** straně, je nutné odeslat na stažený **certifikát (Base64), URL odhlašování, SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** do aplikace na tým podpory. Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/saml-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/saml-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/saml-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/saml-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Vytvoření SAML 1.1 Token povolené obchodní aplikaci pro testovacího uživatele

V této části, vytvořte uživatele Britta Simon tokenu SAML 1.1 obchodní aplikace s povolenou. Práce s aplikací podporují týmu a vytvořte uživatele na straně aplikace. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte Britta Simon do Azure můžete používat jednotné přihlašování tak, že udělíte přístup k tokenu SAML 1.1 obchodní aplikace s povolenou.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon tokenu SAML 1.1 obchodní aplikace s povolenou, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným**.

    ![Tokenu SAML 1.1 obchodní aplikaci pro odkaz v seznamu aplikací povoleno](./media/saml-tutorial/tutorial_saml_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na SAML 1.1 Token povolena obchodní aplikaci pro dlaždice na přístupovém panelu, by vám měl automaticky přihlášení k vaší SAML 1.1 Token obchodní aplikaci pro aplikace s podporou.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saml-tutorial/tutorial_general_01.png
[2]: ./media/saml-tutorial/tutorial_general_02.png
[3]: ./media/saml-tutorial/tutorial_general_03.png
[4]: ./media/saml-tutorial/tutorial_general_04.png

[100]: ./media/saml-tutorial/tutorial_general_100.png

[200]: ./media/saml-tutorial/tutorial_general_200.png
[201]: ./media/saml-tutorial/tutorial_general_201.png
[202]: ./media/saml-tutorial/tutorial_general_202.png
[203]: ./media/saml-tutorial/tutorial_general_203.png
