---
title: 'Kurz: Azure Active Directory integrace s Heroku | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Heroku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d7d72ec6-4a60-4524-8634-26d8fbbcc833
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: a3ce04b090f53222a4cb58714afea418d39730d7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225843"
---
# <a name="tutorial-azure-active-directory-integration-with-heroku"></a>Kurz: Azure Active Directory integrace s Heroku

V tomto kurzu zjistěte, jak integrovat Heroku s Azure Active Directory (Azure AD).

Integrace Heroku s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Heroku
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Heroku (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Heroku, potřebujete následující položky:

- Předplatné služby Azure AD
- Heroku jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Heroku z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-heroku-from-the-gallery"></a>Přidání Heroku z Galerie
Při konfiguraci integrace Heroku do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Heroku z galerie.

**Pokud chcete přidat Heroku z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Heroku**.

    ![Vytváření testovacího uživatele Azure AD](./media/heroku-tutorial/tutorial_heroku_search.png)

5. Na panelu výsledků vyberte **Heroku**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/heroku-tutorial/tutorial_heroku_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Heroku podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Heroku je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Heroku musí navázat.

V Heroku, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Heroku, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Heroku](#creating-a-heroku-test-user)**  – Pokud chcete mít protějšek Britta Simon v Heroku propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Heroku.

**Ke konfiguraci Azure AD jednotné přihlašování s Heroku, proveďte následující kroky:**

1. Na portálu Azure na **Heroku** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/heroku-tutorial/tutorial_heroku_samlbase.png)

3. Na **Heroku domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/heroku-tutorial/tutorial_heroku_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:    
    `https://sso.heroku.com/saml/<company-name>/init`

    b. V **identifikátoru adresy URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce:            
    `https://sso.heroku.com/saml/<company-name>`

    > [!NOTE]
    >Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Tyto hodnoty můžete získat od Heroku týmu, který je popsán v pozdějších částech tohoto článku. 
        
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/heroku-tutorial/tutorial_heroku_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/heroku-tutorial/tutorial_general_400.png)

6. Pokud chcete povolit jednotné přihlašování v Heroku, proveďte následující kroky:
   
    a. Přihlaste se k účtu Heroku jako správce.

    b. Klikněte na kartu **Nastavení**.

    c. Na **jediné přihlášení na stránce**, klikněte na tlačítko **nahrát Metadata**.

    d. Nahrajte soubor metadat, který jste si stáhli z portálu Azure.

    e. Po úspěšné instalaci správci najdete v potvrzovacím dialogovém okně a zobrazí se adresa URL jednotného přihlášení pro koncové uživatele. 

    f. Kopírování **Heroku přihlašovací adresa URL** a **Heroku Entity ID** hodnoty a vraťte se zpátky a **Heroku domény a adresy URL** části na portálu Azure a vložte tyto hodnoty do  **Adresa Url přihlašování** a **identifikátor** textových polí v uvedeném pořadí.

    ![Konfigurovat jednotné přihlašování](./media/heroku-tutorial/tutorial_heroku_52.png) 
    
8. Klikněte na **Další**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **Active Directory podnikové aplikace** jednoduše klikněte na tlačítko **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím  **Konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/heroku-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/heroku-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/heroku-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/heroku-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-heroku-test-user"></a>Vytvoření zkušebního uživatele Heroku

V této části vytvoříte volal Britta Simon v Heroku uživatele. Heroku podporuje za běhu zřizování, který je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří při přístupu k Heroku, pokud uživatel ještě neexistuje. Po zřízení účtu koncový uživatel obdrží e-mail o ověření a je třeba ji kliknutím na odkaz potvrzení.

>[!NOTE]
>Pokud potřebujete ručně vytvořit uživatele, budete muset kontaktovat [tým podpory Heroku klienta](https://www.heroku.com/support).
>  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Heroku.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Heroku, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Heroku**.

    ![Konfigurovat jednotné přihlašování](./media/heroku-tutorial/tutorial_heroku_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Heroku na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Heroku.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/heroku-tutorial/tutorial_general_01.png
[2]: ./media/heroku-tutorial/tutorial_general_02.png
[3]: ./media/heroku-tutorial/tutorial_general_03.png
[4]: ./media/heroku-tutorial/tutorial_general_04.png

[100]: ./media/heroku-tutorial/tutorial_general_100.png

[200]: ./media/heroku-tutorial/tutorial_general_200.png
[201]: ./media/heroku-tutorial/tutorial_general_201.png
[202]: ./media/heroku-tutorial/tutorial_general_202.png
[203]: ./media/heroku-tutorial/tutorial_general_203.png
