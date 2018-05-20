---
title: 'Kurz: Azure Active Directory integrace s Wdesk | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: c55b9c32ccf6712da69c869a6f44c71e1c209627
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Kurz: Azure Active Directory integrace s Wdesk

V tomto kurzu zjistěte, jak integrovat Wdesk s Azure Active Directory (Azure AD).

Integrace Wdesk s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Wdesk
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Wdesk (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Wdesk, potřebujete následující položky:

- Předplatné služby Azure AD
- Wdesk jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Wdesk z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-wdesk-from-the-gallery"></a>Přidání Wdesk z Galerie
Při konfiguraci integrace Wdesk do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Wdesk z galerie.

**Pokud chcete přidat Wdesk z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Wdesk**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_search.png)

5. Na panelu výsledků vyberte **Wdesk**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Wdesk podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Wdesk je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Wdesk musí navázat.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Wdesk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Wdesk, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Wdesk](#creating-a-wdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon v Wdesk propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Wdesk.

**Ke konfiguraci Azure AD jednotné přihlašování s Wdesk, proveďte následující kroky:**

1. Na portálu Azure na **Wdesk** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_samlbase.png)

3. Na **Wdesk domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** initiated režimu proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu provést následující krok:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Tyto hodnoty z portálu WDesk získat nakonfigurovat jednotné přihlašování. 
  
5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_general_400.png)
    
7. V okně prohlížeče jiný web a přihlášení k Wdesk jako správce zabezpečení.

8. V dolní části vlevo, klikněte na **správce** a zvolte **správce účtu**:
 
     ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

9. V Wdesk správce, přejděte na **zabezpečení**, pak **SAML** > **SAML nastavení**:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

10. V části **obecné nastavení**, zkontrolujte **povolit SAML jednotné přihlašování**:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

11. V části **podrobné informace o poskytovateli služby**, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopírování **přihlašovací adresa URL** a vložte jej do **přihlašovací adresa Url** textové pole na portálu Azure.
   
      b. Kopírování **adresu Url metadat** a vložte jej do **identifikátor** textové pole na portálu Azure.
       
      c. Kopírování **příjemce url** a vložte jej do **adresa Url odpovědi** textové pole na portálu Azure.
   
      d. Klikněte na tlačítko **Uložit** na portálu Azure a uložte změny.      

12. Klikněte na tlačítko **konfigurovat nastavení IdP** otevřete **upravit nastavení IdP** dialogové okno. Klikněte na tlačítko **zvolit soubor** najít **Metadata.xml** soubor uložit z portálu Azure, pak nahrajte ho.
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
13. Klikněte na tlačítko **uložit změny**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-wdesk-test-user"></a>Vytvoření zkušebního uživatele Wdesk

Pokud chcete povolit uživatelům Azure AD přihlášení k Wdesk, musí být zřízená do Wdesk. V Wdesk zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k Wdesk jako správce zabezpečení.
2. Přejděte na **správce** > **účet správce**.

     ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Klikněte na tlačítko **členy** pod **osoby**.

4. Klikněte na tlačítko **přidat člena** otevřete **přidat člena** dialogové okno. 
   
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser1.png)  

5. V **uživatele** textové pole, zadejte uživatelské jméno uživatele jako **brittasimon@contoso.com** a klikněte na tlačítko **pokračovat** tlačítko.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser3.png)

6.  Zadejte podrobnosti, jak je uvedeno níže:
  
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser4.png)
 
    a. V **e-mailu** textové pole, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele jako **Britta**.

    c. V **příjmení** text zadejte příjmení uživatele jako **Simon**.

7. Klikněte na tlačítko **uložit člen** tlačítko.  

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Wdesk.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Wdesk, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Wdesk**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-wdesk-tutorial/tutorial_wdesk_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Wdesk na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Wdesk.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-wdesk-tutorial/tutorial_general_203.png

