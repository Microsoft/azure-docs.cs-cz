---
title: 'Kurz: Integrace Azure Active Directory s AppDynamics | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AppDynamics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: jeedes
ms.openlocfilehash: 3600e83d18f8cabd03c46af2ef47445c588cbdb5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548270"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Kurz: Integrace Azure Active Directory s AppDynamics

V tomto kurzu se dozvíte, jak integrovat AppDynamics s Azure Active Directory (Azure AD).

AppDynamics integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k AppDynamics
- Můžete povolit uživatelům, aby automaticky získat přihlášení k AppDynamics (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s AppDynamics, potřebujete následující položky:

- S předplatným služby Azure AD
- AppDynamics jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání AppDynamics z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-appdynamics-from-the-gallery"></a>Přidání AppDynamics z Galerie
Konfigurace integrace AppDynamics do služby Azure AD, budete muset přidat AppDynamics z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat AppDynamics z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **AppDynamics**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appdynamics-tutorial/tutorial_appdynamics_search.png)

5. Na panelu výsledků vyberte **AppDynamics**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s AppDynamics podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v AppDynamics je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v AppDynamics je potřeba navázat.

V AppDynamics, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s AppDynamics, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření uživatele s AppDynamics testovací](#creating-an-appdynamics-test-user)**  – Pokud chcete mít protějšek Britta Simon AppDynamics, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci AppDynamics.

**Ke konfiguraci Azure AD jednotné přihlašování s AppDynamics, proveďte následující kroky:**

1. Na webu Azure Portal na **AppDynamics** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Konfigurace jednotného přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. Na **AppDynamics domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.saas.appdynamics.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory AppDynamics klienta](https://www.appdynamics.com/support/) k získání těchto hodnot.

4. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_certificate.png)

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/appdynamics-tutorial/tutorial_general_400.png)

6. Na **AppDynamics konfigurace** klikněte na tlačítko **nakonfigurovat AppDynamics** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_configure.png)

7. V okně jiné webové prohlížeče Přihlaste se k webu společnosti AppDynamics jako správce.

8. Na panelu nástrojů v horní části klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **správu**.

    ![Správa](./media/appdynamics-tutorial/ic790216.png "správy")

9. Klikněte na tlačítko **zprostředkovatele ověřování** kartu.

    ![Zprostředkovatel ověřování](./media/appdynamics-tutorial/ic790224.png "zprostředkovatele ověřování")

10. V **zprostředkovatele ověřování** části, proveďte následující kroky:

    ![Konfigurace SAML](./media/appdynamics-tutorial/ic790225.png "konfigurace SAML")

    a. Jako **zprostředkovatele ověřování**vyberte **SAML**.

    b. V **přihlašovací adresa URL** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. V **odhlašovací adresa URL** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.

    d. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát** textové pole

    e. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appdynamics-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appdynamics-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/appdynamics-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/appdynamics-tutorial/create_aaduser_04.png)

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-an-appdynamics-test-user"></a>Vytvoření uživatele s AppDynamics testu

Cílem této části je vytvořte uživatele Britta Simon v AppDynamics. AppDynamics podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k AppDynamics, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory AppDynamics klienta](https://www.appdynamics.com/support/).

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k AppDynamics použití Azure jednotného přihlašování.

![Přiřadit uživatele][200]

**Přiřadit AppDynamics Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201]

2. V seznamu aplikací vyberte **AppDynamics**.

    ![Konfigurace jednotného přihlašování](./media/appdynamics-tutorial/tutorial_appdynamics_app.png)

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici AppDynamics na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci AppDynamics.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/appdynamics-tutorial/tutorial_general_203.png
