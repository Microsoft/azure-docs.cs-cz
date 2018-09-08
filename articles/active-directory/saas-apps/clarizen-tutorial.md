---
title: 'Kurz: Integrace Azure Active Directory se službou Clarizen | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: jeedes
ms.openlocfilehash: 855f147b0622ecc0831f2bc464e83d245af9e574
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158667"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Kurz: Integrace Azure Active Directory se službou Clarizen

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) s Clarizen. Tato integrace poskytuje následující výhody:

- Můžete řídit, ve službě Azure AD, který má přístup k Clarizen.
- Můžete povolit uživatelům, aby se automaticky přihlášeni k Clarizen (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty v jednom centrálním místě na webu Azure portal.

Scénář v tomto kurzu se skládá ze dvou hlavních úloh:

1. Přidání Clarizen z galerie.
1. Konfigurace a otestování služby Azure AD jednotného přihlašování.

Pokud chcete další informace o softwaru jako integraci služby (SaaS) aplikací s Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky
Konfigurace integrace Azure AD s Clarizen, potřebujete následující položky:

- Předplatné Azure AD
- Clarizen odběr, který je povolený pro jednotné přihlašování

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Testování Azure AD jednotné přihlašování v testovacím prostředí. Nepoužívejte produkčním prostředí, pokud je to nezbytné.
- Pokud nemáte Azure AD testovací prostředí, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Přidání Clarizen z Galerie
Pokud chcete nakonfigurovat integraci Clarizen do služby Azure AD, přidejte na seznam spravovaných aplikací SaaS Clarizen z galerie.

1. V [webu Azure portal](https://portal.azure.com), v levém podokně klikněte **Azure Active Directory** ikonu.

    ![Ikona služby Azure Active Directory][1]

1. Klikněte na tlačítko **podnikové aplikace**. Pak klikněte na tlačítko **všechny aplikace**.

    ![Kliknutím na "Podnikové aplikace" a "Všechny aplikace"][2]

1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Tlačítko "Přidat"][3]

1. Do vyhledávacího pole zadejte **Clarizen**.

    ![Do vyhledávacího pole zadáte "Clarizen"](./media/clarizen-tutorial/tutorial_clarizen_000.png)

1. V podokně výsledků vyberte **Clarizen**a potom klikněte na tlačítko **přidat** pro přidání aplikace.

    ![V podokně výsledků vyberete Clarizen](./media/clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V následujících částech konfigurace a testování Azure AD jednotné přihlašování pomocí Clarizen podle testovacího uživatele Britta Simon.

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Clarizen je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Clarizen potřeba navázat. Navázání tohoto vztahu odkazu přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Clarizen.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clarizen, proveďte následující stavebních bloků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Clarizen](#create-a-clarizen-test-user)**  mít protějšek Britta Simon Clarizen, který je propojený s Azure AD reprezentace jí.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování
Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vaší aplikaci Clarizen.

1. Na webu Azure Portal na **Clarizen** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Kliknutím na "Single sign-on"][4]

1. V **jednotného přihlašování** dialogovém okně pro **režimu**vyberte **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Výběrem možnosti "založené na SAML Sign-on"](./media/clarizen-tutorial/tutorial_clarizen_01.png)

1. V **Clarizen domény a adresy URL** části, proveďte následující kroky:

    ![Pole pro identifikátor a odpověď URL](./media/clarizen-tutorial/tutorial_clarizen_02.png)

    a. V **identifikátor** zadejte hodnoty jako: **Clarizen**

    b. V **adresy URL odpovědi** zadejte adresu URL pomocí následujícímu vzoru: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Ty nejsou skutečné hodnoty. Je nutné použít skutečné identifikátor a adresa URL odpovědi. Tady doporučujeme použít jedinečnou hodnotu řetězce jako identifikátor. Abyste získali skutečné hodnoty, obraťte se [tým podpory Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

1. Na **podpisový certifikát SAML** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Kliknutím na "Vytvořit nový certifikát."](./media/clarizen-tutorial/tutorial_clarizen_03.png)    

1. V **vytvořit nový certifikát** dialogového okna, klikněte na ikonu kalendáře a vyberte datum vypršení platnosti. Potom klikněte na **Uložit**.

    ![Výběr a ukládá datum vypršení platnosti](./media/clarizen-tutorial/tutorial_general_300.png)

1. V **podpisový certifikát SAML** vyberte **nastavit nový certifikát jako aktivní**a potom klikněte na tlačítko **Uložit**.

    ![Zaškrtněte políčko pro aktivace nového certifikátu](./media/clarizen-tutorial/tutorial_clarizen_04.png)

1. V **certifikát výměny** dialogové okno, klikněte na tlačítko **OK**.

    ![Kliknutím na tlačítko "OK" potvrďte, že chcete aktivovat certifikát](./media/clarizen-tutorial/tutorial_general_400.png)

1. V **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Kliknutím na "Certifikát (Base64)" zahájíte stahování](./media/clarizen-tutorial/tutorial_clarizen_05.png)

1. V **Clarizen konfigurace** klikněte na tlačítko **nakonfigurovat Clarizen** otevřít **nakonfigurovat přihlašování** okna.

    ![Kliknutím na "Konfigurace Clarizen"](./media/clarizen-tutorial/tutorial_clarizen_06.png)

    ![Okno "Konfigurace přihlašování", včetně souborů a adresy URL](./media/clarizen-tutorial/tutorial_clarizen_07.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Clarizen jako správce.

1. Klikněte na své uživatelské jméno a potom klikněte na tlačítko **nastavení**.

    ![Kliknutím na "Nastavení" pod vaším uživatelským jménem](./media/clarizen-tutorial/tutorial_clarizen_001.png "nastavení")

1. Klikněte na tlačítko **globální nastavení** kartu. Potom vedle **federovaného ověření**, klikněte na tlačítko **upravit**.

    ![Karta "Globální nastavení"](./media/clarizen-tutorial/tutorial_clarizen_002.png "globální nastavení")

1. V **federovaného ověření** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "Federované ověřování"](./media/clarizen-tutorial/tutorial_clarizen_003.png "federovaného ověření")

    a. Vyberte **povolit federované ověřování**.

    b. Klikněte na tlačítko **nahrát** na stažený certifikát nahrajete.

    c. V **přihlašovací adresa URL** zadejte hodnotu **SAML jednotné přihlašování – adresa URL služby** v okně Konfigurace aplikací služby Azure AD.

    d. V **odhlašování URL** zadejte hodnotu **odhlašování URL** v okně Konfigurace aplikací služby Azure AD.

    e. Vyberte **použít příspěvek**.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Na webu Azure Portal vytvořte testovacího uživatele Britta Simon.

![Jméno a e-mailovou adresu testovacího uživatele Azure AD][100]

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Ikona služby Azure Active Directory](./media/clarizen-tutorial/create_aaduser_01.png)

1. Klikněte na tlačítko **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.

    ![Kliknutím na "Uživatele a skupiny" a "Všichni uživatelé"](./media/clarizen-tutorial/create_aaduser_02.png)

1. V horní části dialogového okna, klikněte na tlačítko **přidat** otevřít **uživatele** dialogové okno.

    ![Tlačítko "Přidat"](./media/clarizen-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Vyplněno "User" dialogové okno s názvem, e-mailovou adresu a heslo](./media/clarizen-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu účtu Britta Simon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-clarizen-test-user"></a>Vytvoření zkušebního uživatele Clarizen

Cílem této části je vytvořte uživatele Britta Simon v Clarizen.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Clarizen, je třeba zřídit uživatelské účty. V případě Clarizen zřizování se ruční úlohy.

1. Přihlaste se k webu společnosti Clarizen jako správce.

2. Klikněte na tlačítko **lidé**.

    ![Kliknutím na "Lidé"](./media/clarizen-tutorial/create_aaduser_001.png "osoby")

3. Klikněte na tlačítko **pozvat uživatele**.

    ![Tlačítko "Pozvat uživatele"](./media/clarizen-tutorial/create_aaduser_002.png "pozvat uživatele")

1. V **pozvat lidé** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "Pozvat"](./media/clarizen-tutorial/create_aaduser_003.png "pozvání uživatelů")

    a. V **e-mailu** zadejte e-mailovou adresu účtu Britta Simon.

    b. Klikněte na tlačítko **pozvat**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD
Povolte Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Clarizen.

![Přiřazené testovacího uživatele][200]

1. Ve službě Azure klikněte na portálu, otevřete zobrazení aplikací, přejděte do zobrazení adresáře **podnikové aplikace**a potom klikněte na tlačítko **všechny aplikace**.

    ![Kliknutím na "Podnikové aplikace" a "Všechny aplikace"][201]

1. V seznamu aplikací vyberte **Clarizen**.

    ![Výběrem Clarizen v seznamu](./media/clarizen-tutorial/tutorial_clarizen_50.png)

1. V levém podokně klikněte na tlačítko **uživatelů a skupin**.

    ![Kliknutím na "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **Přidat**. Potom v **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Tlačítko "Přidat" a "Přidat přiřazení" dialogových oken][203]

1. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů.

1. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko.

1. V **přidat přiřazení** dialogové okno, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování
Testování Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Clarizen na přístupovém panelu, vám by měl být automaticky přihlášeni Clarizen aplikace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clarizen-tutorial/tutorial_general_01.png
[2]: ./media/clarizen-tutorial/tutorial_general_02.png
[3]: ./media/clarizen-tutorial/tutorial_general_03.png
[4]: ./media/clarizen-tutorial/tutorial_general_04.png

[100]: ./media/clarizen-tutorial/tutorial_general_100.png

[200]: ./media/clarizen-tutorial/tutorial_general_200.png
[201]: ./media/clarizen-tutorial/tutorial_general_201.png
[202]: ./media/clarizen-tutorial/tutorial_general_202.png
[203]: ./media/clarizen-tutorial/tutorial_general_203.png