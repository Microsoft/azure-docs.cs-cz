---
title: 'Kurz: Integrace Azure Active Directory se službou Menlo zabezpečení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Menlo zabezpečení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9e63fe6b-0ad0-405d-9e41-6a1a40a41df8
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: jeedes
ms.openlocfilehash: a1f7458d52ffdee4cb48e4be0f553e3d57413249
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428841"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>Kurz: Integrace Azure Active Directory se službou Menlo zabezpečení

V tomto kurzu se dozvíte, jak integrovat Menlo zabezpečení Azure Active Directory (Azure AD).

Integrace zabezpečení Menlo s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k zabezpečení Menlo
- Uživatele, aby automaticky získat přihlášení k zabezpečení Menlo (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Menlo zabezpečení, potřebujete následující položky:

- S předplatným služby Azure AD
- Zabezpečení Menlo jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání zabezpečení Menlo z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-menlo-security-from-the-gallery"></a>Přidání zabezpečení Menlo z Galerie
Konfigurace integrace Menlo zabezpečení do služby Azure AD, musíte zvýšit zabezpečení Menlo z Galerie váš seznam spravovaných aplikací SaaS.

**Pro přidání zabezpečení Menlo z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Menlo zabezpečení**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/menlosecurity-tutorial/tutorial_menlosecurity_search.png)

1. Na panelu výsledků vyberte **Menlo zabezpečení**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/menlosecurity-tutorial/tutorial_menlosecurity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Menlo zabezpečení na základě testovací uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Menlo zabezpečení je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské Menlo zabezpečení je potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** Menlo zabezpečení.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Menlo zabezpečení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Menlo zabezpečení](#creating-a-menlo-security-test-user)**  – Pokud chcete mít protějšek Britta Simon Menlo zabezpečení, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Menlo zabezpečení.

**Ke konfiguraci Azure AD jednotné přihlašování s Menlo zabezpečení, proveďte následující kroky:**

1. Na webu Azure Portal na **Menlo zabezpečení** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_samlbase.png)

1. Na **Menlo zabezpečení domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.menlosecurity.com/account/login`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory klienta zabezpečení Menlo](https://www.menlosecurity.com/menlo-contact) k získání těchto hodnot. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/tutorial_general_400.png)

1. Na **konfigurace zabezpečení Menlo** klikněte na tlačítko **konfigurace zabezpečení Menlo** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **Menlo zabezpečení** straně, přihlaste se k **Menlo zabezpečení** webu jako správce.

1. V části **nastavení** přejděte na **ověřování** a proveďte následující akce:
    
    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. Zaškrtněte políčko **povolit ověřování uživatelů pomocí SAML**.

    b. Vyberte **povolení externího přístupu** k **Ano**.

    c. V části **SAML zprostředkovatele**vyberte **Azure Active Directory**.

    d. **Koncový bod SAML 2.0** : Vložit **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    e. **Identifikátor služby (Issuer)** : Vložit **SAML Entity ID** zkopírovanou z webu Azure portal.

    f. **Certifikát X.509** : Otevřít **certifikát (Base64)** stáhli z portálu Azure v programu Poznámkový blok a vložte ho do tohoto pole.

    g. Kliknutím na **Uložit** nastavení uložte.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/menlosecurity-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/menlosecurity-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/menlosecurity-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/menlosecurity-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-menlo-security-test-user"></a>Vytvoření zkušebního uživatele Menlo zabezpečení
 
V této části vytvořte uživatele Britta Simon Menlo zabezpečení. Práce s [tým podpory klienta zabezpečení Menlo](https://www.menlosecurity.com/menlo-contact) přidat uživatele na platformě Menlo zabezpečení. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Menlo zabezpečení.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Menlo zabezpečení, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Menlo zabezpečení**.

    ![Konfigurace jednotného přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace.

Otevřete okno prohlížeče v režimu "InPrivate" nebo "Incognito" k aktivaci nové ověřování.  V aplikaci Internet Explorer použijte kombinaci kláves Ctrl + Shift + P.  V prohlížeči Chrome použijte kombinaci kláves Ctrl + Shift + N.  V okně privátní procházení přejděte k chráněnému prostředku a provést přihlášení Azure AD.  Po úspěšném přihlášení budete přesměrováni na požadovaný server v rámci relace izolace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/menlosecurity-tutorial/tutorial_general_01.png
[2]: ./media/menlosecurity-tutorial/tutorial_general_02.png
[3]: ./media/menlosecurity-tutorial/tutorial_general_03.png
[4]: ./media/menlosecurity-tutorial/tutorial_general_04.png

[100]: ./media/menlosecurity-tutorial/tutorial_general_100.png

[200]: ./media/menlosecurity-tutorial/tutorial_general_200.png
[201]: ./media/menlosecurity-tutorial/tutorial_general_201.png
[202]: ./media/menlosecurity-tutorial/tutorial_general_202.png
[203]: ./media/menlosecurity-tutorial/tutorial_general_203.png

