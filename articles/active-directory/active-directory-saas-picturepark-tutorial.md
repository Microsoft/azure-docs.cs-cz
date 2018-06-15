---
title: 'Kurz: Azure Active Directory integrace s Picturepark | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 6421921d75b299d44cdd0942b64e524a73dfa042
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352340"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Kurz: Azure Active Directory integrace s Picturepark

V tomto kurzu zjistěte, jak integrovat Picturepark s Azure Active Directory (Azure AD).

Integrace Picturepark s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Picturepark
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Picturepark (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Picturepark, potřebujete následující položky:

- Předplatné služby Azure AD
- Picturepark jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Picturepark z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-picturepark-from-the-gallery"></a>Přidání Picturepark z Galerie
Při konfiguraci integrace Picturepark do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Picturepark z galerie.

**Pokud chcete přidat Picturepark z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Picturepark**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_search.png)

5. Na panelu výsledků vyberte **Picturepark**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Picturepark podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Picturepark je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Picturepark musí navázat.

V Picturepark, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Picturepark, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Picturepark](#creating-a-picturepark-test-user)**  – Pokud chcete mít protějšek Britta Simon v Picturepark propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Picturepark.

**Ke konfiguraci Azure AD jednotné přihlašování s Picturepark, proveďte následující kroky:**

1. Na portálu Azure na **Picturepark** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. Na **Picturepark domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.picturepark.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory Picturepark klienta](https://picturepark.com/about/contact/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** část, zkopírujte **kryptografický OTISK** hodnota certifikátu.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-picturepark-tutorial/tutorial_general_400.png)

6. Na **Picturepark konfigurace** klikněte na tlačítko **konfigurace Picturepark** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_configure.png) 

7. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti Picturepark.

8. Na panelu nástrojů v horní části klikněte na tlačítko **nástroje pro správu**a potom klikněte na **konzoly pro správu**.
   
    ![Konzola pro správu](./media/active-directory-saas-picturepark-tutorial/ic795062.png "konzoly pro správu")

9. Klikněte na tlačítko **ověřování**a potom klikněte na **zprostředkovatelů Identity**.
   
    ![Ověřování](./media/active-directory-saas-picturepark-tutorial/ic795063.png "ověřování")

10. V **konfigurace zprostředkovatele Identity** část, proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/active-directory-saas-picturepark-tutorial/ic795064.png "konfigurace zprostředkovatele Identity")
   
    a. Klikněte na tlačítko **Add** (Přidat).
  
    b. Zadejte název pro svou konfiguraci.
   
    c. Vyberte **nastavit jako výchozí**.
   
    d. V **vystavitele URI** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
   
    e. V **důvěryhodné vystavitele jezdec tiskových** textovému poli, vložte hodnotu **kryptografický otisk** který jste zkopírovali z **SAML podpisový certifikát** části. 

11. Klikněte na tlačítko **JoinDefaultUsersGroup**.

12. Nastavit **Emailaddress** atribut **deklarace identity** textovému poli, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` a klikněte na tlačítko **Uložit**.

      ![Konfigurace](./media/active-directory-saas-picturepark-tutorial/ic795065.png "konfigurace")

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-picturepark-test-user"></a>Vytvoření zkušebního uživatele Picturepark

Pokud chcete povolit uživatelům Azure AD přihlášení do Picturepark, musí být zřízená do Picturepark. V případě Picturepark zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Picturepark** klienta.

2. Na panelu nástrojů v horní části klikněte na tlačítko **nástroje pro správu**a potom klikněte na **uživatelé**.
   
    ![Uživatelé](./media/active-directory-saas-picturepark-tutorial/ic795067.png "uživatelů")

3. V **přehled uživatelů** , klikněte na **nový**.
   
    ![Správa uživatelů](./media/active-directory-saas-picturepark-tutorial/ic795068.png "Správa uživatelů")

4. Na **vytvořit uživatele** dialogové okno, proveďte následující kroky platný Azure Active Directory uživatele, který chcete zřídit:
   
    ![Vytvoření uživatele](./media/active-directory-saas-picturepark-tutorial/ic795069.png "vytvoření uživatele")
   
    a. V **e-mailovou adresu** textovému poli, typ **e-mailová adresa** uživatele **BrittaSimon@contoso.com**.  
   
    b. V **heslo** a **Potvrdit heslo** textová pole, zadejte **heslo** z BrittaSimon. 
   
    c. V **křestní jméno** textovému poli, typ **křestní jméno** uživatele **Britta**. 
   
    d. V **příjmení** textovému poli, typ **příjmení** uživatele **Simon**.
   
    e. V **společnosti** textovému poli, typ **název společnosti** uživatele. 
   
    f. V **země** textovému poli, vyberte **země** uživatele.
  
    g. V **ZIP** textovému poli, typ **PSČ** města.
   
    h. V **města** textovému poli, typ **název města** uživatele.

    i. Vyberte **jazyk**.
   
    j. Klikněte na možnost **Vytvořit**.

>[!NOTE]
>Můžete použít všechny ostatní Picturepark uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované Picturepark ke zřízení uživatelských účtů Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Picturepark.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Picturepark, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Picturepark**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Picturepark na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Picturepark. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_203.png

