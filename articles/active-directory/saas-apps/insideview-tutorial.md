---
title: 'Kurz: Integrace Azure Active Directory se službou InsideView | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: fd004302018bf032675957c12e58759a0d4ed6e5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436270"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Kurz: Integrace Azure Active Directory se službou InsideView

V tomto kurzu se dozvíte, jak integrovat InsideView s Azure Active Directory (Azure AD).

InsideView integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k InsideView
- Můžete povolit uživatelům, aby automaticky získat přihlášení k InsideView (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s InsideView, potřebujete následující položky:

- S předplatným služby Azure AD
- InsideView jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání InsideView z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-insideview-from-the-gallery"></a>Přidání InsideView z Galerie
Konfigurace integrace InsideView ve službě Azure AD, budete muset přidat InsideView z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat InsideView z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **InsideView**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/insideview-tutorial/tutorial_insideview_search.png)

1. Na panelu výsledků vyberte **InsideView**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/insideview-tutorial/tutorial_insideview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s InsideView podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v InsideView je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v InsideView potřeba navázat.

V InsideView, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s InsideView, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele InsideView](#creating-a-insideview-test-user)**  – Pokud chcete mít protějšek Britta Simon InsideView, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci InsideView.

**Ke konfiguraci Azure AD jednotné přihlašování s InsideView, proveďte následující kroky:**

1. Na webu Azure Portal na **InsideView** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/insideview-tutorial/tutorial_insideview_samlbase.png)

1. Na **InsideView domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/insideview-tutorial/tutorial_insideview_url.png)
    
    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu Skutečná adresa URL odpovědi. Kontakt [tým podpory InsideView ](mailto:support@insideview.com) tuto výhodu získáte.
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Raw)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/insideview-tutorial/tutorial_insideview_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/insideview-tutorial/tutorial_general_400.png)

1. Na **InsideView konfigurace** klikněte na tlačítko **nakonfigurovat InsideView** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/insideview-tutorial/tutorial_insideview_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti InsideView jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **správce**, **SingleSignOn nastavení**a potom klikněte na tlačítko **přidat SAML**.
   
   ![Jednotné přihlašování SAML pro nastavení](./media/insideview-tutorial/ic794135.png "SAML jednotného přihlašování nastavení")

1. V **přidat nové SAML** části, proveďte následující kroky:

    ![Přidat nový SAML](./media/insideview-tutorial/ic794136.png "přidat nové SAML")
   
    a. V **název služby tokenů zabezpečení** textového pole zadejte název pro vaši konfiguraci.

    b. V **nevyžádané koncový bod SamlP/WS-Fed** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.
    
    c. Otevřete váš kódovaného certifikátu Base64, který jste si stáhli z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **certifikátu STS** textového pole.

    d. V **mapování Id uživatele Crm** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
        
    e. V **Crm e-mailu mapování** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. V **Crm křestní jméno mapování** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    g. V **Crm lastName mapování** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.  

    h. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
 
### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/insideview-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/insideview-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/insideview-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/insideview-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-insideview-test-user"></a>Vytvoření zkušebního uživatele InsideView

Umožňuje uživatelům Azure AD se přihlaste k InsideView, musí být poskytnuty v k InsideView. V případě InsideView zřizování se ruční úlohy.

Obraťte se na uživatele nebo kontaktů vytvořených v InsideView získáte [tým podpory InsideView](mailto:support@insideview.com).

>[!NOTE]
>Můžete použít jakékoli jiné InsideView uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných InsideView zřízení uživatelských účtů služby Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k InsideView použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit InsideView Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **InsideView**.

    ![Konfigurace jednotného přihlašování](./media/insideview-tutorial/tutorial_insideview_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici InsideView na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci InsideView.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/insideview-tutorial/tutorial_general_01.png
[2]: ./media/insideview-tutorial/tutorial_general_02.png
[3]: ./media/insideview-tutorial/tutorial_general_03.png
[4]: ./media/insideview-tutorial/tutorial_general_04.png

[100]: ./media/insideview-tutorial/tutorial_general_100.png

[200]: ./media/insideview-tutorial/tutorial_general_200.png
[201]: ./media/insideview-tutorial/tutorial_general_201.png
[202]: ./media/insideview-tutorial/tutorial_general_202.png
[203]: ./media/insideview-tutorial/tutorial_general_203.png

