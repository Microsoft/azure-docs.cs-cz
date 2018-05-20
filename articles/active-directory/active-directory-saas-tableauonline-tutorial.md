---
title: 'Kurz: Azure Active Directory integrace s Tableau Online | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 4747c2146c6590e30da2ba8985ecdfbd6a75ae96
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Kurz: Azure Active Directory integrace s Tableau Online

V tomto kurzu zjistěte, jak integrovat Tableau Online s Azure Active Directory (Azure AD).

Integrace Tableau Online s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Tableau Online
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Tableau Online (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tableau Online, potřebujete následující položky:

- Předplatné služby Azure AD
- Tableau Online jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Tableau Online z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-tableau-online-from-the-gallery"></a>Přidání Tableau Online z Galerie
Při konfiguraci integrace služby Tableau Online do služby Azure AD, musíte přidat Tableau Online z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Tableau Online z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tableau Online**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. Na panelu výsledků vyberte **Tableau Online**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Online na základě testovací uživatele, nazývá "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Tableau Online je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah propojení mezi uživatele Azure AD a související uživatelské v Tableau Online.

V Tableau Online přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Online, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Tableau Online](#creating-a-tableau-online-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tableau Online propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Tableau Online.

**Ke konfiguraci Azure AD jednotné přihlašování s Tableau Online, proveďte následující kroky:**

1. Na portálu Azure na **Tableau Online** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. Na **Tableau Online domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://sso.online.tableau.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. V okně jiný prohlížeč přihlášení do aplikace Tableau Online. Přejděte na **nastavení** a potom **ověřování**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Chcete-li povolit SAML, v části **typy ověřování** části. Zkontrolujte **jednotné přihlašování s SAML** zaškrtávací políčko.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Posuňte se dolů, dokud **soubor Import metadat do Tableau Online** části.  Klikněte na tlačítko Procházet a importovat soubor metadat, které jste si stáhli z Azure AD. Potom klikněte na **použít**.
   
   ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. V **odpovídat kontrolní výrazy** část, vložte s odpovídajícím názvem assertion zprostředkovatele Identity pro **e-mailová adresa**, **křestní jméno**, a **příjmení**. Pokud chcete získat tyto informace z Azure AD: 
  
    a. Na portálu Azure přejděte **Tableau Online** stránky integrace aplikace.
    
    b. V části atributy, vyberte **"Zobrazit a upravit všechny ostatní atributy uživatele"** zaškrtávací políčko. 
    
   ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Zkopírujte hodnotu oboru názvů pro tyto atributy: givenname, e-mailu a Přezdívka pomocí následujících kroků:

   ![Azure AD jednotné přihlášení](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Klikněte na tlačítko **user.givenname** hodnota 
    
    e. Zkopírujte hodnotu z **obor názvů** textové pole.

   ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Kopírování namesapce hodnoty pro e-mailu a příjmení, postupujte podle předchozích kroků.

    g. Přepněte do Tableau Online aplikace a pak nastavte **Tableau Online atributy** části následujícím způsobem:
     * E-mailu: **e-mailu** nebo **userprincipalname**
     * Křestní jméno: **givenname**
     * Příjmení: **Přezdívka**
   
   ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-tableau-online-test-user"></a>Vytvoření Tableau Online zkušebního uživatele

V této části vytvoříte uživatele volal Britta Simon v Tableau Online.

1. Na **Tableau Online**, klikněte na tlačítko **nastavení** a potom **ověřování** části. Přejděte dolů k položce **vybrat uživatele** části. Klikněte na tlačítko **přidat uživatele** a potom **zadejte e-mailové adresy**.
   
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Vyberte **přidat uživatele pro jednotné přihlašování (SSO) ověřování**. V **zadejte e-mailové adresy** přidat textové pole britta.simon@contoso.com
   
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Klikněte na možnost **Vytvořit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon tak, že udělíte přístup k Tableau Online používat Azure jednotné přihlašování.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Tableau Online, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tableau Online**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici Tableau Online na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Tableau Online.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
