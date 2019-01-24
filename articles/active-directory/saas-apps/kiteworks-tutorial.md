---
title: 'Kurz: Integrace Azure Active Directory pomocí Kiteworks | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kiteworks.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f7984aaf-ab1f-4a85-9646-a9523f5275d9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 627d2733c01a33a243978df552cf268dde983c5f
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808141"
---
# <a name="tutorial-azure-active-directory-integration-with-kiteworks"></a>Kurz: Integrace Azure Active Directory pomocí Kiteworks

V tomto kurzu se dozvíte, jak integrovat Kiteworks s Azure Active Directory (Azure AD).

Kiteworks integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Kiteworks
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Kiteworks (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Kiteworks, potřebujete následující položky:

- Předplatné Azure AD
- Kiteworks jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Kiteworks z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-kiteworks-from-the-gallery"></a>Přidání Kiteworks z Galerie
Konfigurace integrace Kiteworks do služby Azure AD, budete muset přidat Kiteworks z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Kiteworks z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Kiteworks**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kiteworks-tutorial/tutorial_kiteworks_search.png)

1. Na panelu výsledků vyberte **Kiteworks**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kiteworks-tutorial/tutorial_kiteworks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Kiteworks podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Kiteworks je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Kiteworks potřeba navázat.

V Kiteworks přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování pomocí Kiteworks, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Kiteworks](#creating-a-kiteworks-test-user)**  – Pokud chcete mít protějšek Britta Simon Kiteworks, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Kiteworks.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Kiteworks, proveďte následující kroky:**

1. Na webu Azure Portal na **Kiteworks** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_samlbase.png)

1. Na **Kiteworks domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.kiteworks.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.kiteworks.com/sp/module.php/saml/sp/saml2-acs.php/sp-sso`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory Kiteworks klienta](https://accellion.com/support) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_general_400.png)

1. Na **Kiteworks konfigurace** klikněte na tlačítko **nakonfigurovat Kiteworks** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_configure.png) 

1. Přihlaste se k webu společnosti Kiteworks jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**.
   
    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_06.png) 

1. V **ověřování a autorizace** klikněte na tlačítko **nastavení jednotného přihlašování**. 
   
    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_07.png)
 
1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_09.png)   

    a. Vyberte **ověřování prostřednictvím jednotného přihlašování**.

    b. Vyberte **zahájit AuthnRequest**.

    c. V **IDP Entity ID** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 

    d. V **jednotné přihlašování – adresa URL služby** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    e. V **adresu URL jednotného odhlašování služby** textového pole vložte hodnotu **odhlašování URL**, který jste zkopírovali z portálu Azure portal.

    f. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **certifikát veřejného klíče RSA** textového pole.
 
    g. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/kiteworks-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/kiteworks-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kiteworks-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/kiteworks-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-kiteworks-test-user"></a>Vytvoření zkušebního uživatele Kiteworks

Cílem této části je vytvořte uživatele Britta Simon v Kiteworks.

Kiteworks podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Kitewors, pokud ještě neexistuje.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory Kiteworks](https://accellion.com/support).
 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Kiteworks použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Kiteworks Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Kiteworks**.

    ![Konfigurace jednotného přihlašování](./media/kiteworks-tutorial/tutorial_kiteworks_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.  

Po kliknutí na dlaždici Kiteworks na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Kiteworks.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kiteworks-tutorial/tutorial_general_01.png
[2]: ./media/kiteworks-tutorial/tutorial_general_02.png
[3]: ./media/kiteworks-tutorial/tutorial_general_03.png
[4]: ./media/kiteworks-tutorial/tutorial_general_04.png

[100]: ./media/kiteworks-tutorial/tutorial_general_100.png

[200]: ./media/kiteworks-tutorial/tutorial_general_200.png
[201]: ./media/kiteworks-tutorial/tutorial_general_201.png
[202]: ./media/kiteworks-tutorial/tutorial_general_202.png
[203]: ./media/kiteworks-tutorial/tutorial_general_203.png

