---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Kantega pro Bitbucket | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování Kantega pro Bitbucketu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 0b0389147c79bbb15c5034b4885fd7ab0141e72b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196629"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním Kantega pro Bitbucketu

V tomto kurzu se dozvíte, jak integrovat Kantega jednotné přihlašování pro Bitbucketu se službou Azure Active Directory (Azure AD).

Integrace Kantega jednotné přihlašování pro Bitbucket s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Kantega jednotného přihlašování pro Bitbucketu
- Uživatele, aby automaticky získat přihlášení k Kantega jednotné přihlašování pro Bitbucket (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním Kantega pro Bitbucket, potřebujete následující položky:

- Předplatné Azure AD
- Kantega SSO pro Bitbucket jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kantega jednotné přihlašování pro Bitbucketu z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Přidání Kantega jednotné přihlašování pro Bitbucketu z Galerie
Konfigurace integrace Kantega jednotné přihlašování pro Bitbucket do služby Azure AD, budete muset přidat Kantega jednotné přihlašování pro Bitbucketu z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Kantega jednotné přihlašování pro Bitbucketu z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Kantega jednotné přihlašování pro Bitbucket**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

1. Na panelu výsledků vyberte **Kantega jednotné přihlašování pro Bitbucket**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Kantega pro Bitbucket podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Kantega jednotné přihlašování pro Bitbucket je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Kantega jednotné přihlašování pro Bitbucketu.

V Kantega jednotného přihlašování pro Bitbucket, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním Kantega pro Bitbucket, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytváření Kantega jednotné přihlašování pro Bitbucket testovacího uživatele](#creating-a-kantega-sso-for-bitbucket-test-user)**  – Pokud chcete mít protějšek Britta Simon Kantega jednotné přihlašování pro Bitbucketu, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich Kantega jednotného přihlašování pro aplikaci Bitbucketu.

**Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním Kantega pro Bitbucket, proveďte následující kroky:**

1. Na webu Azure Portal na **Kantega jednotné přihlašování pro Bitbucket** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

1. V **IDP** inicializovat v režimu **Kantega jednotné přihlašování pro Bitbucket domény a adresy URL** části provést následující krok:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. V **SP** iniciované režimu, kontrola **zobrazit pokročilé nastavení URL** a postupujte následovně:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Tyto hodnoty jsou přijímány během konfigurace modulu plug-in Bitbucketu, který je vysvětlen později v tomto kurzu.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

1. V okně jiné webové prohlížeče Přihlaste se k portálu pro správu vašich Bitbucket jako správce.

1. Klikněte na tlačítko ozubeného kola a kliknutím **najít nové doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Hledání **Kantega jednotného přihlašování Bitbucket SAML & protokolu Kerberos** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Spustí se instalace modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon33.png)

1.  Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
1. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in. 

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. V **SAML** oddílu. Vyberte **Azure Active Directory (Azure AD)** z **přidat zprostředkovatele identity** rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. Na **vlastnosti aplikace** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Kopírovat **identifikátor ID URI aplikace** hodnoty a použít ho jako **identifikátor, adresa URL odpovědi a přihlašovací adresa URL** na **Kantega jednotné přihlašování pro Bitbucket domény a adresy URL** části webu Azure Portal.

    b. Klikněte na **Další**.

1. Na **import metadat** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Vyberte **soubor metadat v mém počítači**a nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    b. Klikněte na **Další**.

1. Na **název a jednotného přihlašování k umístění** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Přidání názvu zprostředkovatele Identity v **název zprostředkovatele identit** textového pole (např. Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisového certifikátu a klikněte na tlačítko **Další**.   

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. Na **Bitbucket uživatelské účty** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Vyberte **v případě potřeby vytvořte uživatele v Bitbucketu pro interní adresář** a zadejte odpovídající název skupiny pro uživatele (může být více č. ze skupiny, oddělené čárkami).

    b. Klikněte na **Další**.

1. Klikněte na **Dokončit**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. Na **známé domény pro službu Azure AD** části, proveďte následující kroky:  

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Vyberte **známé domén** na levém panelu na stránce.

    b. Zadejte název domény **známé domén** textového pole.

    c. Klikněte na **Uložit**.  

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Vytváření Kantega jednotné přihlašování pro Bitbucket testovacího uživatele

Povolit uživatele Azure AD se přihlaste k Bitbucket, musí být poskytnuty v Bitbucketu. V Kantega jednotného přihlašování pro Bitbucket zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Bitbucket jako správce.

1. Klikněte na ikonu nastavení.

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. V části **správu** kartě oddíl, klikněte na tlačítko **uživatelé**.

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klikněte na tlačítko **vytvořit uživatele**.

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Na **vytvořit uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.
    
    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.
    
    c. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo uživatele.  

    e. V **potvrzení hesla** textového pole zadejte znovu heslo uživatele.

    f. Klikněte na tlačítko **vytvořit uživatele**.   

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Kantega jednotné přihlašování pro Bitbucketu.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Kantega jednotné přihlašování pro Bitbucket, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Kantega jednotné přihlašování pro Bitbucket**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na Kantega jednotné přihlašování pro Bitbucket dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší Kantega jednotného přihlašování pro aplikaci Bitbucketu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

