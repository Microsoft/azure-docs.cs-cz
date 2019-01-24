---
title: 'Kurz: Integrace Azure Active Directory s Rightscale | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: cda9af9b732643300b8f212471beff066f47b6b3
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827359"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Kurz: Integrace Azure Active Directory s Rightscale

V tomto kurzu se dozvíte, jak integrovat Rightscale s Azure Active Directory (Azure AD).

Rightscale integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Rightscale
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Rightscale (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Rightscale, potřebujete následující položky:

- Předplatné Azure AD
- Rightscale jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Rightscale z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rightscale-from-the-gallery"></a>Přidání Rightscale z Galerie
Konfigurace integrace Rightscale do služby Azure AD, budete muset přidat Rightscale z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Rightscale z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Rightscale**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/rightscale-tutorial/tutorial_rightscale_search.png)

1. Na panelu výsledků vyberte **Rightscale**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Rightscale podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Rightscale je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Rightscale potřeba navázat.

V Rightscale, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Rightscale, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Rightscale](#creating-a-rightscale-test-user)**  – Pokud chcete mít protějšek Britta Simon Rightscale, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Rightscale.

**Ke konfiguraci Azure AD jednotné přihlašování s Rightscale, proveďte následující kroky:**

1. Na webu Azure Portal na **Rightscale** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

1. Na **Rightscale domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **zahájené pomocí IDP režimu** není nutné provádět žádné kroky jako aplikace už je předem integrovaná s Azure.

    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_url.png)

1. Na **Rightscale domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Klikněte na **zobrazit pokročilé nastavení URL**.

    b. V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://login.rightscale.com/`

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_general_400.png)

1. Na **Rightscale konfigurace** klikněte na tlačítko **nakonfigurovat Rightscale** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, budete muset přihlašování k vašemu tenantovi RightScale jako správce.

    a. V nabídce v horní části klikněte **nastavení** kartě a vyberte **Single Sign-On**.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Klikněte "**nové**" tlačítko pro přidání **vašeho zprostředkovatele Identity SAML**.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. Do textového pole z **zobrazovaný název**, zadejte název vaší společnosti.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Vyberte **iniciované RightScale povolit jednotné přihlašování pomocí pomocného parametru zjišťování** a vstupní vaše **název domény** v nižší než textovém poli.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal do **koncový bod jednotného přihlašování SAML** v RightScale.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal do **SAML EntityID** v RightScale.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Klikněte na tlačítko **prohlížeče** tlačítko Nahrát certifikát, který jste si stáhli z webu Azure portal.
   
    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Klikněte na **Uložit**.
<CE>
> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/rightscale-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/rightscale-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/rightscale-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-rightscale-test-user"></a>Vytvoření zkušebního uživatele Rightscale

V této části vytvoříte uživatele v RightScale jako Britta Simon. Práce s [tým podpory Rightscale klienta](mailto:support@rightscale.com) přidat uživatele na platformě RightScale.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Rightscale použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Rightscale Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Rightscale**.

    ![Konfigurace jednotného přihlašování](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.  

Po kliknutí na dlaždici RightScale na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci RightScale.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

