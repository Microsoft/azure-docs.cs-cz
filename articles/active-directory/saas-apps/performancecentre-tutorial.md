---
title: 'Kurz: Azure Active Directory integrace s PerformanceCentre | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 1a9935dbb0ec43c1eb2ec78fdd7caa92b69e3784
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214157"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Kurz: Azure Active Directory integrace s PerformanceCentre

V tomto kurzu zjistěte, jak integrovat PerformanceCentre s Azure Active Directory (Azure AD).

Integrace PerformanceCentre s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k PerformanceCentre
- Můžete povolit uživatelům, aby automaticky získat přihlášení k PerformanceCentre (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s PerformanceCentre, potřebujete následující položky:

- Předplatné služby Azure AD
- PerformanceCentre jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání PerformanceCentre z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-performancecentre-from-the-gallery"></a>Přidání PerformanceCentre z Galerie
Při konfiguraci integrace PerformanceCentre do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS PerformanceCentre z galerie.

**Pokud chcete přidat PerformanceCentre z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **PerformanceCentre**.

    ![Vytváření testovacího uživatele Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_search.png)

5. Na panelu výsledků vyberte **PerformanceCentre**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s PerformanceCentre podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v PerformanceCentre je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v PerformanceCentre musí navázat.

V PerformanceCentre, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s PerformanceCentre, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele PerformanceCentre](#creating-a-performancecentre-test-user)**  – Pokud chcete mít protějšek Britta Simon v PerformanceCentre propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci PerformanceCentre.

**Ke konfiguraci Azure AD jednotné přihlašování s PerformanceCentre, proveďte následující kroky:**

1. Na portálu Azure na **PerformanceCentre** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. Na **PerformanceCentre domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://companyname.performancecentre.com/saml/SSO`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory PerformanceCentre klienta](https://www.performancecentre.com/contact-us/) k získání těchto hodnot. 

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/performancecentre-tutorial/tutorial_general_400.png)

6. Na **PerformanceCentre konfigurace** klikněte na tlačítko **konfigurace PerformanceCentre** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Přihlášení k vaší **PerformanceCentre** společnosti lokality jako správce.

8. Na kartě na levé straně klikněte na tlačítko **konfigurace**.
   
    ![Azure AD jednotné přihlášení][10]

9. Na kartě na levé straně klikněte na tlačítko **různé**a potom klikněte na **jednotné přihlašování**.
   
    ![Azure AD jednotné přihlášení][11]

10. Jako **protokol**, vyberte **SAML**.
   
    ![Azure AD jednotné přihlášení][12]

11. V poznámkovém bloku otevřete váš soubor stažený metadata, kopírovat obsah, vložte jej do **metadat zprostředkovatelů Identity** textovému poli a potom klikněte na **Uložit**.
   
    ![Azure AD jednotné přihlášení][13]

12. Ověřte, že hodnoty **Entity základní adresa URL** a **Entity ID URL** jsou správné.
    
     ![Azure AD jednotné přihlášení][14]

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/performancecentre-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/performancecentre-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/performancecentre-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/performancecentre-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-performancecentre-test-user"></a>Vytvoření zkušebního uživatele PerformanceCentre

Cílem této části je vytvoření uživatele v PerformanceCentre nazývá Britta Simon.

**Vytvoření uživatele v PerformanceCentre nazývá Britta Simon, proveďte následující kroky:**

1. Přihlásit se k serveru vaší společnosti PerformanceCentre jako správce.

2. V nabídce na levé straně klikněte na tlačítko **Interrelate**a potom klikněte na **vytvořit účastník**.
   
    ![Vytvořit uživatele][400]

3. Na **vztah mezi – vytvoření účastník** dialogové okno, proveďte následující kroky:
   
    ![Vytvořit uživatele][401]
    
    a. Zadejte povinné atributy pro Britta Simon do související textových polí.
    
    >[!IMPORTANT]
    >Atribut Britta na uživatelské jméno v PerformanceCentre musí být stejné jako uživatelské jméno ve službě Azure AD.
    
    b. Vyberte **správce klienta** jako **zvolte roli**.
    
    c. Klikněte na **Uložit**. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu PerformanceCentre.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon PerformanceCentre, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **PerformanceCentre**.

    ![Konfigurovat jednotné přihlašování](./media/performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.  

Když kliknete na dlaždici PerformanceCentre na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci PerformanceCentre.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png

