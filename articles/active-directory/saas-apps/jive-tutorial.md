---
title: 'Kurz: Integrace Azure Active Directory s Jive | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f6155f348a8e5698c9c7792f45536c94f6ead81
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203943"
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Kurz: Integrace Azure Active Directory s Jive

V tomto kurzu se dozvíte, jak integrovat Jive s Azure Active Directory (Azure AD).

Jive integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Jive
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Jive (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jive, potřebujete následující položky:

- Předplatné Azure AD
- Jive jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Jive z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-jive-from-the-gallery"></a>Přidání Jive z Galerie
Konfigurace integrace Jive do služby Azure AD, budete muset přidat Jive z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Jive z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Jive**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jive-tutorial/tutorial_jive_search.png)

1. Na panelu výsledků vyberte **Jive**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jive-tutorial/tutorial_jive_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování s Jive podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Jive je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Jive potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Jive.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jive, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Jive](#creating-a-jive-test-user)**  – Pokud chcete mít protějšek Britta Simon Jive, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Jive.

**Ke konfiguraci Azure AD jednotné přihlašování s Jive, proveďte následující kroky:**

1. Na webu Azure Portal na **Jive** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/jive-tutorial/tutorial_jive_samlbase.png)

1. Na **Jive domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/jive-tutorial/tutorial_jive_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<instance name>.jivecustom.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<instance name>.jiveon.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Jive klienta](https://www.jivesoftware.com/services-support/) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/jive-tutorial/tutorial_jive_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/jive-tutorial/tutorial_general_400.png)

1. Ke konfiguraci jednotného přihlašování na **Jive** straně přihlašování k vašemu tenantovi Jive jako správce.

1. V nabídce v horní části klikněte na tlačítko "**Saml**."

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/jive-tutorial/tutorial_jive_002.png)

    a. Vyberte **povolené** pod **Obecné** kartu b. Klikněte "**uložit všechna nastavení saml**" tlačítko.

1. Přejděte "**metadat zprostředkovatele identity**" kartu.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/jive-tutorial/tutorial_jive_003.png)

    a. Zkopírujte obsah stažený metadata souboru XML a vložte jej do **metadat zprostředkovatele Identity (IDP)** textového pole.

    b. Klikněte "**uložit všechna nastavení saml**" tlačítko.

1. Přejděte "**mapování atributů uživatele**" kartu.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/jive-tutorial/tutorial_jive_004.png)

    a. V **e-mailu** textového pole zkopírujte a vložte název **e-mailu** hodnotu.

    b. V **křestní jméno** textového pole zkopírujte a vložte název **givenname** hodnotu.

    c. V **příjmení** textového pole zkopírujte a vložte název **příjmení** hodnotu.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jive-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jive-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/jive-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/jive-tutorial/create_aaduser_04.png)

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-jive-test-user"></a>Vytvoření zkušebního uživatele Jive

Cílem této části je vytvořte uživatele Britta Simon v Jive. Jive podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](jive-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

Pokud je potřeba ručně vytvořit uživatele, pracovat s [tým podpory Jive klienta](https://www.jivesoftware.com/services-support/) přidat uživatele na platformě Jive.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Jive použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Jive Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Jive**.

    ![Konfigurace jednotného přihlašování](./media/jive-tutorial/tutorial_jive_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Jive na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Jive.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](jive-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/jive-tutorial/tutorial_general_01.png
[2]: ./media/jive-tutorial/tutorial_general_02.png
[3]: ./media/jive-tutorial/tutorial_general_03.png
[4]: ./media/jive-tutorial/tutorial_general_04.png

[100]: ./media/jive-tutorial/tutorial_general_100.png

[200]: ./media/jive-tutorial/tutorial_general_200.png
[201]: ./media/jive-tutorial/tutorial_general_201.png
[202]: ./media/jive-tutorial/tutorial_general_202.png
[203]: ./media/jive-tutorial/tutorial_general_203.png
