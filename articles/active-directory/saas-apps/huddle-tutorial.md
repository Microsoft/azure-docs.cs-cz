---
title: 'Kurz: Integrace Azure Active Directory s tlačí se k | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tlačí se k.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 050a5a210ab10a557b149513a0416b188f4618ba
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885899"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Kurz: Integrace Azure Active Directory s tlačí se k

V tomto kurzu se dozvíte, jak integrovat tlačí se k Azure Active Directory (Azure AD).

Integrování tlačí se k Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k tlačí se k
- Můžete povolit uživatelům, aby automaticky získat přihlášení k tlačí se k (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s tlačí se k, potřebujete následující položky:

- Předplatné Azure AD
- Tlačí se k jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání tlačí se k z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-huddle-from-the-gallery"></a>Přidání tlačí se k z Galerie

Pokud chcete nakonfigurovat integraci tlačí se k Azure AD, musíte doplnit tlačí se k z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat tlačí se k z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **tlačí se k**. Vyberte **tlačí se k** z panel výsledků a pak klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s tlačí se k podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v tlačí se k je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v tlačí se k musí být vytvořeno.

Ke konfiguraci a Azure AD jednotné přihlašování s tlačí se k testování, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele tlačí se k](#creating-a-huddle-test-user)**  – Pokud chcete mít protějšek Britta Simon tlačí, který je propojený s Azure AD reprezentace uživatel se k.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v tlačí se k aplikaci.

**Ke konfiguraci Azure AD jednotné přihlašování s tlačí se k, proveďte následující kroky:**

1. Na webu Azure Portal na **tlačí se k** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_general_300.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_general_301.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_general_302.png)

5. Na **základní konfiguraci SAML** části proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** intiated režimu:

    > [!NOTE]
    > Tlačí se k instanci se zjišťují automaticky z domény, které zadáte níže.

    ![Tlačí se k doméně a adresy URL jednotné přihlašování – informace](./media/huddle-tutorial/tutorial_huddle_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL:

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL:

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Tlačí se k doméně a adresy URL jednotné přihlašování – informace](./media/huddle-tutorial/tutorial_huddle_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte některou z adresy URL, pomocí následujícího vzorce:

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > Hodnota přihlašovací adresa URL není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [klienta tlačí se k týmu podpory](https://huddle.zendesk.com) tuto výhodu získáte.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** stáhnout příslušný certifikát, jak je uvedeno vaše požadavek a uložte ho do počítače.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. Na **nastavení tlačí se k** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. Ke konfiguraci jednotného přihlašování na **tlačí se k** straně, budete muset odeslat certifikát, který jste si stáhli a adresy URL, které jste zkopírovali z **nastavení** **tlačí se k** oddílu z webu Azure portal [klienta tlačí se k týmu podpory](https://huddle.zendesk.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

    >[!NOTE]
    > Jednotné přihlašování je potřeba povolit tlačí se k tým podpory. Po dokončení konfigurace, dostanete oznámení.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    ![Vytvoření uživatele Azure AD][100]

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Vytváří se testovací uživatele služby Azure AD](./media/huddle-tutorial/create_aaduser_02.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="creating-a-huddle-test-user"></a>Vytvoření zkušebního uživatele tlačí se k

Přihlaste se k tlačí se k Azure AD uživatelům umožnit, musí být poskytnuty do tlačí se k. V případě tlačí se k zřizování se ruční úlohy.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **tlačí se k** společnosti serveru jako správce.

2. Klikněte na tlačítko **pracovní prostor**.

3. Klikněte na tlačítko **lidé \> pozvat**.

    ![Lidé](./media/huddle-tutorial/IC787838.png "osoby")

4. V **vytvořit nová pozvánka** části, proveďte následující kroky:
  
    ![Nová Pozvánka](./media/huddle-tutorial/IC787839.png "nová pozvánka")
  
    a. V **zvolte tým, který pozvat** seznamu vyberte **týmu**.

    b. Typ **e-mailovou adresu** platný Azure AD účet, který chcete zřídit v **zadejte e-mailová adresa osoby, které chcete pozvat** textového pole.

    c. Klikněte na tlačítko **pozvat**.

    >[!NOTE]
    > Držitel účtu Azure AD, dostanou e-mailu včetně odkaz pro potvrzení účtu, pak se změní na aktivní.

>[!NOTE]
>Další tlačí se k nástrojů pro vytváření účtu uživatele nebo rozhraní API poskytovaných tlačí se k můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k tlačí se k použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **tlačí se k**.

    ![Konfigurace jednotného přihlašování](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici tlačí se k na přístupovém panelu, měli byste obdržet automaticky přihlašovací stránku tlačí se k aplikaci.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

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
