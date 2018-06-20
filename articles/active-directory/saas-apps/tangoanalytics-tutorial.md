---
title: 'Kurz: Azure Active Directory integrace s Tango Analytics | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tango analýzy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2f7555d3-e9ba-40b2-9b3a-2f0ab38a4c08
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: e72429eb8aadb4749bc19a4536972bfe6006769b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229423"
---
# <a name="tutorial-azure-active-directory-integration-with-tango-analytics"></a>Kurz: Azure Active Directory integrace s Tango Analytics

V tomto kurzu zjistěte, jak integrovat Tango Analytics s Azure Active Directory (Azure AD).

Integrace s Azure AD Tango Analytics poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k analýze Tango
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k analýze Tango (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Tango Analytics, potřebujete následující položky:

- Předplatné služby Azure AD
- Analýzy Tango jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Tango Analytics z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-tango-analytics-from-the-gallery"></a>Přidání Tango Analytics z Galerie
Při konfiguraci integrace Tango Analytics do služby Azure AD, potřebujete přidat Tango Analytics z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Tango Analytics z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tango Analytics**.

    ![Vytváření testovacího uživatele Azure AD](./media/tangoanalytics-tutorial/tutorial_tangoanalytics_search.png)

5. Na panelu výsledků vyberte **Tango Analytics**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/tangoanalytics-tutorial/tutorial_tangoanalytics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tango Analytics podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v Tango Analytics je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Tango Analytics musí navázat.

V Tango Analytics přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tango Analytics, budete muset provést následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Tango Analytics](#creating-a-tango-analytics-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tango analýz, které je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Tango Analytics.

**Ke konfiguraci Azure AD jednotné přihlašování s Tango Analytics, proveďte následující kroky:**

1. Na portálu Azure na **Tango Analytics** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/tangoanalytics-tutorial/tutorial_tangoanalytics_samlbase.png)

3. Na **Tango Analytics domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/tangoanalytics-tutorial/tutorial_tangoanalytics_url.png)

    a. V **identifikátor** textovému poli, zadejte hodnotu `TACORE_SSO`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://mts.tangoanalytics.com/saml2/sp/acs/post`

    > [!NOTE] 
    > Adresa URL odpovědi hodnota není skutečné. Aktualizujte s skutečná adresa URL odpovědi. Obraťte se na [Tango Analytics podporovat team](mailto:support@tangoanalytics.com) získat tuto hodnotu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Konfigurovat jednotné přihlašování](./media/tangoanalytics-tutorial/tutorial_tangoanalytics_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/tangoanalytics-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **Tango Analytics** straně, budete muset odeslat stažené **soubor XML s metadaty** k [Tango Analytics podporovat team](mailto:support@tangoanalytics.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/tangoanalytics-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/tangoanalytics-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/tangoanalytics-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/tangoanalytics-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-tango-analytics-test-user"></a>Vytvoření zkušebního uživatele Tango Analytics

V této části vytvoříte uživatele volal Britta Simon v Tango Analytics. Práce s [Tango Analytics podporovat team](mailto:support@tangoanalytics.com) přidat uživatele do Tango Analytics platform. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k analýze Tango.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Tango analýzy, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tango Analytics**.

    ![Konfigurovat jednotné přihlašování](./media/tangoanalytics-tutorial/tutorial_tangoanalytics_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Tango Analytics na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Tango Analytics.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/tangoanalytics-tutorial/tutorial_general_01.png
[2]: ./media/tangoanalytics-tutorial/tutorial_general_02.png
[3]: ./media/tangoanalytics-tutorial/tutorial_general_03.png
[4]: ./media/tangoanalytics-tutorial/tutorial_general_04.png

[100]: ./media/tangoanalytics-tutorial/tutorial_general_100.png

[200]: ./media/tangoanalytics-tutorial/tutorial_general_200.png
[201]: ./media/tangoanalytics-tutorial/tutorial_general_201.png
[202]: ./media/tangoanalytics-tutorial/tutorial_general_202.png
[203]: ./media/tangoanalytics-tutorial/tutorial_general_203.png

