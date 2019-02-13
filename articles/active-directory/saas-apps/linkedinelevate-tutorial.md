---
title: 'Kurz: Integrace Azure Active Directory s rozvoji Linkedinem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zvýšení oprávnění LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca8e537f261b59fb4e069d47d24e21abbdeca46
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202006"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Kurz: Integrace Azure Active Directory s rozvoji Linkedinem

V tomto kurzu se dozvíte, jak integrovat zvýšení Linkedinu s Azure Active Directory (Azure AD).

Integrace zvýšení Linkedinu s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k rozvoji Linkedinem
- Uživatele, aby automaticky získat přihlášeného k rozvoji Linkedinem (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s rozvoji Linkedinem, potřebujete následující položky:

- Předplatné Azure AD
- Rozvoji Linkedinem jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání rozvoji Linkedinem z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Přidání rozvoji Linkedinem z Galerie
Pokud chcete nakonfigurovat integraci rozvoji Linkedinem do služby Azure AD, musíte doplnit rozvoji Linkedinem z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat rozvoji Linkedinem z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **rozvoji Linkedinem**. Na panelu výsledků klikněte na **rozvoji Linkedinem** pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotného přihlášení pomocí Linkedinu zvýšit oprávnění na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v rozvoji Linkedinem je pro uživatele ve službě Azure AD. Vztah odkazu mezi uživatele služby Azure AD a související uživatelské v rozvoji Linkedinem jinak řečeno, je nutné stanovit.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v rozvoji Linkedinem.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s rozvoji Linkedinem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele rozvoji Linkedinem](#creating-a-linkedin-elevate-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci zvyšovat LinkedIn.

**Ke konfiguraci Azure AD jednotné přihlašování s rozvoji Linkedinem, proveďte následující kroky:**

1. V portálu pro správu Azure na **rozvoji Linkedinem** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi rozvoji Linkedinem jako správce.

1. V **centra pro účty**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **zvýšení – zvýšení testu AAD** z rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klikněte na **nebo klikněte sem pro načítání a kopírování jednotlivých polí ve formuláři** a zkopírujte **Entity Id** a **Url Assertion Consumer přístupu (ACS)**

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. Na webu Azure Portal v části **LinkedIn zvýšení domény a adresy URL**, proveďte následující kroky, pokud chcete nakonfigurovat jednotné přihlašování v **zahájené pomocí IdP** režimu

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. V **identifikátor** textového pole zadejte **Entity ID** zkopírovali z portálu LinkedIn 

    b. V **adresy URL odpovědi** textového pole zadejte **Assertion Consumer přístupu (ACS) adresy Url** zkopírovali z portálu LinkedIn

1. Pokud chcete nakonfigurovat jednotné přihlašování v **SP iniciované**, klikněte na možnost nastavení zobrazit pokročilé adresy URL v části o konfiguraci a nakonfigurovat přihlašovací adresa URL následujícímu vzoru:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. Vaše aplikace rozvoji Linkedinem očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **identifikátor uživatele** je **user.userprincipalname** ale rozvoji Linkedinem očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/updateusermail.png)

1. V **atributy uživatele** klikněte na tlačítko **zobrazit a upravit všechny ostatní atributy uživatele** a nastavte atributy. Budete muset přidat další deklarace identity s názvem **oddělení** a hodnota musí být namapovaný na **user.department**.

    | Název atributu | Hodnota atributu |
    | --- | --- |
    | Oddělení| user.department |

      ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/userattribute.png)

      a. Klikněte na Přidat atribut a otevřete stránku podrobností atribut přidejte atribut oddělení, jak je znázorněno níže –

      ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. Klikněte na **Ok** uložit atribut.

      c. Změnit název atributu **emailaddress** k **e-mailu**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. Klikněte na **Uložit**.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. Přejděte na **nastavení Linkedinu správce** oddílu. Nahrajte soubor XML, který jste si právě stáhli z portálu Azure portal kliknutím na možnost nahrát XML soubor.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Klikněte na tlačítko **na** pro povolení jednotného přihlašování. Stav jednotného přihlašování se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-linkedin-elevate-test-user"></a>Vytvoření zkušebního uživatele rozvoji Linkedinem

LinkedIn zvýšit oprávnění aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky. Na správce stránky nastavení na portálu vyfiltrují rozvoji Linkedinem přepínač **automaticky přiřadit licence** na aktivní pouze v čase zřizování a to se také přiřadit licenci uživateli. Rozvoji Linkedinem také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](linkedinelevate-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

   ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k rozvoji Linkedinem.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit k rozvoji Linkedinem Britta Simon, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

1. V seznamu aplikací vyberte **rozvoji Linkedinem**.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici rozvoji Linkedinem na přístupovém panelu, měli byste získat na stránce Azure Sign-on a na po úspěšném přihlášení, měli byste obdržet do své aplikace rozvoji Linkedinem.

## <a name="additional-resources"></a>Další materiály

* [Kurz: Konfigurace zvýšení oprávnění LinkedIn pro automatické zřizování uživatelů pomocí Azure Active Directory](linkedinelevate-provisioning-tutorial.md)
* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png
