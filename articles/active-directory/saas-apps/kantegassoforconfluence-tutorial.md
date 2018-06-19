---
title: 'Kurz: Azure Active Directory integrace s Kantega jednotné přihlašování pro soutoku | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kantega jednotné přihlašování pro soutoku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: c6a4f0f21510f2c1affcbfc2968a5e3eca372348
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899120"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Kurz: Azure Active Directory integrace s Kantega jednotné přihlašování pro soutoku

V tomto kurzu zjistěte, jak integrovat Kantega jednotné přihlašování pro soutoku s Azure Active Directory (Azure AD).

Integrace Kantega jednotné přihlašování pro soutoku s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, kdo má přístup k Kantega jednotné přihlašování pro soutoku
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Kantega jednotné přihlašování pro soutoku (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí jednotného přihlašování Kantega pro soutoku, potřebujete následující položky:

- Předplatné služby Azure AD
- Předplatné povolené Kantega SSO pro soutoku jednotné přihlašování

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kantega jednotné přihlašování pro soutoku z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Přidání Kantega jednotné přihlašování pro soutoku z Galerie
Při konfiguraci integrace Kantega přihlašování pro soutoku do služby Azure AD, potřebujete přidat Kantega jednotné přihlašování pro soutoku z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Kantega jednotné přihlašování pro soutoku z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Kantega jednotné přihlašování pro soutoku**.

    ![Vytváření testovacího uživatele Azure AD](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_search.png)

5. Na panelu výsledků vyberte **Kantega jednotné přihlašování pro soutoku**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí jednotného přihlašování Kantega pro soutoku podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějšku Kantega SSO pro soutoku je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské Kantega SSO pro soutoku musí navázat.

V Kantega jednotné přihlašování pro soutoku, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí jednotného přihlašování Kantega pro soutoku, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření Kantega SSO pro testovací uživatele soutoku](#creating-a-kantega-sso-for-confluence-test-user)**  – Pokud chcete mít protějšek Britta Simon Kantega SSO pro soutoku propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší Kantega jednotné přihlašování pro aplikace soutoku.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí jednotného přihlašování Kantega pro soutoku, proveďte následující kroky:**

1. Na portálu Azure na **Kantega jednotné přihlašování pro soutoku** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_samlbase.png)

3. V **IDP** spustil v režimu **Kantega SSO soutoku domény a adresy URL** části provést následující krok:

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url1.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. V **SP** initiated režimu, zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok:

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_url2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in soutoku, který je vysvětlen později v tomto kurzu.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web, přihlaste se k vaší **portál pro správu soutoku** jako správce.

8. Pozastavte ukazatel myši na ikonu a klikněte na **doplňky**.
    
    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon1.png)

9. V části **ATLASSIAN MARKETPLACE** , klikněte na **najít nové rozšíření**. 

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon.png)

10. Hledání **Kantega jednotné přihlašování pro protokol Kerberos SAML soutoku** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon2.png)

11. Spustí se instalace modulu plug-in.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon3.png)

12. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon33.png)

13. Klikněte na **Manage** (Spravovat).

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon34.png)
    
14. Klikněte na tlačítko **konfigurace** konfigurace nového modulu plug-in.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon35.png)

15. Tento nový modul plug-in můžete také najít v části **uživatelů a zabezpečení** kartě.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon36.png)
    
16. V **SAML** části. Vyberte **Azure Active Directory (Azure AD)** z **zprostředkovatele identity přidat** rozevíracího seznamu.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon4.png)

17. Vyberte úroveň předplatné jako **základní**.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon5.png)     

18. Na **vlastností aplikace** část, proveďte následující kroky: 

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopírování **identifikátor ID URI aplikace** hodnotu a použít ho jako **identifikátor, adresa URL odpovědi a přihlašovací adresa URL** na **Kantega SSO soutoku domény a adresy URL** části na portálu Azure.

    b. Klikněte na **Další**.

19. Na **import metadat** část, proveďte následující kroky: 

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Vyberte **soubor metadat v mém počítači**a metadata souboru k odeslání, který jste si stáhli z portálu Azure.

    b. Klikněte na **Další**.

20. Na **název a jednotného přihlašování k umístění** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon8.png)
    
    a. Přidejte název poskytovatele Identity **název zprostředkovatele Identity** textbox (např. Azure AD).

    b. Klikněte na **Další**.

21. Ověřte podpisového certifikátu a klikněte na **Další**.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon9.png)

22. Na **soutoku uživatelské účty** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Vyberte **v případě potřeby vytvořte uživatele v adresáři interní na soutoku** a zadejte odpovídající název skupiny pro uživatele (může být více ne. skupin oddělené čárkou).

    b. Klikněte na **Další**.

23. Klikněte na **Dokončit**.   

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon11.png)

24. Na **známé domén pro Azure AD** část, proveďte následující kroky: 

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Vyberte **známé domén** v levém panelu stránky.

    b. Zadejte název domény v **známé domén** textové pole.

    c. Klikněte na **Uložit**. 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/kantegassoforconfluence-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kantega-sso-for-confluence-test-user"></a>Vytváření Kantega SSO pro soutoku testovacího uživatele

Pokud chcete povolit uživatelům Azure AD přihlášení do soutoku, musí být zřízená do soutoku. V případě Kantega jednotné přihlašování pro soutoku zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší Kantega jednotné přihlašování pro web společnosti soutoku jako správce.

2. Pozastavte ukazatel myši na ikonu a klikněte na **Správa uživatelů**.

    ![Můžete přidat zaměstnance](./media/kantegassoforconfluence-tutorial/user1.png) 

3. V části uživatelé klikněte na tlačítko **přidat uživatele** kartě. Na **"Přidat uživatele"** dialogové okno stránky, proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/kantegassoforconfluence-tutorial/user2.png) 

    a. V **uživatelské jméno** jako typ e-mailu uživatele k textovému poli, Brittasimon@contoso.com.

    b. V **úplný název** textovému poli, zadejte úplný název tohoto uživatele jako Britta Simon.

    c. V **e-mailu** jako typ e-mailovou adresu uživatele k textovému poli, Brittasimon@contoso.com.

    d. V **heslo** textovému poli, zadejte heslo pro uživatele.

    e. Klikněte na tlačítko **Potvrdit heslo** znovu zadat heslo.
    
    f. Klikněte na tlačítko **přidat** tlačítko.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí jednotného přihlašování k Kantega pro soutoku udělení přístupu.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Kantega jednotné přihlašování pro soutoku, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Kantega jednotné přihlašování pro soutoku**.

    ![Konfigurovat jednotné přihlašování](./media/kantegassoforconfluence-tutorial/tutorial_kantegassoforconfluence_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko Kantega jednotné přihlašování pro dlaždici soutoku na přístupovém panelu, můžete by měl získat automaticky přihlášení k vaší Kantega jednotné přihlašování pro aplikace soutoku.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforconfluence-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforconfluence-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforconfluence-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforconfluence-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforconfluence-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforconfluence-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforconfluence-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforconfluence-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforconfluence-tutorial/tutorial_general_203.png

