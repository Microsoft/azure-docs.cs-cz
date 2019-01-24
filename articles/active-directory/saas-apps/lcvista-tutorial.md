---
title: 'Kurz: Integrace Azure Active Directory s LCVista | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LCVista.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 8db80d6e-3275-419f-aa39-6115a7bc9800
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 12e3719cb292ef0f2132597e6b849f79f596ff7c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54827801"
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Kurz: Integrace Azure Active Directory s LCVista

V tomto kurzu se dozvíte, jak integrovat LCVista s Azure Active Directory (Azure AD).

LCVista integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LCVista
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LCVista (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LCVista, potřebujete následující položky:

- Předplatné Azure AD
- LCVista jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LCVista z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-lcvista-from-the-gallery"></a>Přidání LCVista z Galerie
Konfigurace integrace LCVista do služby Azure AD, budete muset přidat LCVista z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LCVista z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LCVista**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lcvista-tutorial/tutorial_lcvista_search.png)

1. Na panelu výsledků vyberte **LCVista**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lcvista-tutorial/tutorial_lcvista_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s LCVista podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v LCVista je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LCVista potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v LCVista.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LCVista, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LCVista](#creating-a-lcvista-test-user)**  – Pokud chcete mít protějšek Britta Simon LCVista, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LCVista.

**Ke konfiguraci Azure AD jednotné přihlašování s LCVista, proveďte následující kroky:**

1. Na webu Azure Portal na **LCVista** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_lcvista_samlbase.png)

1. Na **LCVista domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_lcvista_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.lcvista.com/rainier/login`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.lcvista.com` 
     
    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty se skutečné identifikátorem a přihlašovací adresa URL. Kontakt [tým podpory LCVista klienta](https://lcvista.com/contact) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_lcvista_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_general_400.png)
    
1. Na **LCVista konfigurace** klikněte na tlačítko **nakonfigurovat LCVista** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_lcvista_configure.png) 

1.  Přihlaste se k aplikaci LCVista jako správce.

1. V **SAML Config** oddílu, zkontrolujte **povolit SAML přihlášení** a zadejte podrobnosti, jak je uvedeno v obrázku níže. 

    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_lcvista_config.png)

    a. Vložit **URL vystavitele** zkopírovanou z Azure AD **Entity ID** oddílu. 

    b. Vložit **jednotné přihlašování – adresa URL služby** zkopírovanou z Azure AD **adresy URL** oddílu.

    c. Z Metadata (XML) který jste si stáhli z webu Azure portal, zkopírujte hodnotu **certifikátu x 509** a vložte ji **certifikátu x509** oddílu.

    d. V **křestní jméno atributu** textového pole vložte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. V **poslední název atributu** textového pole vložte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. V **atribut e-mailové** textového pole vložte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. V **atribut uživatelského jména** textového pole vložte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Kliknutím na **Uložit** nastavení uložte.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/lcvista-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/lcvista-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lcvista-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/lcvista-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-lcvista-test-user"></a>Vytvoření zkušebního uživatele LCVista

V této části vytvoříte uživatele v LCVista jako Britta Simon. Budete muset požádat [tým podpory LCVista klienta](https://lcvista.com/contact) přidat uživatele v aplikaci LCVista. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k LCVista použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit LCVista Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **LCVista**.

    ![Konfigurace jednotného přihlašování](./media/lcvista-tutorial/tutorial_lcvista_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu. Kliknutím na dlaždici LCVista na přístupovém panelu, budete přesměrováni na přihlášení organizace na stránce. Po úspěšném přihlášení budete se být přihlášení k aplikaci LCVista. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lcvista-tutorial/tutorial_general_01.png
[2]: ./media/lcvista-tutorial/tutorial_general_02.png
[3]: ./media/lcvista-tutorial/tutorial_general_03.png
[4]: ./media/lcvista-tutorial/tutorial_general_04.png

[100]: ./media/lcvista-tutorial/tutorial_general_100.png

[200]: ./media/lcvista-tutorial/tutorial_general_200.png
[201]: ./media/lcvista-tutorial/tutorial_general_201.png
[202]: ./media/lcvista-tutorial/tutorial_general_202.png
[203]: ./media/lcvista-tutorial/tutorial_general_203.png

