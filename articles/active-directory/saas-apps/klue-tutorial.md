---
title: 'Kurz: Azure Active Directory integrace s Klue | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Klue.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 08341008-980b-4111-adb2-97bbabbf1e47
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: e2a625c733aac82b5db8529f2d770d31578a6246
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220416"
---
# <a name="tutorial-azure-active-directory-integration-with-klue"></a>Kurz: Azure Active Directory integrace s Klue

V tomto kurzu zjistěte, jak integrovat Klue s Azure Active Directory (Azure AD).

Integrace Klue s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Klue
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Klue (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Klue, potřebujete následující položky:

- Předplatné služby Azure AD
- Klue jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Klue z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-klue-from-the-gallery"></a>Přidání Klue z Galerie
Při konfiguraci integrace Klue do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Klue z galerie.

**Pokud chcete přidat Klue z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Klue**.

    ![Vytváření testovacího uživatele Azure AD](./media/klue-tutorial/tutorial_klue_search.png)

5. Na panelu výsledků vyberte **Klue**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/klue-tutorial/tutorial_klue_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Klue podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Klue je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Klue musí navázat.

V Klue, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Klue, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Klue](#creating-a-klue-test-user)**  – Pokud chcete mít protějšek Britta Simon v Klue propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Klue.

**Ke konfiguraci Azure AD jednotné přihlašování s Klue, proveďte následující kroky:**

1. Na portálu Azure na **Klue** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_klue_samlbase.png)

3. Na **Klue domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_klue_url1.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `urn:klue:<Customer ID>`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://app.klue.com/account/auth/saml/<Customer UUID>/callback`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL**. Pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_klue_url2.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://app.klue.com/account/auth/saml/<Customer UUID>/`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte s skutečná adresa URL odpovědi, identifikátor a přihlašovací adresa URL. Obraťte se na [tým podpory Klue klienta](mailto:support@klue.com) k získání těchto hodnot.

5. Aplikace Klue očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. 

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu      | Hodnota atributu      |
    | ------------------- | -------------------- |
    | křestní_jméno          | user.givenname |
    | Příjmení           | user.surname |
    | e-mail               | user.userprincipalname|
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_attribute_04.png)

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_klue_certificate.png) 

8. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_general_400.png)
    
9. Na **Klue konfigurace** klikněte na tlačítko **konfigurace Klue** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_klue_configure.png) 

10. Konfigurace jednotného přihlašování na **Klue** straně, budete muset odeslat stažené **Certificate(Base64) SAML jeden přihlašování adresa URL služby a SAML Entity ID** k [tým podpory Klue](mailto:support@klue.com).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/klue-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/klue-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/klue-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/klue-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-klue-test-user"></a>Vytvoření zkušebního uživatele Klue

Cílem této části je vytvoření uživatele v Klue nazývá Britta Simon. Klue podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Klue, pokud ještě neexistuje.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Klue](mailto:support@klue.com).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Klue.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Klue, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Klue**.

    ![Konfigurovat jednotné přihlašování](./media/klue-tutorial/tutorial_klue_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Klue na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Klue.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/klue-tutorial/tutorial_general_01.png
[2]: ./media/klue-tutorial/tutorial_general_02.png
[3]: ./media/klue-tutorial/tutorial_general_03.png
[4]: ./media/klue-tutorial/tutorial_general_04.png

[100]: ./media/klue-tutorial/tutorial_general_100.png

[200]: ./media/klue-tutorial/tutorial_general_200.png
[201]: ./media/klue-tutorial/tutorial_general_201.png
[202]: ./media/klue-tutorial/tutorial_general_202.png
[203]: ./media/klue-tutorial/tutorial_general_203.png

