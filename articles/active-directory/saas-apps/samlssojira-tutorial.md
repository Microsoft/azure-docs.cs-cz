---
title: 'Kurz: Integrace Azure Active Directory pomocí jednotného přihlašování SAML pro Jira podle řešení GmbH | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro Jira podle řešení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 4885863519ab2ba0a37a0f14ccab12bbfc35a03a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225265"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory pomocí jednotného přihlašování SAML pro Jira podle řešení GmbH

V tomto kurzu zjistěte, jak integrovat jednotné přihlašování SAML pro Jira podle řešení GmbH službou Azure Active Directory (Azure AD).

Integrace jednotné přihlašování SAML pro Jira podle řešení GmbH s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k jednotné přihlašování SAML pro Jira podle řešení GmbH
- Můžete povolit uživatelům, aby automaticky získat přihlášení k jednotné přihlašování SAML pro Jira podle řešení GmbH (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí jednotného přihlašování SAML pro Jira pomocí řešení GmbH, potřebujete následující položky:

- Předplatné služby Azure AD
- Jednotné přihlašování SAML pro Jira podle řešení GmbH jednotné přihlašování v předplatném povolené

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání jednotné přihlašování SAML pro Jira podle řešení GmbH z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotné přihlašování SAML pro Jira podle řešení GmbH z Galerie
Při konfiguraci integrace jednotné přihlašování SAML pro Jira podle řešení GmbH do služby Azure AD, musíte přidat jednotné přihlašování SAML pro Jira řešení GmbH z Galerie si na seznam spravovaných aplikací SaaS.

**Přidání jednotné přihlašování SAML pro Jira řešení GmbH z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro Jira podle řešení GmbH**.

    ![Vytváření testovacího uživatele Azure AD](./media/samlssojira-tutorial/tutorial_samlssojira_search.png)

5. Na panelu výsledků vyberte **jednotné přihlašování SAML pro Jira podle řešení GmbH**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML pro Jira pomocí řešení, které GmbH podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, jaké příslušného uživatele v jednotné přihlašování SAML pro Jira pomocí řešení GmbH je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v jednotné přihlašování SAML pro Jira pomocí řešení GmbH musí být vytvořeno.

V jednotné přihlašování SAML pro Jira podle řešení GmbH, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML pro Jira podle řešení GmbH, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření jednotné přihlašování SAML pro Jira uživatelem testovací GmbH řešení](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML pro Jira podle řešení GmbH propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší jednotné přihlašování SAML pro Jira podle řešení GmbH aplikace.

**Konfigurace Azure AD jednotné přihlašování pomocí jednotného přihlašování SAML pro Jira pomocí řešení GmbH, proveďte následující kroky:**

1. Na portálu Azure na **jednotné přihlašování SAML pro Jira podle řešení GmbH** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. Na **jednotné přihlašování SAML Jira podle řešení GmbH domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory jednotné přihlašování SAML pro Jira podle řešení GmbH klienta](https://www.resolution.de/go/support) k získání těchto hodnot. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web, přihlaste se k vaší **jednotné přihlašování SAML pro Jira pomocí portálu pro správu GmbH řešení** jako správce.

8. Pozastavte ukazatel myši na ikonu a klikněte na **doplňky**.
    
    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon1.png)

9. Budete přesměrováni na stránku přístup správce. Zadejte **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon2.png)

10. Karta části doplňky, klikněte na tlačítko **najít nové rozšíření**. Hledání **SAML jednotné přihlašování na (SSO) pro JIRA** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon7.png)

11. Spustí se instalace modulu plug-in. Klikněte na **Zavřít**.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon8.png)

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon9.png)

12. Klikněte na **Manage** (Spravovat).

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon10.png)
    
13. Klikněte na tlačítko **konfigurace** konfigurace nového modulu plug-in.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon11.png)

14. Na **konfigurace modulu plug-in SingleSignOn SAML** klikněte na tlačítko **přidat nové rozšíření IdP** tlačítko ke konfiguraci nastavení zprostředkovatele Identity.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon4.png)

15. Na **zvolte poskytovatele Identity SAML** proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon5a.png)
 
    a. Nastavit **Azure AD** jako typ deklarací identity.
    
    b. Přidat **název** zprostředkovatele Identity (např. Azure AD).
    
    c. Přidat **popis** zprostředkovatele Identity (např. Azure AD).
    
    d. Klikněte na **Další**.
    
16. Na **konfigurace zprostředkovatele Identity** klikněte na tlačítko **Další** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon5b.png)

17. Na **importovat IdP Metadata SAML** proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon5c.png)

    a. Klikněte na tlačítko **načíst soubor** tlačítko a vyberte soubor XML s metadaty souboru, které jste si stáhli v kroku 5.

    b. Klikněte na tlačítko **Import** tlačítko.
    
    c. Počkejte, než stručně dokud úspěšně importovat.
    
    d. Klikněte na tlačítko **Další** tlačítko.
    
18. Na **atribut ID uživatele a transformace** klikněte na tlačítko **Další** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon5d.png)
    
19. Na **vytvoření uživatele a aktualizace** klikněte na tlačítko **Uložit & Další** se uložit nastavení.   
    
    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon6a.png)
    
20. Na **Test nastavení** klikněte na tlačítko **přeskočit testovací & Ruční konfigurace** testovací uživatele pro tuto chvíli přeskočit. To se provede v další části a vyžaduje některá nastavení v portálu Azure. 
    
    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon6b.png)
    
21. V dialogu čtení apprearing **přeskočení znamená testovací...** , klikněte na tlačítko **OK**.
    
    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/addon6c.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/samlssojira-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/samlssojira-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/samlssojira-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/samlssojira-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Vytváření jednotné přihlašování SAML pro Jira řešení GmbH testovací uživatel

Pokud chcete povolit uživatelům Azure AD přihlášení pro jednotné přihlašování SAML pro Jira řešení GmbH, se musí být zřízená do jednotné přihlašování SAML pro Jira podle řešení GmbH.  
V jednotné přihlašování SAML pro Jira podle řešení GmbH zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší SAML jednotného přihlašování pro Jira řešení GmbH společnosti lokalita jako správce.

2. Pozastavte ukazatel myši na ikonu a klikněte na **Správa uživatelů**.

    ![Můžete přidat zaměstnance](./media/samlssojira-tutorial/user1.png) 

3. Budete přesměrováni na stránku přístup správce k zadání **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Můžete přidat zaměstnance](./media/samlssojira-tutorial/user2.png) 

4. V části **Správa uživatelů** oddíl, klikněte na **vytvořit uživatele**.

    ![Můžete přidat zaměstnance](./media/samlssojira-tutorial/user3.png) 

5. Na **"Vytvořit nový uživatel"** dialogové okno proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/samlssojira-tutorial/user4.png) 

    a. V **e-mailová adresa** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.

    b. V **úplný název** textovému poli, úplný název typu uživatele jako Britta Simon.

    c. V **uživatelské jméno** jako typ e-mailu uživatele k textovému poli, Brittasimon@contoso.com.

    d. V **heslo** textovému poli, zadejte heslo uživatele.

    e. Klikněte na tlačítko **vytvořit uživateli**.   

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí jednotného přihlašování SAML pro Jira udělení přístupu podle řešení GmbH.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon jednotné přihlašování SAML pro Jira podle řešení GmbH, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **jednotné přihlašování SAML pro Jira podle řešení GmbH**.

    ![Konfigurovat jednotné přihlašování](./media/samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko jednotné přihlašování SAML pro Jira pomocí dlaždice GmbH řešení na přístupovém panelu jste měli získat automaticky přihlášení k vaší jednotné přihlašování SAML pro Jira podle řešení GmbH aplikace.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/samlssojira-tutorial/tutorial_general_203.png

