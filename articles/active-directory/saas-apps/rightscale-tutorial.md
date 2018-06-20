---
title: 'Kurz: Azure Active Directory integrace s Rightscale | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Rightscale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3a8d376d-95fb-4dd7-832a-4fdd4dd7c87c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: a78f3eb0de9fd874f8f90d3773355e59896a6bcd
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217523"
---
# <a name="tutorial-azure-active-directory-integration-with-rightscale"></a>Kurz: Azure Active Directory integrace s Rightscale

V tomto kurzu zjistěte, jak integrovat Rightscale s Azure Active Directory (Azure AD).

Integrace Rightscale s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Rightscale
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Rightscale (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Rightscale, potřebujete následující položky:

- Předplatné služby Azure AD
- Rightscale jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Rightscale z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-rightscale-from-the-gallery"></a>Přidání Rightscale z Galerie
Při konfiguraci integrace Rightscale do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Rightscale z galerie.

**Pokud chcete přidat Rightscale z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Rightscale**.

    ![Vytváření testovacího uživatele Azure AD](./media/rightscale-tutorial/tutorial_rightscale_search.png)

5. Na panelu výsledků vyberte **Rightscale**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/rightscale-tutorial/tutorial_rightscale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Rightscale podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Rightscale je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Rightscale musí navázat.

V Rightscale, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Rightscale, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Rightscale](#creating-a-rightscale-test-user)**  – Pokud chcete mít protějšek Britta Simon v Rightscale propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Rightscale.

**Ke konfiguraci Azure AD jednotné přihlašování s Rightscale, proveďte následující kroky:**

1. Na portálu Azure na **Rightscale** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_samlbase.png)

3. Na **Rightscale domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP iniciované režimu** není nutné provádět žádné kroky, protože aplikace je již předem integrované se službou Azure.

    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_url.png)

4. Na **Rightscale domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **SP iniciované režimu**, proveďte následující kroky:
    
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_url1.png)

    a. Klikněte na **zobrazit upřesňující nastavení adresy URL**.

    b. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL: `https://login.rightscale.com/`

5. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_general_400.png)

7. Na **Rightscale konfigurace** klikněte na tlačítko **konfigurace Rightscale** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_configure.png) 
<CS>
8. Pokud chcete získat jednotné přihlašování, které jsou nakonfigurované pro vaši aplikaci, musíte k přihlašování ke klientovi RightScale jako správce.

    a. V nabídce v horní části, klikněte **nastavení** a vyberte **jednotné přihlašování**.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_001.png) 

    b. Klikněte "**nové**" tlačítko Přidat **vaše zprostředkovatelů Identity SAML**.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_002.png) 
 
    c. Do textového pole z **zobrazovaný název**, zadejte název vaší společnosti.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_003.png)
 
    d. Vyberte **spouštěná RightScale povolit jednotné přihlašování pomocí zjišťování nápovědu** a vstupní vaše **název domény** v pod textové pole.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_004.png)

    e. Vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure do **koncového bodu jednotného přihlašování SAML** v RightScale.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_006.png)

    f. Vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure do **SAML EntityID** v RightScale.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_008.png)

    g. Klikněte na tlačítko **prohlížeče** tlačítko odeslat certifikát, který jste si stáhli z portálu Azure.
   
    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_009.png)

    h. Klikněte na **Uložit**.
<CE>
> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/rightscale-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/rightscale-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/rightscale-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/rightscale-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-rightscale-test-user"></a>Vytvoření zkušebního uživatele Rightscale

V této části vytvoříte volal Britta Simon v RightScale uživatele. Práce s [tým podpory Rightscale klienta](mailto:support@rightscale.com) přidat uživatele do RightScale platformy.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Rightscale.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Rightscale, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Rightscale**.

    ![Konfigurovat jednotné přihlašování](./media/rightscale-tutorial/tutorial_rightscale_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je testování konfigurace Azure AD jednotného přihlašování k použití na přístupovém panelu.  

Když kliknete na dlaždici RightScale na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci RightScale.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/rightscale-tutorial/tutorial_general_01.png
[2]: ./media/rightscale-tutorial/tutorial_general_02.png
[3]: ./media/rightscale-tutorial/tutorial_general_03.png
[4]: ./media/rightscale-tutorial/tutorial_general_04.png

[100]: ./media/rightscale-tutorial/tutorial_general_100.png

[200]: ./media/rightscale-tutorial/tutorial_general_200.png
[201]: ./media/rightscale-tutorial/tutorial_general_201.png
[202]: ./media/rightscale-tutorial/tutorial_general_202.png
[203]: ./media/rightscale-tutorial/tutorial_general_203.png

