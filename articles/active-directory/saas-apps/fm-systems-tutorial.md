---
title: 'Kurz: Azure Active Directory integrace s FM:Systems | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FM:Systems.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f78c58c5-6e98-458b-8991-78624a245665
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: d089e35e28c466f91c550a41898731f683bf5033
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216327"
---
# <a name="tutorial-azure-active-directory-integration-with-fmsystems"></a>Kurz: Azure Active Directory integrace s FM:Systems

V tomto kurzu zjistěte, jak integrovat FM:Systems s Azure Active Directory (Azure AD).

Integrace FM:Systems s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FM:Systems
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FM:Systems (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FM:Systems, potřebujete následující položky:

- Předplatné služby Azure AD
- FM:Systems jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FM:Systems z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-fmsystems-from-the-gallery"></a>Přidání FM:Systems z Galerie
Při konfiguraci integrace FM:Systems do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS FM:Systems z galerie.

**Pokud chcete přidat FM:Systems z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **FM:Systems**.

    ![Vytváření testovacího uživatele Azure AD](./media/fm-systems-tutorial/tutorial_fmsystems_search.png)

5. Na panelu výsledků vyberte **FM:Systems**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/fm-systems-tutorial/tutorial_fmsystems_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s FM:Systems podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v FM:Systems je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v FM:Systems musí navázat.

V FM:Systems, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FM:Systems, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváření testovacího uživatele FM:Systems](#creating-an-fmsystems-test-user)**  – Pokud chcete mít protějšek Britta Simon v FM:Systems propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci FM:Systems.

**Ke konfiguraci Azure AD jednotné přihlašování s FM:Systems, proveďte následující kroky:**

1. Na portálu Azure na **FM:Systems** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/fm-systems-tutorial/tutorial_fmsystems_samlbase.png)

3. Na **FM:Systems domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/fm-systems-tutorial/tutorial_fmsystems_url.png)

    V **adresa URL odpovědi** textovému poli, zadejte vaše FM:Systems **adresa URL odpovědi**, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.fmshosted.com/fminteract/ConsumerService2.aspx`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL odpovědi. Obraťte se na [tým podpory FM:Systems](https://fmsystems.com/ask-us/) získat tuto hodnotu.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/fm-systems-tutorial/tutorial_fmsystems_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/fm-systems-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **FM:Systems** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory FM:Systems](https://fmsystems.com/ask-us/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách. Zobrazí se oznámení, když bylo povoleno jednotné přihlašování pro vaše předplatné.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/fm-systems-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/fm-systems-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/fm-systems-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/fm-systems-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-an-fmsystems-test-user"></a>Vytváření testovacího uživatele FM:Systems

1. V okně webového prohlížeče Přihlaste se jako správce k serveru vaší společnosti FM:Systems.

2. Přejděte na **systému správy \> spravovat zabezpečení \> uživatelé \> seznam uživatelů**.
   
    ![Správa systému](./media/fm-systems-tutorial/ic795905.png "správu systému")

3. Klikněte na tlačítko **vytvořit nového uživatele**.
   
    ![Vytvoření nového uživatele](./media/fm-systems-tutorial/ic795906.png "vytvořit nového uživatele")

4. V **vytvořit uživatele** část, proveďte následující kroky:
   
    ![Vytvoření uživatele](./media/fm-systems-tutorial/ic795907.png "vytvoření uživatele")
   
    a. Typ **uživatelské jméno**, **heslo**, **potvrzení hesla**, **e-mailu** a **ID zaměstnance** z platný účet služby Azure Active Directory, který má být zahrnuty do související textových polí.
   
    b. Klikněte na **Další**.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon chcete použít Azure jednotného přihlašování k udělení přístupu k FM:Systems.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon FM:Systems, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **FM:Systems**.

    ![Konfigurovat jednotné přihlašování](./media/fm-systems-tutorial/tutorial_fmsystems_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici FM:Systems na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci FM:Systems.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/fm-systems-tutorial/tutorial_general_01.png
[2]: ./media/fm-systems-tutorial/tutorial_general_02.png
[3]: ./media/fm-systems-tutorial/tutorial_general_03.png
[4]: ./media/fm-systems-tutorial/tutorial_general_04.png

[100]: ./media/fm-systems-tutorial/tutorial_general_100.png

[200]: ./media/fm-systems-tutorial/tutorial_general_200.png
[201]: ./media/fm-systems-tutorial/tutorial_general_201.png
[202]: ./media/fm-systems-tutorial/tutorial_general_202.png
[203]: ./media/fm-systems-tutorial/tutorial_general_203.png

