---
title: 'Kurz: Azure Active Directory integrace s CloudPassage | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 412e2c46ba8dae19892b63899abbfc48ca6a82a8
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Kurz: Azure Active Directory integrace s CloudPassage

V tomto kurzu zjistěte, jak integrovat CloudPassage s Azure Active Directory (Azure AD).

Integrace CloudPassage s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k CloudPassage
- Můžete povolit uživatelům, aby automaticky získat přihlášení k CloudPassage (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s CloudPassage, potřebujete následující položky:

- Předplatné služby Azure AD
- CloudPassage jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání CloudPassage z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cloudpassage-from-the-gallery"></a>Přidání CloudPassage z Galerie
Při konfiguraci integrace CloudPassage do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS CloudPassage z galerie.

**Pokud chcete přidat CloudPassage z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **CloudPassage**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_search.png)

5. Na panelu výsledků vyberte **CloudPassage**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s CloudPassage podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v CloudPassage je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v CloudPassage musí navázat.

V CloudPassage, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s CloudPassage, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele CloudPassage](#creating-a-cloudpassage-test-user)**  – Pokud chcete mít protějšek Britta Simon v CloudPassage propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci CloudPassage.

**Ke konfiguraci Azure AD jednotné přihlašování s CloudPassage, proveďte následující kroky:**

1. Na portálu Azure na **CloudPassage** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_samlbase.png)

3. Na **CloudPassage domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://portal.cloudpassage.com/saml/init/accountid`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://portal.cloudpassage.com/saml/consume/accountid`. Hodnota tohoto atributu můžete získat kliknutím **nastavení jednotného přihlašování k dokumentaci** v **nastavení jednotného přihlašování** části portálu CloudPassage.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png)
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory CloudPassage klienta](https://www.cloudpassage.com/company/contact/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_certificate.png) 

5. Aplikace CloudPassage očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tento.
   
   ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_25.png) 

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | FirstName |user.givenname |
    | Příjmení |user.surname |
    | e-mail |User.Mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_400.png)
    
8. Na **CloudPassage konfigurace** klikněte na tlačítko **konfigurace CloudPassage** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_configure.png) 

9. V okně jiný prohlížeč přihlašování k webu společnosti CloudPassage jako správce.

10. V nabídce v horní části, klikněte na tlačítko **nastavení**a potom klikněte na **Správa webu**. 
   
    ![Konfigurovat jednotné přihlašování][12]

11. Klikněte **nastavení ověřování** kartě. 
   
    ![Konfigurovat jednotné přihlašování][13]

12. V **nastavení jednotného přihlašování** část, proveďte následující kroky: 
   
    ![Konfigurovat jednotné přihlašování][14]

    a. Vyberte **povolit jeden sign-on(SSO) (dokumentace instalace jednotné přihlašování)** zaškrtávací políčko.
    
    b. Vložení **SAML Entity ID** do **URL vystavitele SAML** textové pole.
  
    c. Vložení **SAML jeden přihlašování adresa URL služby** do **adresu URL koncového bodu SAML** textové pole.
  
    d. Vložení **Sign-Out URL** do **odhlášení cílová stránka** textové pole.
  
    e. V poznámkovém bloku otevřete stažený certifikát, zkopírujte obsah stažený certifikát do schránky a vložte ji do **x 509 certifikát** textové pole.
  
    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-cloudpassage-test-user"></a>Vytvoření zkušebního uživatele CloudPassage

Cílem této části je vytvoření uživatele v CloudPassage nazývá Britta Simon.

**Vytvoření uživatele v CloudPassage nazývá Britta Simon, proveďte následující kroky:**

1. Přihlášení k vaší **CloudPassage** společnosti lokality jako správce. 

2. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom klikněte na **Správa webu**. 
   
   ![Vytvoření zkušebního uživatele CloudPassage][22] 

3. Klikněte **uživatelé** a pak klikněte **přidat nové uživatele**. 
   
   ![Vytvoření zkušebního uživatele CloudPassage][23]

4. V **přidat nové uživatele** část, proveďte následující kroky: 
   
   ![Vytvoření zkušebního uživatele CloudPassage][24]
    
    a. V **křestní jméno** textovému poli, zadejte Britta. 
  
    b. V **příjmení** textovému poli, zadejte Simon.
  
    c. V **uživatelské jméno** textovému poli, **e-mailu** textové pole a **znovu zadejte e-mailu** textovému poli, zadejte uživatelské jméno je Britta ve službě Azure AD.
  
    d. Jako **typ přístupu**, vyberte **povolit přístup z portálu bylo**.
  
    e. Klikněte na tlačítko **Add** (Přidat).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu CloudPassage.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon CloudPassage, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **CloudPassage**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.

Když kliknete na dlaždici CloudPassage na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci CloudPassage.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png

[100]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_203.png

