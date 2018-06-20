---
title: 'Kurz: Azure Active Directory integrace s SensoScientific bezdrátové teploty monitorování systému | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SensoScientific bezdrátové teploty monitorování systému.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: bb0952fe5b78b0a9933cf7e9f20787150bdbcc3b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231504"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Kurz: Azure Active Directory integrace s SensoScientific bezdrátové teploty monitorování systému

V tomto kurzu zjistěte, jak integrovat SensoScientific bezdrátové teploty monitorování systému s Azure Active Directory (Azure AD).

Integrace s Azure AD SensoScientific bezdrátové teploty monitorování systému poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SensoScientific bezdrátové teploty monitorování systému
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SensoScientific bezdrátové teploty monitorování systému (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SensoScientific bezdrátové teploty monitorování systému, potřebujete následující položky:

- Předplatné služby Azure AD
- SensoScientific bezdrátové teploty monitorování systému jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SensoScientific bezdrátové teploty monitorování systému z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Přidání SensoScientific bezdrátové teploty monitorování systému z Galerie
Při konfiguraci integrace SensoScientific bezdrátové teploty monitorování systému do služby Azure AD, potřebujete přidat SensoScientific bezdrátové teploty monitorování systému z Galerie si na seznam spravovaných aplikací SaaS.

**Chcete-li přidat SensoScientific bezdrátové teploty monitorování systému z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **SensoScientific bezdrátové teploty monitorování systému**.

    ![Vytváření testovacího uživatele Azure AD](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. Na panelu výsledků vyberte **SensoScientific bezdrátové teploty monitorování systému**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SensoScientific bezdrátové teploty monitorování systému je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SensoScientific bezdrátové teploty monitorování systému, je nutné stanovit.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v SensoScientific bezdrátové teploty monitorování systému.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SensoScientific bezdrátové teploty monitorování systému](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  – Pokud chcete mít protějšek Britta Simon v SensoScientific bezdrátové teploty monitorování systému, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SensoScientific bezdrátové teploty monitorování systému.

**Ke konfiguraci Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému, proveďte následující kroky:**

1. Na portálu Azure na **SensoScientific bezdrátové teploty monitorování systému** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. Na **SensoScientific bezdrátové teploty monitorování systému domény a adresy URL** část, není nutné provádět žádné kroky, protože aplikace je již předem integrované se službou Azure:

    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. Na **SAML podpisový certifikát** klikněte na tlačítko **Certificate(Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_general_400.png)

6. Na **SensoScientific bezdrátové teploty monitorování System Configuration** klikněte na tlačítko **nakonfigurujte systém monitorování SensoScientific bezdrátové teploty** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID** a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. Přihlásit se k vaší aplikaci SensoScientific bezdrátové teploty monitorování systému jako správce.

8. V navigační nabídce v horní části, klikněte na tlačítko **konfigurace** a goto **konfigurace** pod **jednotné přihlašování** otevřete jeden znak na nastavení.

    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. V **jeden znak v nastavení** formuláři proveďte následující kroky:
 
    a. Vyberte **název vystavitele** jako Azure AD.
    
    b. Vložení **SAML Entity ID** který jste zkopírovali z portálu Azure do pole Adresa URL vystavitele.
    
    c. Vložení **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure do jednoho přihlášení adresa URL služby textového pole.

    d. Vložení **Sign-Out URL** který jste zkopírovali z portálu Azure do jedné adresy URL služby Sign-Out textové pole.

    e. Vyhledejte certifikát, který jste si stáhli z portálu Azure a sem odeslání.
    
    f. Klikněte na **Uložit**.
  
> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/sensoscientific-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/sensoscientific-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/sensoscientific-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/sensoscientific-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Vytvoření zkušebního uživatele SensoScientific bezdrátové teploty monitorování systému

Pokud chcete povolit uživatelům Azure AD přihlášení k SensoScientific bezdrátové teploty monitorování systému, musí být zřízená do SensoScientific bezdrátové teploty monitorování systému. Práce s [tým podpory SensoScientific bezdrátové teploty monitorování systému](https://www.sensoscientific.com/contact-us/) přidat uživatele do platformy SensoScientific bezdrátové teploty monitorování systému. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k SensoScientific bezdrátové teploty monitorování systému.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon SensoScientific bezdrátové teploty monitorování systému, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SensoScientific bezdrátové teploty monitorování systému**.

    ![Konfigurovat jednotné přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu. Klikněte na dlaždici SensoScientific bezdrátové teploty monitorování systému na přístupovém panelu, můžete se být automaticky přihlášení k aplikaci SensoScientific bezdrátové teploty monitorování systému. Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/sensoscientific-tutorial/tutorial_general_203.png

