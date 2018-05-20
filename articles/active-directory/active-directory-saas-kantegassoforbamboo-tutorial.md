---
title: 'Kurz: Azure Active Directory integrace s Kantega jednotné přihlašování pro bambus | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kantega jednotné přihlašování pro bambus.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 67d2f6e649ff01ec5a4e2ab8c924c764e65c8a5d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Kurz: Azure Active Directory integrace s Kantega jednotné přihlašování pro bambus

V tomto kurzu zjistěte, jak integrovat Kantega jednotné přihlašování pro bambus s Azure Active Directory (Azure AD).

Integrace Kantega jednotné přihlašování pro bambus s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k Kantega jednotné přihlašování pro bambus
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Kantega jednotné přihlašování pro bambus (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí jednotného přihlašování Kantega pro bambus, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené Kantega SSO pro bambus jednotné přihlašování

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kantega jednotné přihlašování pro bambus z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Přidání Kantega jednotné přihlašování pro bambus z Galerie
Při konfiguraci integrace Kantega přihlašování pro bambus do služby Azure AD, potřebujete přidat Kantega jednotné přihlašování pro bambus z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Kantega jednotné přihlašování pro bambus z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Kantega jednotné přihlašování pro bambus**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_search.png)

5. Na panelu výsledků vyberte **Kantega jednotné přihlašování pro bambus**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí jednotného přihlašování Kantega pro bambus podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku Kantega SSO pro bambus je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Kantega SSO pro bambus musí navázat.

V Kantega jednotné přihlašování pro bambus, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování Kantega pro bambus, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření Kantega SSO pro testovací uživatele bambus](#creating-a-kantega-sso-for-bamboo-test-user)**  – Pokud chcete mít protějšek Britta Simon Kantega SSO pro bambus propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší Kantega jednotné přihlašování pro aplikace bambus.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí jednotného přihlašování Kantega pro bambus, proveďte následující kroky:**

1. Na portálu Azure na **Kantega jednotné přihlašování pro bambus** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_samlbase.png)

3. V **IDP** spustil v režimu **Kantega SSO bambus domény a adresy URL** části provést následující krok:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url1.png)
    
    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. V **SP** initiated režimu, zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_url2.png)
    
    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Tyto hodnoty jsou přijímány během konfigurace bambus modul plug-in, který je vysvětlen později v tomto kurzu.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web Přihlaste se k vaší bambus na místním serveru jako správce.

8. Pozastavte ukazatel myši na ikonu a klikněte na **doplňky**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon1.png)

9. Karta části doplňky, klikněte na tlačítko **najít nové rozšíření**. Hledání **Kantega jednotné přihlašování pro bambus (SAML & protokolu Kerberos)** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon2.png)

10. Spustí se instalace modulu plug-in.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon21.png)

11. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon33.png)

12. Klikněte na **Manage** (Spravovat).

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon34.png)
    
13. Klikněte na tlačítko **konfigurace** konfigurace nového modulu plug-in.    

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon3.png)

14. V **SAML** části. Vyberte **Azure Active Directory (Azure AD)** z **zprostředkovatele identity přidat** rozevíracího seznamu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon4.png)

15. Vyberte úroveň předplatné jako **základní**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon5.png)

16. Na **vlastností aplikace** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon6.png)

    a. Kopírování **identifikátor ID URI aplikace** hodnotu a použít ho jako **identifikátor, adresa URL odpovědi a přihlašovací adresa URL** na **Kantega SSO bambus domény a adresy URL** části na portálu Azure.

    b. Klikněte na **Další**.

17. Na **import metadat** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon7.png)

    a. Vyberte **soubor metadat v mém počítači**a metadata souboru k odeslání, který jste si stáhli z portálu Azure.

    b. Klikněte na **Další**.

18. Na **název a jednotného přihlašování k umístění** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon8.png)

    a. Přidejte název poskytovatele Identity **název zprostředkovatele Identity** textbox (např. Azure AD).

    b. Klikněte na **Další**.

19. Ověřte podpisového certifikátu a klikněte na **Další**.  

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon9.png)

20. Na **bambus uživatelské účty** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon10.png)

    a. Vyberte **v případě potřeby vytvořte uživatele v adresáři společnosti bambus interní** a zadejte odpovídající název skupiny pro uživatele (může být více ne. skupin oddělené čárkou).

    b. Klikněte na **Další**.

21. Klikněte na **Dokončit**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon11.png)

22. Na **známé domén pro Azure AD** část, proveďte následující kroky: 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/addon12.png)

    a. Vyberte **známé domén** v levém panelu stránky.

    b. Zadejte název domény v **známé domén** textové pole.

    c. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-kantegassoforbamboo-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kantega-sso-for-bamboo-test-user"></a>Vytváření Kantega SSO pro bambus testovacího uživatele

Pokud chcete povolit uživatelům Azure AD přihlášení do bambus, musí být zřízená do bambus. V Kantega jednotné přihlašování pro bambus zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší bambus na místním serveru jako správce.

2. Pozastavte ukazatel myši na ikonu a klikněte na **Správa uživatelů**.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-kantegassoforbamboo-tutorial/user1.png) 

3. Klikněte na tlačítko **uživatelé**. V části **přidat uživatele** část, udělejte toto:

    ![Můžete přidat zaměstnance](./media/active-directory-saas-kantegassoforbamboo-tutorial/user2.png) 

    a. V **uživatelské jméno** jako typ e-mailu uživatele k textovému poli, Brittasimon@contoso.com.
    
    b. V **heslo** textovému poli, zadejte heslo uživatele.

    c. V **Potvrdit heslo** textovému poli, znovu zadejte heslo uživatele.
    
    d. V **úplný název** textovému poli, úplný název typu uživatele jako Britta Simon.
    
    e. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.
    
    f. Klikněte na **Uložit**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí jednotného přihlašování k Kantega pro bambus udělení přístupu.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Kantega jednotné přihlašování pro bambus, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Kantega jednotné přihlašování pro bambus**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_kantegassoforbamboo_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko Kantega jednotné přihlašování pro dlaždici bambus na přístupovém panelu, můžete by měl získat automaticky přihlášení k vaší Kantega jednotné přihlašování pro aplikace bambus.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforbamboo-tutorial/tutorial_general_203.png

