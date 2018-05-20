---
title: 'Kurz: Azure Active Directory integrace s QuickHelp | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a QuickHelp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 655c9ad3-2076-4e2c-8e47-9ed3bf04be56
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: jeedes
ms.openlocfilehash: 9eaa9032a60b6b2906034df10d6eef8671255483
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Kurz: Azure Active Directory integrace s QuickHelp

V tomto kurzu zjistěte, jak integrovat QuickHelp s Azure Active Directory (Azure AD).

Integrace QuickHelp s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k QuickHelp
- Můžete povolit uživatelům, aby automaticky získat přihlášení k QuickHelp (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s QuickHelp, potřebujete následující položky:

- Předplatné služby Azure AD
- QuickHelp jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání QuickHelp z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-quickhelp-from-the-gallery"></a>Přidání QuickHelp z Galerie
Při konfiguraci integrace QuickHelp do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS QuickHelp z galerie.

**Pokud chcete přidat QuickHelp z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **QuickHelp**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_search.png)

5. Na panelu výsledků vyberte **QuickHelp**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s QuickHelp podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v QuickHelp je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v QuickHelp musí navázat.

V QuickHelp, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s QuickHelp, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele QuickHelp](#creating-a-quickhelp-test-user)**  – Pokud chcete mít protějšek Britta Simon v QuickHelp propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci QuickHelp.

**Ke konfiguraci Azure AD jednotné přihlašování s QuickHelp, proveďte následující kroky:**

1. Na portálu Azure na **QuickHelp** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_samlbase.png)

3. Na **QuickHelp domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://quickhelp.com/<ROUTEURL>`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://auth.quickhelp.com`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. Aktualizujte hodnotu s skutečná adresa URL přihlašování. Obraťte se na správce QuickHelp ve vaší organizaci nebo váš správce klienta úspěch Debata k získání hodnoty.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-quickhelp-tutorial/tutorial_general_400.png) 

6. Přihlašování k webu společnosti QuickHelp jako správce.

7. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Konfigurovat jednotné přihlašování][21]

8. V **QuickHelp správce** nabídky, klikněte na tlačítko **nastavení**.
   
    ![Konfigurovat jednotné přihlašování][22]

9. Klikněte na tlačítko **nastavení ověřování**.

10. Na **nastavení ověřování** proveďte následující kroky
   
    ![Konfigurovat jednotné přihlašování][23]
   
    a. Jako **typ jednotného přihlašování k**, vyberte **WSFederation**.
   
    b. Chcete-li nahrát soubor stažený Azure metadata, klikněte na tlačítko **Procházet**, přejděte na soubor, pak klikněte na tlačítko Ukončit **nahrát Metadata**.
   
    c. V **e-mailu** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
   
    d. V **křestní jméno** textovému poli, `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
   
    e. V **příjmení** textovému poli, `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
   
    f. V **panelu akcí**, klikněte na tlačítko **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-quickhelp-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-quickhelp-test-user"></a>Vytvoření zkušebního uživatele QuickHelp

Cílem této části je vytvoření uživatele v QuickHelp nazývá Britta Simon.
Pro jednotné přihlašování pro práci je potřeba vědět, co uživatel protějškem v QuickHelp pro uživatele ve službě Azure AD je Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v QuickHelp musí navázat.

QuickHelp podporuje zřizování za běhu. To znamená, v případě potřeby, uživatelský účet je automaticky vytvořen ve QuickHelp a účet je propojený s účtem Azure AD.

Neexistuje žádná položka akce pro vás v této části.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu QuickHelp.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon QuickHelp, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **QuickHelp**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.  

Když kliknete na dlaždici QuickHelp na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci QuickHelp.


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_general_203.png
[21]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_05.png
[22]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_06.png
[23]: ./media/active-directory-saas-quickhelp-tutorial/tutorial_quickhelp_07.png
