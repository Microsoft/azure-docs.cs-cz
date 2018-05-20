---
title: 'Kurz: Azure Active Directory integrace s LearnUpon | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: f9f7cf65cc2742fef705558c3a99f9faa49a7d3d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Kurz: Azure Active Directory integrace s LearnUpon

V tomto kurzu zjistěte, jak integrovat LearnUpon s Azure Active Directory (Azure AD).

Integrace LearnUpon s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k LearnUpon
- Můžete povolit uživatelům, aby automaticky získat přihlášení k LearnUpon (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LearnUpon, potřebujete následující položky:

- Předplatné služby Azure AD
- LearnUpon jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání LearnUpon z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-learnupon-from-the-gallery"></a>Přidání LearnUpon z Galerie
Při konfiguraci integrace LearnUpon do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS LearnUpon z galerie.

**Pokud chcete přidat LearnUpon z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **LearnUpon**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_search.png)

5. Na panelu výsledků vyberte **LearnUpon**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s LearnUpon podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v LearnUpon je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v LearnUpon musí navázat.

V LearnUpon, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LearnUpon, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele LearnUpon](#creating-a-learnupon-test-user)**  – Pokud chcete mít protějšek Britta Simon v LearnUpon propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci LearnUpon.

**Ke konfiguraci Azure AD jednotné přihlašování s LearnUpon, proveďte následující kroky:**

1. Na portálu Azure na **LearnUpon** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_samlbase.png)

3. Na **LearnUpon domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_url.png)

    V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Upozorňujeme, že se nejedná skutečné hodnoty. budete muset aktualizovat tuto hodnotu s skutečná adresa URL odpovědi. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory LearnUpon](https://www.learnupon.com/features/support/).



4. Na **SAML podpisový certifikát** část, vyhledejte **kryptografický otisk** – to bude přidán do nastavení LearnUpon SAML.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_general_400.png)

6. Na **LearnUpon konfigurace** klikněte na tlačítko **konfigurace LearnUpon** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_configure.png) 

7. Otevřete jiné instance prohlížeče a přihlášení do LearnUpon s účtem správce. 

8. Klikněte **nastavení** kartě.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_06.png)

9. Klikněte na tlačítko **jednotné přihlašování – SAML**a potom klikněte na **obecné nastavení** ke konfiguraci nastavení SAML.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_07.png) 

10. V **obecné nastavení** část, proveďte následující kroky:
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Vyberte **povoleno**.

    b. Vyberte **verze** jako **2.0**.

    c. Vyberte **přeskočit podmínky** jako **ne**.

    d. V **vystavení tokenu SAML název parametru** textovému poli, typ výše uvedený název parametru požadavek post na adresu URL SAML příjemce, který obsahuje kontrolního výrazu SAML ověřit a ověřit – například **SAMLResponse** .

    e. V **formát názvu identifikátor** textovému poli, zadejte hodnotu, která určuje, kam ve vaší kontrolního výrazu SAML identifikátor uživatele (e-mailovou adresu) nachází – například **urn: oasis: názvy: tc: SAML:1.1:nameid-formát: emailAddress**.
  
    f. V **identifikovat umístění poskytovatele** textovému poli, zadejte hodnotu, která určuje, kdy uživatelé odesílají na Pokud kliknutí na ikonu vašeho nahrané z obrazovky přihlášení k portálu Azure.
  
    g. V **Odhlásit se adresa URL** textovému poli, Vložit **Sign-Out URL** který jste zkopírovali z portálu Azure.
    
    h. Klikněte na tlačítko **spravovat prstem výtisků**a pak nahrajte prstu stažené certifikátu.

11. Klikněte na tlačítko **uživatelská nastavení**a poté proveďte následující kroky:
   
     ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. V **křestní jméno identifikátor formátu** textovému poli, zadejte hodnotu, která sděluje nám v vaší kontrolního výrazu SAML firstname uživatelé nachází – například: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. V **poslední formát názvu identifikátor** textovému poli, zadejte hodnotu, která sděluje nám v vaší kontrolního výrazu SAML lastname uživatelé nachází – například: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-learnupon-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-learnupon-test-user"></a>Vytvoření zkušebního uživatele LearnUpon

Cílem této části je vytvoření uživatele v LearnUpon nazývá Britta Simon. LearnUpon podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Vytvoří se nový uživatel během pokusu o přístup k LearnUpon, pokud ještě neexistuje. [Konfigurace Azure AD jednotné přihlášení](#configuring-azure-ad-single-single-sign-on).

>[!NOTE]
>Pokud potřebujete vytvořit uživatele s ručně, budete muset kontaktovat [tým podpory LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu LearnUpon.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon LearnUpon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **LearnUpon**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-learnupon-tutorial/tutorial_learnupon_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici LearnUpon na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci LearnUpon.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-learnupon-tutorial/tutorial_general_203.png

