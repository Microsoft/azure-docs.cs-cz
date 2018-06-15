---
title: 'Kurz: Azure Active Directory integrace s Tichomoří rozvrhu | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a časových rozvrhů Tichomoří.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e546e8ba-821a-4942-9545-c84b0670beab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 2f29c041c5464277937594a45b816fef395d04e6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345856"
---
# <a name="tutorial-azure-active-directory-integration-with-pacific-timesheet"></a>Kurz: Azure Active Directory integrace s Tichomoří časových rozvrhů

V tomto kurzu zjistěte, jak integrovat Tichomoří rozvrhu s Azure Active Directory (Azure AD).

Integrace Tichomoří rozvrhu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Tichomoří časových rozvrhů
- Můžete povolit uživatelům, aby automaticky získat přihlášení k časových rozvrhů Tichomoří (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tichomoří rozvrhu, potřebujete následující položky:

- Předplatné služby Azure AD
- Časových rozvrhů Tichomoří jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Tichomoří rozvrhu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pacific-timesheet-from-the-gallery"></a>Přidání Tichomoří rozvrhu z Galerie
Při konfiguraci integrace Tichomoří rozvrhu do služby Azure AD, potřebujete přidat Tichomoří rozvrhu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Tichomoří rozvrhu z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Tichomoří rozvrhu**.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_search.png)

5. Na panelu výsledků vyberte **Tichomoří rozvrhu**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části konfiguraci a testování Azure AD jednotné přihlašování pomocí časových rozvrhů Tichomoří podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Tichomoří rozvrhu je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Tichomoří rozvrhu musí navázat.

V časových rozvrhů Tichomoří, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tichomoří rozvrhu, musíte dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Tichomoří rozvrhu](#creating-a-pacific-timesheet-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tichomoří rozvrhu, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Tichomoří rozvrhu.

**Ke konfiguraci Azure AD jednotné přihlašování s Tichomoří rozvrhu, proveďte následující kroky:**

1. Na portálu Azure na **Tichomoří rozvrhu** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_samlbase.png)

3. Na **Tichomoří časových rozvrhů domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<InstanceID>.pacifictimesheet.com/timesheet/home.do`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory Tichomoří rozvrhu](http://www.pacifictimesheet.com/support) k získání těchto hodnot.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_400.png)

6. Na **konfigurace časových rozvrhů Tichomoří** klikněte na tlačítko **konfigurace časových rozvrhů Tichomoří** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_configure.png) 

7. Konfigurace jednotného přihlašování na **Tichomoří rozvrhu** straně, budete muset odeslat stažený **certifikátu (Base64)**, **SAML jeden přihlašování adresa URL služby**, a **SAML Entity ID** k [tým podpory Tichomoří rozvrhu](http://www.pacifictimesheet.com/support). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/active-directory-saas-pacific-timesheet-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-pacific-timesheet-test-user"></a>Vytvoření zkušebního uživatele Tichomoří časových rozvrhů

V této části vytvoříte uživatele volat Britta Simon v Tichomoří rozvrhu. Práce s [tým podpory Tichomoří rozvrhu](http://www.pacifictimesheet.com/support) vytvoření uživatele v aplikaci.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon k používání Azure jednotné přihlašování v časových rozvrhů Tichomoří udělení přístupu.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Tichomoří rozvrhu, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Tichomoří rozvrhu**.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_pacifictimesheet_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

Cílem této části je Azure AD jeden přihlašování konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Tichomoří rozvrhu na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Tichomoří rozvrhu.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pacific-timesheet-tutorial/tutorial_general_203.png

