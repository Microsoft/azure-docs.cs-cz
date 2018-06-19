---
title: 'Kurz: Azure Active Directory integrace s tlačí se k | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tlačí se k.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: b3771701586eb06d852e5a4541d184eaef589465
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903333"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Kurz: Azure Active Directory integrace s tlačí se k

V tomto kurzu zjistěte, jak integrovat tlačí se k službě Azure Active Directory (Azure AD).

Integrace tlačí se k službě Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k tlačí se k
- Můžete povolit uživatelům, aby automaticky získat přihlášení k tlačí se k (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li nakonfigurovat tlačí se k integraci Azure AD, potřebujete následující položky:

- Předplatné služby Azure AD
- Tlačí se k jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tlačí se k z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-huddle-from-the-gallery"></a>Přidání tlačí se k z Galerie
Chcete-li nakonfigurovat integraci tlačí se k do služby Azure AD, přidejte tlačí se k z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat tlačí se k z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **tlačí se k**.

    ![Vytváření testovacího uživatele Azure AD](./media/huddle-tutorial/tutorial_huddle_search.png)

5. Na panelu výsledků vyberte **tlačí se k**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s tlačí se k podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v tlačí se k je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v tlačí se k musí navázat.

V tlačí se k, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s tlačí se k, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.

2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.

3. **[Vytvoření zkušebního uživatele tlačí se k](#creating-a-huddle-test-user)**  – Pokud chcete mít protějšek Britta Simon v tlačí se k propojeném s Azure AD reprezentace daného uživatele.

4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.

5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v tlačí se k aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s tlačí se k, proveďte následující kroky:**

1. Na portálu Azure na **tlačí se k** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/huddle-tutorial/tutorial_huddle_samlbase.png)

3. Na **tlačí se k doméně a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/huddle-tutorial/tutorial_huddle_url.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://<company name>.huddle.com`

    > [!NOTE] 
    > Tato hodnota není skutečné. Aktualizujte tuto hodnotu s skutečná adresa URL přihlašování. Obraťte se na [tým podpory tlačí se k klienta](https://huddle.zendesk.com) získat tuto hodnotu. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/huddle-tutorial/tutorial_huddle_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/huddle-tutorial/tutorial_general_400.png)

6. Na **tlačí se k konfigurace** klikněte na tlačítko **konfigurace tlačí se k** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.** 

    ![Konfigurovat jednotné přihlašování](./media/huddle-tutorial/tutorial_huddle_configure.png) 
    
7. Na straně tlačí se k nakonfigurovat jednotné přihlašování, budete muset odeslat stažené **certifikát**, **SAML jeden přihlašování adresa URL služby**, a **SAML Entity ID** k [tým podpory tlačí se k klienta](https://huddle.zendesk.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.  
   
    >[!NOTE]
    > Jednotné přihlašování musí být povolena tlačí se k tým podpory. Po dokončení konfigurace zobrazí oznámení. 
    > 

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
   
### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/huddle-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/huddle-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/huddle-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-huddle-test-user"></a>Vytvoření zkušebního uživatele tlačí se k

Pokud chcete povolit uživatelům přihlášení do tlačí se k Azure AD, musí být zřízená do tlačí se k. V případě tlačí se k zřizování je ruční úloha.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **tlačí se k** společnosti lokality jako správce.
2. Klikněte na tlačítko **prostoru**.
3. Klikněte na tlačítko **osoby \> pozvat uživatele**.
   
   ![Lidé](./media/huddle-tutorial/IC787838.png "osoby")

4. V **vytvořit nová pozvánka** část, proveďte následující kroky:
   
   ![Nová Pozvánka](./media/huddle-tutorial/IC787839.png "nová pozvánka")
   
   a. V **vyberte tým pro pozvat** seznamu, vyberte **team**.

   b. Typ **e-mailovou adresu** platný Azure AD účet chcete zřídit v k **zadejte e-mailová adresa osoby, které byste chtěli pozvat** textové pole.

   c. Klikněte na tlačítko **pozvat**.   
   
    >[!NOTE]
    > Držitel účtu Azure AD budou obdrží e-mail zahrnutím odkazu pro potvrzení účtu před stane aktivní. 
    > 

>[!NOTE]
>Ostatní tlačí se k nástroje pro tvorbu účet uživatele nebo rozhraní API poskytovaných tlačí se k můžete použít ke zřízení uživatelských účtů Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v tlačí se k udělení přístupu.

![Přiřadit uživatele][200] 

**Chcete-li tlačí se k přiřadit Britta Simon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **tlačí se k**.

    ![Konfigurovat jednotné přihlašování](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici tlačí se k na přístupovém panelu, měli byste obdržet automaticky přihlašovací stránku tlačí se k aplikaci.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
