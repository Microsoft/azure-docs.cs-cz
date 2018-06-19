---
title: 'Kurz: Integrace Azure Active Directory s tokenu SAML 1.1 obchodní aplikace s povoleným | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tokenu SAML 1.1 obchodní aplikace s povoleným.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ced1d88d-0e48-40d5-9aea-ef991cd9d270
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeedes
ms.openlocfilehash: c5a806bb586ceeb4ce68774a1b5d874dfc1e4f26
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35898967"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-11-token-enabled-lob-app"></a>Kurz: Obchodní aplikace s povoleným integrace Azure Active Directory s tokenu SAML 1.1

V tomto kurzu zjistíte, jak integrovat SAML 1.1 tokenu aplikace s povoleným LOB s Azure Active Directory (Azure AD).

Integrace tokenu SAML 1.1 povolená obchodní aplikace s Azure AD poskytuje následující výhody:

- Můžete řídit v Azure AD, který má přístup k tokenu SAML 1.1 obchodní aplikace s povoleným.
- Můžete povolit uživatelům automaticky přihlášení k získání tokenu SAML 1.1 povoleny obchodní aplikace (jednotné přihlášení) v rámci své účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci Azure obchodní aplikace s povoleným integrace služby AD s tokenu SAML 1.1, potřebujete následující položky:

- Předplatné služby Azure AD
- SAML 1.1 Token povolené aplikace LOB jedné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tokenu SAML 1.1 s podporou obchodní aplikaci z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-saml-11-token-enabled-lob-app-from-the-gallery"></a>Přidání tokenu SAML 1.1 s podporou obchodní aplikaci z Galerie
Konfigurace integrace tokenu SAML 1.1 povolena obchodní aplikace do služby Azure AD, je nutné přidat SAML 1.1 Token povoleným obchodní aplikaci z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat že token SAML 1.1 obchodní aplikace z galerie s povoleným, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **tokenu SAML 1.1 obchodní aplikace s povoleným**, vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SAML 1.1 Token povolena obchodní aplikace v seznamu výsledků](./media/saml-tutorial/tutorial_saml_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části konfiguraci a testování Azure AD jednotné přihlašování s SAML 1.1 tokenu povolená obchodní aplikace podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v SAML 1.1 tokenu je povolená obchodní aplikace pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v tokenu SAML 1.1 povoleno obchodní aplikace musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s tokenu SAML 1.1 obchodní aplikace s povoleným, je potřeba provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit SAML 1.1 Token povolena obchodní aplikace testovacího uživatele](#create-a-saml-11-token-enabled-lob-app-test-user)**  – Pokud chcete mít obchodní aplikace, propojené služby Azure AD reprezentace uživatele s povoleným protějšek Britta Simon v tokenu SAML 1.1.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování ve vašem tokenu 1.1 SAML aplikace obchodní aplikace s podporou.

**Ke konfiguraci Azure AD jednotné přihlašování s tokenu SAML 1.1 povolené obchodní aplikace, proveďte následující kroky:**

1. Na portálu Azure na **tokenu SAML 1.1 obchodní aplikace s povoleným** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/saml-tutorial/tutorial_saml_samlbase.png)

3. Na **tokenu SAML 1.1 povolit obchodní aplikace domény a adresy URL** část, proveďte následující kroky:

    ![SAML 1.1 Token povoleno obchodní aplikace adresy URL jeden přihlašování informace o doméně a](./media/saml-tutorial/tutorial_saml_url.png)

    a. V **přihlásit na adrese URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://your-app-url`

    b. V **identifikátoru (Entity ID)** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://your-app-url`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty prosím nahraďte konkrétní adresy URL aplikace.  

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/saml-tutorial/tutorial_saml_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/saml-tutorial/tutorial_general_400.png)
    
7. Na **tokenu SAML 1.1 povoleno konfigurace aplikací LOB** klikněte na tlačítko **konfigurace SAML 1.1 na Token obchodní aplikace s povoleným** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![SAML 1.1 Token povoleno obchodní konfigurace aplikací](./media/saml-tutorial/tutorial_saml_configure.png) 

8. Konfigurace jednotného přihlašování na **tokenu SAML 1.1 obchodní aplikace s povoleným** straně, budete muset odeslat stažené **certifikát (Base64), adresu URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** na tým podpory aplikace. Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/saml-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/saml-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/saml-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/saml-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-saml-11-token-enabled-lob-app-test-user"></a>Vytvořit SAML 1.1 Token povolena obchodní aplikace testovacího uživatele

V této části vytvoříte uživatele volal Britta Simon v tokenu SAML 1.1 obchodní aplikace s povoleným. Práce s aplikací podporují týmu a vytvořit uživatele na straně aplikace. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování tak, že udělíte přístup k tokenu SAML 1.1 obchodní aplikace s povoleným.

![Přiřadit role uživatele][200] 

**Přiřadit Britta Simon tokenu SAML 1.1 obchodní aplikace s povoleným, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **tokenu SAML 1.1 obchodní aplikace s povoleným**.

    ![SAML 1.1 Token odkaz obchodní aplikaci v seznamu aplikací povoleno](./media/saml-tutorial/tutorial_saml_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko tokenu 1.1 SAML povolené dlaždici obchodní aplikace na přístupovém panelu, měli byste obdržet automaticky přihlášení k vaší SAML 1.1 tokenu aplikace obchodní aplikace s podporou.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
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

