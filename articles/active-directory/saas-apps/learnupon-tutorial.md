---
title: 'Kurz: Integrace Azure Active Directory s LearnUpon | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70d4e507087e645c9bfd41e7ef6b90098079ab1d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202430"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Kurz: Integrace Azure Active Directory s LearnUpon

V tomto kurzu se dozvíte, jak integrovat LearnUpon s Azure Active Directory (Azure AD).

LearnUpon integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LearnUpon
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LearnUpon (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LearnUpon, potřebujete následující položky:

- Předplatné Azure AD
- LearnUpon jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LearnUpon z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-learnupon-from-the-gallery"></a>Přidání LearnUpon z Galerie
Konfigurace integrace LearnUpon do služby Azure AD, budete muset přidat LearnUpon z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LearnUpon z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **LearnUpon**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learnupon-tutorial/tutorial_learnupon_search.png)

1. Na panelu výsledků vyberte **LearnUpon**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí LearnUpon podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v LearnUpon je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LearnUpon potřeba navázat.

V LearnUpon, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LearnUpon, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele LearnUpon](#creating-a-learnupon-test-user)**  – Pokud chcete mít protějšek Britta Simon LearnUpon, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LearnUpon.

**Ke konfiguraci Azure AD jednotné přihlašování s LearnUpon, proveďte následující kroky:**

1. Na webu Azure Portal na **LearnUpon** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

1. Na **LearnUpon domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Všimněte si, že to není skutečné hodnoty. je potřeba aktualizovat tato hodnota se skutečná adresa URL odpovědi. Chcete-li získat tuto hodnotu kontakt [tým podpory LearnUpon](https://www.learnupon.com/features/support/).



1. Na **podpisový certifikát SAML** části, vyhledejte **kryptografický otisk** – tím se přidají do nastavení LearnUpon SAML.

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_general_400.png)

1. Na **LearnUpon konfigurace** klikněte na tlačítko **nakonfigurovat LearnUpon** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

1. Otevřete jinou instanci prohlížeče a přihlaste se do LearnUpon pomocí účtu správce. 

1. Klikněte na tlačítko **nastavení** kartu.
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Klikněte na tlačítko **jednotného přihlašování – SAML**a potom klikněte na tlačítko **obecné nastavení** ke konfiguraci nastavení SAML.
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. V **obecné nastavení** části, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Vyberte **povolené**.

    b. Vyberte **verze** jako **2.0**.

    c. Vyberte **přeskočit podmínky** jako **ne**.

    d. V **odeslat Token SAML název param** textové pole, typ výše uvedené název parametru požadavku post odeslané na adresu příjemce SAML, který obsahuje kontrolní výraz SAML ověřit a – třeba authenticated **SAMLResponse** .

    e. V **formát názvu identifikátor** textového pole zadejte hodnotu, která označuje, kde ve vaší kontrolní výraz SAML identifikátor uživatele (e-mailovou adresu) se nachází – například **urn: oasis: názvy: tc: SAML:1.1:nameid – formát: emailAddress**.
  
    f. V **určit konkrétní umístění poskytovatele** textového pole zadejte hodnotu, která určuje, kde jsou uživatelé nasměrovaní pokud kliknou na vaše nahrané ikonu z obrazovky přihlášení k portálu Azure.
  
    g. V **odhlašovací adresa URL** vložit do textového pole **odhlašování URL** který jste zkopírovali z portálu Azure portal.
    
    h. Klikněte na tlačítko **spravovat prstem vytiskne**a pak nahrajte hlas staženého certifikátu.

1. Klikněte na tlačítko **uživatelská nastavení**a pak proveďte následující kroky:
   
     ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. V **křestní jméno identifikátor formátu** textového pole zadejte hodnotu, která nám sdělí v vaše kontrolní výraz SAML jméno uživatele se nachází – například: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. V **poslední formát názvu identifikátor** textového pole zadejte hodnotu, která nám sdělí v vaše kontrolní výraz SAML příjmení uživatele nachází – například: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/learnupon-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/learnupon-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/learnupon-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-learnupon-test-user"></a>Vytvoření zkušebního uživatele LearnUpon

Cílem této části je vytvořte uživatele Britta Simon v LearnUpon. LearnUpon podporuje just-in-time zřizování, který je ve výchozím nastavení povolená.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k LearnUpon, pokud ještě neexistuje. Konfiguruje se Azure AD Single Sign-On.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k LearnUpon použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit LearnUpon Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **LearnUpon**.

    ![Konfigurace jednotného přihlašování](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici LearnUpon na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci LearnUpon.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

