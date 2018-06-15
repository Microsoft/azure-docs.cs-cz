---
title: 'Kurz: Azure Active Directory integrace s PolicyStat | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 0cd4cc4a622a8e23b98e06bb33d99755f247612c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349987"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Kurz: Azure Active Directory integrace s PolicyStat

V tomto kurzu zjistěte, jak integrovat PolicyStat s Azure Active Directory (Azure AD).

Integrace PolicyStat s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PolicyStat
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PolicyStat (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PolicyStat, potřebujete následující položky:

- Předplatné služby Azure AD
- PolicyStat jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PolicyStat z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-policystat-from-the-gallery"></a>Přidání PolicyStat z Galerie
Při konfiguraci integrace PolicyStat do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS PolicyStat z galerie.

**Pokud chcete přidat PolicyStat z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **PolicyStat**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_search.png)

5. Na panelu výsledků vyberte **PolicyStat**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s PolicyStat podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v PolicyStat je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v PolicyStat musí navázat.

V PolicyStat, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PolicyStat, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele PolicyStat](#creating-a-policystat-test-user)**  – Pokud chcete mít protějšek Britta Simon v PolicyStat propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci PolicyStat.

**Ke konfiguraci Azure AD jednotné přihlašování s PolicyStat, proveďte následující kroky:**

1. Na portálu Azure na **PolicyStat** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_samlbase.png)

3. Na **PolicyStat domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.policystat.com`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory PolicyStat klienta](http://www.policystat.com/support/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_certificate.png) 

5. Cílem této části se popisují, jak uživatelům povolit ověřování na PolicyStat ke svému účtu ve službě Azure AD využívající federaci na základě protokolu SAML.

    Aplikace PolicyStat očekává SAML kontrolní výrazy ve specifickém formátu, který můžete přidat mapování vlastní atribut vyžaduje vaše **atributy tokenu SAML** konfigurace.  

     Následující snímek obrazovky ukazuje příklad tohoto objektu.

     ![Atributy](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_attribute.png "atributy")

6. Pokud chcete přidat mapování požadovaný atribut, proveďte následující kroky:

    | Název atributu    |   Hodnota atributu |
    |------------------- | -------------------- |
    | UID | ExtractMailPrefix([mail]) |
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_04.png)

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. V **název atributu** textovému poli, typ **uid**.

    c. V **hodnota atributu** textovému poli, vyberte **ExtractMailPrefix()**.    
   
    d. Z **e-mailu** seznamu, vyberte **User.mail**.
    
    e. Klikněte na tlačítko **Ok**

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_general_400.png)

8. V okně prohlížeče jiný web Přihlaste se jako správce k serveru vaší společnosti PolicyStat.

9. Klikněte **správce** a pak klikněte **konfigurace přihlášení** v levém navigačním podokně.
   
    ![Správce nabídek](./media/active-directory-saas-policystat-tutorial/ic808633.png "správce nabídek")

10. V **instalace** vyberte **povolit jeden integrace přihlašování**.
   
    ![Jednotné přihlašování konfigurace](./media/active-directory-saas-policystat-tutorial/ic808634.png "jednotné přihlašování v konfiguraci")

11. Klikněte na tlačítko **konfigurace atributů**a pak na **konfigurace atributů** část, proveďte následující kroky:
   
    ![Jednotné přihlašování konfigurace](./media/active-directory-saas-policystat-tutorial/ic808635.png "jednotné přihlašování v konfiguraci")
   
    a. V **uživatelské jméno atribut** textovému poli, typ **uid**.

    b. V **křestní jméno atribut** textovému poli, typ **firstname** uživatele **Britta**.

    c. V **poslední atribut Name** textovému poli, typ **lastname** uživatele **Simon**.

    d. V **atribut e-mailu** textovému poli, typ **emailaddress** uživatele **BrittaSimon@contoso.com**.

    e. Klikněte na tlačítko **uložit změny**.

12. Klikněte na tlačítko **si Metadata IDP**a pak na **si Metadata IDP** část, proveďte následující kroky:
   
    ![Jednotné přihlašování konfigurace](./media/active-directory-saas-policystat-tutorial/ic808636.png "jednotné přihlašování v konfiguraci")
   
    a. Otevřete váš soubor stažený metadata, kopírovat obsah a vložte ji do **vaše metadat zprostředkovatelů Identity** textové pole.

    b. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-policystat-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-policystat-test-user"></a>Vytvoření zkušebního uživatele PolicyStat

Pokud chcete povolit uživatelům Azure AD přihlášení do PolicyStat, musí být zřízená do PolicyStat.  

PolicyStat podporuje jenom při zřizování uživatelů čas. To znamená, není nutné ručně přidat uživatele do PolicyStat. Uživatelé bude se automaticky přidají na jejich první přihlášení pomocí jednotného přihlašování.

>[!NOTE]
>Můžete použít všechny ostatní PolicyStat uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované PolicyStat ke zřízení uživatelských účtů Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu PolicyStat.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon PolicyStat, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **PolicyStat**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-policystat-tutorial/tutorial_policystat_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici PolicyStat na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci PolicyStat.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-policystat-tutorial/tutorial_general_203.png

