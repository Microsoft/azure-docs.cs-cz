---
title: 'Kurz: Azure Active Directory integrace s lidskosti | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a lidskosti.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 4d86c7dbc72ee20c7ae15093abc545ebd73affce
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349137"
---
# <a name="tutorial-azure-active-directory-integration-with-humanity"></a>Kurz: Azure Active Directory integrace s lidskosti

V tomto kurzu zjistěte, jak integrovat lidskosti s Azure Active Directory (Azure AD).

Integrace lidskosti s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k lidskosti
- Můžete povolit uživatelům, aby automaticky získat přihlášení k lidskosti (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s lidskosti, potřebujete následující položky:

- Předplatné služby Azure AD
- Lidskosti jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání lidskosti z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-humanity-from-the-gallery"></a>Přidání lidskosti z Galerie
Chcete-li nakonfigurovat integraci lidskosti do služby Azure AD, přidejte lidskosti z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat lidskosti z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **lidskosti**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_search.png)

5. Na panelu výsledků vyberte **lidskosti**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s lidskosti podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v lidskosti je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v lidskosti musí navázat.

V lidskosti, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s lidskosti, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele lidskosti](#creating-a-humanity-test-user)**  – Pokud chcete mít protějšek Britta Simon v lidskosti propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci lidskosti.

**Ke konfiguraci Azure AD jednotné přihlašování s lidskosti, proveďte následující kroky:**

1. Na portálu Azure na **lidskosti** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_samlbase.png)

3. Na **lidskosti domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://company.humanity.com/includes/saml/`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://company.humanity.com/app/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory lidskosti klienta](https://www.humanity.com/support/) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_400.png)

6. Na **lidskosti konfigurace** klikněte na tlačítko **konfigurace lidskosti** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML jeden přihlašování adresa URL služby a adresu URL Sign-Out** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_configure.png) 

7. V okně prohlížeče jiný web, přihlaste se k vaší **lidskosti** společnosti lokality jako správce.

8. V nabídce v horní části, klikněte na tlačítko **správce**.
   
    ![Správce](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "správce")

9. V části **integrace**, klikněte na tlačítko **jednotné přihlašování**.
   
    ![Jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/iC786620.png "jednotného přihlašování")

10. V **jednotné přihlašování** část, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/iC786905.png "jednotného přihlašování")
   
    a. Vyberte **povoleno SAML**.

    b. Vyberte **povolit přihlášení heslo**.

    c. Vložení **SAML jeden přihlašování adresa URL služby** hodnoty do **URL vystavitele SAML** textové pole.

    d. Vložení **Sign-Out URL** hodnotu do **vzdálené adresy URL odhlašovací** textové pole.
   
    e. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte jej do **certifikát X.509** textové pole.

11. Klikněte na tlačítko **uložit nastavení**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-shiftplanning-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-humanity-test-user"></a>Vytvoření zkušebního uživatele lidskosti

Pokud chcete povolit uživatelům Azure AD přihlášení do lidskosti, musí být zřízená do lidskosti. V případě lidskosti zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **lidskosti** společnosti lokality jako správce.

2. Klikněte na tlačítko **správce**.
   
    ![Správce](./media/active-directory-saas-shiftplanning-tutorial/iC786619.png "správce")

3. Klikněte na tlačítko **zaměstnanci**.
   
    ![Zaměstnanci](./media/active-directory-saas-shiftplanning-tutorial/ic786623.png "zaměstnanci")

4. V části **akce**, klikněte na tlačítko **přidat zaměstnanci**.
   
    ![Přidat zaměstnanci](./media/active-directory-saas-shiftplanning-tutorial/iC786624.png "přidat zaměstnanci")

5. V **přidat zaměstnanci** část, proveďte následující kroky:
   
    ![Uložit zaměstnanci](./media/active-directory-saas-shiftplanning-tutorial/iC786625.png "uložit zaměstnanci")
   
    a. Typ **křestní jméno**, **příjmení**, a **e-mailu** platného účtu AAD chcete mají být zahrnuty do související textových polí.

    b. Klikněte na tlačítko **uložit zaměstnanci**.

>[!NOTE]
>Můžete použít všechny ostatní lidskosti uživatele účtu nástroje pro tvorbu nebo rozhraní API poskytované lidskosti zřídit AAD uživatelské účty.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu lidskosti.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon lidskosti, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **lidskosti**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-shiftplanning-tutorial/tutorial_humanity_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici lidskosti na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci lidskosti.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-shiftplanning-tutorial/tutorial_general_203.png

