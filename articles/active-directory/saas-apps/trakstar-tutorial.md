---
title: 'Kurz: Integrace Azure Active Directory s Trakstar | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trakstar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 411cb8c3-95c6-4138-acf2-ffc7f663e89a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 5380c909602053b1fd20e85c81099d1bb2c6614b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823789"
---
# <a name="tutorial-azure-active-directory-integration-with-trakstar"></a>Kurz: Integrace Azure Active Directory s Trakstar

V tomto kurzu se dozvíte, jak integrovat Trakstar s Azure Active Directory (Azure AD).

Trakstar integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Trakstar
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Trakstar (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Trakstar, potřebujete následující položky:

- Předplatné Azure AD
- Trakstar jednotného přihlašování povolená předplatného
    - Jednotné přihlašování je placené funkce Trakstar. Povolit pro vaši organizaci, kontaktujte [tým podpory Trakstar klienta](mailto:support@trakstar.com).

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Trakstar z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-trakstar-from-the-gallery"></a>Přidání Trakstar z Galerie
Konfigurace integrace Trakstar do služby Azure AD, budete muset přidat Trakstar z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Trakstar z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Trakstar**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/trakstar-tutorial/tutorial_trakstar_search.png)

1. Na panelu výsledků vyberte **Trakstar**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/trakstar-tutorial/tutorial_trakstar_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Trakstar podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Trakstar je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Trakstar potřeba navázat.

V Trakstar, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Trakstar, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Trakstar](#creating-a-trakstar-test-user)**  – Pokud chcete mít protějšek Britta Simon Trakstar, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Trakstar.

**Ke konfiguraci Azure AD jednotné přihlašování s Trakstar, proveďte následující kroky:**

1. Na webu Azure Portal na **Trakstar** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/trakstar-tutorial/tutorial_trakstar_samlbase.png)

1. Na **Trakstar domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/trakstar-tutorial/tutorial_trakstar_url.png)

    a. V **přihlašovací adresa URL** textové pole, kopie hodnota nalezena v **adresa URL služby ACS (příjemce)** v rámci Trakstar (Nastavení > ověřování a jednotné přihlašování) ve formátu: `https://app.trakstar.com/auth/saml/callback?namespace=<YOUR_NAMESPACE>`

    b. V **identifikátor** textové pole, ponechte výchozí nastavení: `https://app.trakstar.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Přihlaste se ke Trakstar jako správce k získání těchto hodnot.
    > Pokud nevidíte kartu "Ověřování a jednotné přihlašování" v rámci nastavení, nemusí mít funkci
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/trakstar-tutorial/tutorial_trakstar_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/trakstar-tutorial/tutorial_general_400.png)

1. Na **Trakstar konfigurace** klikněte na tlačítko **nakonfigurovat Trakstar** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/trakstar-tutorial/tutorial_trakstar_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Trakstar** straně, musíte se přihlásit jako správce a zadejte **certifikát (Base64)**, **URL odhlašování SAML Entity ID a SAML jednotné přihlašování služby Adresa URL**. 

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/trakstar-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/trakstar-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/trakstar-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/trakstar-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-trakstar-test-user"></a>Vytvoření zkušebního uživatele Trakstar

Cílem této části je vytvořte uživatele Britta Simon v Trakstar.


### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Trakstar použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Trakstar Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Trakstar**.

    ![Konfigurace jednotného přihlašování](./media/trakstar-tutorial/tutorial_trakstar_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.  
Po kliknutí na dlaždici Trakstar na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Trakstar. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/trakstar-tutorial/tutorial_general_01.png
[2]: ./media/trakstar-tutorial/tutorial_general_02.png
[3]: ./media/trakstar-tutorial/tutorial_general_03.png
[4]: ./media/trakstar-tutorial/tutorial_general_04.png

[100]: ./media/trakstar-tutorial/tutorial_general_100.png

[200]: ./media/trakstar-tutorial/tutorial_general_200.png
[201]: ./media/trakstar-tutorial/tutorial_general_201.png
[202]: ./media/trakstar-tutorial/tutorial_general_202.png
[203]: ./media/trakstar-tutorial/tutorial_general_203.png

