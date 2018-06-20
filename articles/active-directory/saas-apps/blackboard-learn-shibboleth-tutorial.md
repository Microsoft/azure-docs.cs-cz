---
title: 'Kurz: Azure Active Directory integrace s další Tabule - Shibboleth | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a další Tabule - Shibboleth.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e435cbb4-c0f0-400e-943c-5c923fa8ddf2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: jeedes
ms.openlocfilehash: 5c7f8fb213359da948b4b6d92333bf68935815db
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227461"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Kurz: Azure Active Directory integrace s další Tabule - Shibboleth

V tomto kurzu zjistěte, jak integrovat Tabule další - Shibboleth s Azure Active Directory (Azure AD).

Integrace Tabule další - Shibboleth s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k další Tabule - Shibboleth
- Můžete povolit uživatelům, aby automaticky získat přihlášeného Tabule další - Shibboleth (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s další Tabule - Shibboleth, potřebujete následující položky:

- Předplatné služby Azure AD
- Další tabule - Shibboleth jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání další Tabule - Shibboleth z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Přidání další Tabule - Shibboleth z Galerie
Konfigurace integrace Tabule další - Shibboleth do služby Azure AD, potřebujete přidat další Tabule - Shibboleth z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat další Tabule - Shibboleth z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tabule další - Shibboleth**.

    ![Vytváření testovacího uživatele Azure AD](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_search.png)

5. Na panelu výsledků vyberte **Tabule další - Shibboleth**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s další Tabule – Shibboleth podle testovacího uživatele názvem "Britta Simon."

Pro jednotné přihlašování pro práci Azure AD je potřeba vědět, jaké příslušného uživatele v další Tabule – Shibboleth je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Tabule další - Shibboleth musí navázat.

V další Tabule - Shibboleth, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s další Tabule - Shibboleth, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření Tabule další - Shibboleth testovacího uživatele](#creating-a-blackboard-learn---shibboleth-test-user)**  – Pokud chcete mít protějšek Britta Simon v další Tabule - Shibboleth propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v vaší Tabule další - Shibboleth aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s další Tabule - Shibboleth, proveďte následující kroky:**

1. Na portálu Azure na **Tabule další - Shibboleth** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_samlbase.png)

3. Na **Tabule další - Shibboleth domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`
 
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [Tabule další - tým podpory Shibboleth klienta](https://www.blackboard.com/forms/contact-us_form.aspx) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/blackboard-learn-shibboleth-tutorial/tutorial_general_400.png)
    
6. Na **Tabule další - konfigurace Shibboleth** klikněte na tlačítko **nakonfigurovat další Tabule - Shibboleth** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_configure.png) 

7. Konfigurace jednotného přihlašování na **Tabule další - Shibboleth** straně, budete muset odeslat stažené **soubor XML s metadaty** a **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [Tabule další - tým podpory Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx).

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/blackboard-learn-shibboleth-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-blackboard-learn---shibboleth-test-user"></a>Vytváření Tabule další - Shibboleth testovacího uživatele

V této části vytvoříte volal Britta Simon v další Tabule - Shibboleth uživatele. Práce s vaší [Tabule další - tým podpory Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) přidat uživatele do další Tabule - Shibboleth platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v další Tabule - Shibboleth udělení přístupu.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon Tabule další - Shibboleth, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tabule další - Shibboleth**.

    ![Konfigurovat jednotné přihlašování](./media/blackboard-learn-shibboleth-tutorial/tutorial_blackboardlearn-shibboleth_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Po kliknutí na tlačítko Další tabule - Shibboleth dlaždice na přístupovém panelu jste měli získat automaticky přihlášení k vaší Tabule další - Shibboleth aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_01.png
[2]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_02.png
[3]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_03.png
[4]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_04.png

[100]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_100.png

[200]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_200.png
[201]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_201.png
[202]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_202.png
[203]: ./media/blackboard-learn-shibboleth-tutorial/tutorial_general_203.png

