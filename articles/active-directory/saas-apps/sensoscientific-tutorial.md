---
title: 'Kurz: Integrace Azure Active Directory systému SensoScientific bezdrátové teploty monitorování | Dokumentace Microsoftu'
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
ms.openlocfilehash: de46f3ded72b8f4444426b8754b1c1a14863321f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045875"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Kurz: Integrace Azure Active Directory s SensoScientific bezdrátové teploty monitorování systému

V tomto kurzu se dozvíte, jak integrovat SensoScientific bezdrátové teploty monitorování systému s Azure Active Directory (Azure AD).

Integrace SensoScientific bezdrátové teploty monitorování systému s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k SensoScientific bezdrátové teploty monitorování systému
- Uživatele, aby automaticky získat přihlášení k SensoScientific bezdrátové teploty monitorování systému (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SensoScientific bezdrátové teploty monitorování systému, potřebujete následující položky:

- S předplatným služby Azure AD
- SensoScientific bezdrátové teploty monitorování systému jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SensoScientific bezdrátové teploty monitorování systému z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Přidání SensoScientific bezdrátové teploty monitorování systému z Galerie
Ke konfiguraci integrace SensoScientific bezdrátové teploty monitorování systému do služby Azure AD, budete muset přidat SensoScientific bezdrátové teploty monitorování systému z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat SensoScientific bezdrátové teploty monitorování systému z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **SensoScientific bezdrátové teploty monitorování systému**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. Na panelu výsledků vyberte **SensoScientific bezdrátové teploty monitorování systému**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systém založený na uživateli test "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek SensoScientific bezdrátové teploty monitorování systému je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské SensoScientific bezdrátové teploty monitorování systému je potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** SensoScientific bezdrátové teploty monitorování systému.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SensoScientific bezdrátové teploty monitorování systému](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  – Pokud chcete mít protějšek Britta Simon SensoScientific bezdrátové teploty monitorování systému, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci SensoScientific bezdrátové teploty monitorování systému.

**Ke konfiguraci Azure AD jednotné přihlašování s SensoScientific bezdrátové teploty monitorování systému, proveďte následující kroky:**

1. Na webu Azure Portal na **SensoScientific bezdrátové teploty monitorování systému** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. Na **SensoScientific bezdrátové teploty monitorování systému domény a adresy URL** části, nemusíte provádět žádné kroky jako aplikace je už předem integrované se službou Azure:

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_general_400.png)

6. Na **SensoScientific bezdrátové teploty monitorování System Configuration** klikněte na tlačítko **konfigurovat systém monitorování SensoScientific bezdrátové teploty** otevřete **konfigurace přihlašování** okna. Kopírování **URL odhlašování, SAML Entity ID** a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. Přihlaste se k aplikaci SensoScientific bezdrátové teploty monitorování systému jako správce.

8. V navigační nabídce v horní části klikněte na tlačítko **konfigurace** a goto **konfigurovat** pod **Single Sign On** otevřete nastavení jednotného přihlašování.

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. V **nastavení jednotného přihlašování** formuláři proveďte následující kroky:
 
    a. Vyberte **název vystavitele** jako Azure AD.
    
    b. Vložit **SAML Entity ID** který jste zkopírovali z portálu Azure portal do textového pole URL vystavitele.
    
    c. Vložit **SAML jednotné přihlašování – adresa URL služby** který jste zkopírovali z portálu Azure portal do textového pole jednotné přihlašování – adresa URL služby.

    d. Vložit **odhlašování URL** který jste zkopírovali z portálu Azure portal do textového pole adresu URL jednotného odhlašování služby.

    e. Vyhledejte certifikát, který jste stáhli z webu Azure portal a nahrajte ji sem.
    
    f. Klikněte na **Uložit**.
  
> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/sensoscientific-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/sensoscientific-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sensoscientific-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/sensoscientific-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Vytvoření zkušebního uživatele SensoScientific bezdrátové teploty monitorování systému

Povolit Azure AD uživatelům přihlášení SensoScientific bezdrátové teploty monitorování systému, musí být poskytnuty do SensoScientific bezdrátové teploty monitorování systému. Práce s [tým podpory SensoScientific bezdrátové teploty monitorování systému](https://www.sensoscientific.com/contact-us/) přidat uživatele na platformě SensoScientific bezdrátové teploty monitorování systému. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k SensoScientific bezdrátové teploty monitorování systému.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon SensoScientific bezdrátové teploty monitorování systému, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SensoScientific bezdrátové teploty monitorování systému**.

    ![Konfigurace jednotného přihlašování](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu. Kliknutím na dlaždici SensoScientific bezdrátové teploty monitorování systému, na přístupovém panelu, je bude možné automaticky přihlášení k aplikaci SensoScientific bezdrátové teploty monitorování systému. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
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

