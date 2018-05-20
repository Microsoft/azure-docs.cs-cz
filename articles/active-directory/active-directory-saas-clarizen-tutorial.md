---
title: 'Kurz: Azure Active Directory integrace s Clarizen | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 8af9e7023142077fdeed0262e5f39921b158a14f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Kurz: Azure Active Directory integrace s Clarizen

V tomto kurzu jste zjistěte, jak integrovat Azure Active Directory (Azure AD) s Clarizen. Tato integrace poskytuje následující výhody:

- Můžete ovládat, ve službě Azure AD, který má přístup k Clarizen.
- Můžete povolit uživatelům, aby se automaticky přihlásíte k Clarizen (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě, portálu Azure.

Tento scénář v tomto kurzu se skládá z dvě hlavní úlohy:

1. Přidejte Clarizen z galerie.
2. Konfigurace a otestování Azure AD jednotné přihlašování.

Pokud chcete další informace o softwaru jako integraci aplikace služby (SaaS) s Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky
Konfigurace integrace Azure AD s Clarizen, potřebujete následující položky:

- Předplatné služby Azure AD
- Clarizen odběr, který je povolený pro jednotné přihlašování

Chcete-li otestovat kroky v tomto kurzu, postupujte podle následujících doporučení:

- Testování Azure AD jednotné přihlašování v testovacím prostředí. Nepoužívejte produkční prostředí, pokud je to nutné.
- Pokud nemáte testovacím prostředí s Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="add-clarizen-from-the-gallery"></a>Přidat Clarizen z Galerie
Při konfiguraci integrace Clarizen do služby Azure AD přidáte do seznamu spravovaných aplikací SaaS Clarizen z galerie.

1. V [portál Azure](https://portal.azure.com), v levém podokně klikněte **Azure Active Directory** ikonu.

    ![Azure Active Directory – ikona][1]

2. Klikněte na tlačítko **podnikové aplikace, které**. Pak klikněte na tlačítko **všechny aplikace**.

    ![Kliknutím na tlačítko "Podnikové aplikace" a "Všechny aplikace"][2]

3. Klikněte **přidat** tlačítka v horní části dialogových oken.

    ![Tlačítko "Přidat"][3]

4. Do vyhledávacího pole zadejte **Clarizen**.

    ![Do vyhledávacího pole zadejte "Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. V podokně výsledků vyberte **Clarizen**a potom klikněte na **přidat** tuto aplikaci přidat.

    ![Výběr Clarizen v podokně výsledků](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V následujících částech nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clarizen na základě testovací uživatele Britta Simon.

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Clarizen je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Clarizen musí navázat. Vytvořit tento odkaz vztah přiřazením hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Clarizen.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clarizen, proveďte následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  umožňující uživatelům používat tuto funkci.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  k testování Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Clarizen](#create-a-clarizen-test-user)**  tak, aby měl protějšek Britta Simon v Clarizen propojeném s Azure AD reprezentace jí.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  ověřit, zda funguje konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování
Povolení služby Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Clarizen.

1. Na portálu Azure na **Clarizen** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Kliknutím na tlačítko "Single sign-on"][4]

2. V **jednotného přihlašování** dialogové okno, pro **režimu**, vyberte **na základě SAML přihlašování** umožňující jednotného přihlašování.

    ![Výběr "na základě SAML přihlášení"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. V **Clarizen domény a adresy URL** část, proveďte následující kroky:

    ![Pole pro adresu URL identifikátor dotazů a odpovědí](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. V **identifikátor** zadejte hodnotu jako: **Clarizen**

    b. V **adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzorce: **https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > Tyto nejsou skutečné hodnoty. Budete muset použít identifikátor skutečné a adresa URL odpovědi. Tady doporučujeme použít jedinečnou hodnotu řetězce jako identifikátor. Chcete-li získat skutečnými hodnotami, obraťte se [tým podpory Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Na **SAML podpisový certifikát** klikněte na tlačítko **vytvořit nový certifikát**.

    ![Kliknutím na tlačítko "Vytvořit nový certifikát"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. V **vytvořit nový certifikát** dialogové okno pole, klikněte na ikonu kalendáři a vyberte datum vypršení platnosti. Potom klikněte na **Uložit**.

    ![Výběr a ukládání datum vypršení platnosti](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. V **SAML podpisový certifikát** vyberte **aktivujte nový certifikát**a potom klikněte na **Uložit**.

    ![Zaškrtněte políčko pro vytvoření nového certifikátu active](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. V **certifikát výměny** dialogové okno, klikněte na tlačítko **OK**.

    ![Kliknutím na tlačítko "OK" potvrďte, že chcete aktivovat certifikát](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. V **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Kliknutím na tlačítko "Certifikát (Base64)" zahájíte stahování](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. V **Clarizen konfigurace** klikněte na tlačítko **konfigurace Clarizen** otevřete **konfigurovat přihlášení** okno.

    ![Kliknutím na tlačítko "Konfigurace Clarizen"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![Okno "Konfigurace přihlášení", včetně souborů a adresy URL](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Clarizen jako správce.

11. Klikněte na své uživatelské jméno a potom klikněte na **nastavení**.

    ![Kliknutím na "Nastavení" v části vaše uživatelské jméno](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "nastavení")

12. Klikněte **globální nastavení** kartě. Pak do **federovaného ověřování**, klikněte na tlačítko **upravit**.

    ![Karta "Globální nastavení"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "globální nastavení")

13. V **federovaného ověřování** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "Federovaného ověřování"](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "federovaného ověřování")

    a. Vyberte **povolit federovaného ověřování**.

    b. Klikněte na tlačítko **nahrát** nahrát stažený certifikát.

    c. V **přihlašovací adresa URL** zadejte hodnotu **SAML jeden přihlašování adresa URL služby** z okna konfigurace aplikace Azure AD.

    d. V **Sign-out URL** zadejte hodnotu **Sign-Out URL** z okna konfigurace aplikace Azure AD.

    e. Vyberte **použít POST**.

    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Na portálu Azure vytvořte testovací uživatele volat Britta Simon.

![Jméno a e-mailovou adresu testovacího uživatele Azure AD][100]

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** ikonu.

    ![Azure Active Directory – ikona](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. Klikněte na tlačítko **uživatelů a skupin**a potom klikněte na **všichni uživatelé** zobrazíte seznam uživatelů.

    ![Kliknutím na tlačítko "Uživatelé a skupiny" a "Všichni uživatelé"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. V horní části dialogových oken, klikněte na tlačítko **přidat** otevřete **uživatele** dialogové okno.

    ![Tlačítko "Přidat"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "User" s název, e-mailovou adresu a heslo vyplněno](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu účtu Britta Simon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-clarizen-test-user"></a>Vytvoření zkušebního uživatele Clarizen
Pokud chcete povolit uživatelům Azure AD přihlášení k Clarizen, je třeba zřídit uživatelské účty. V případě Clarizen zřizování je ruční úloha.

1. Přihlaste se k serveru vaší společnosti Clarizen jako správce.

2. Klikněte na tlačítko **osoby**.

    ![Kliknutím na tlačítko "Uživatelé"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "osoby")

3. Klikněte na tlačítko **pozvat uživatele**.

    ![Tlačítko "Pozvat uživatele"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "pozvat uživatele")

4. V **pozvat osoby** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "Pozvat osoby"](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "pozvat osoby")

    a. V **e-mailu** zadejte e-mailovou adresu účtu Britta Simon.

    b. Klikněte na tlačítko **pozvat**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a postupujte podle odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD
Povolte Britta Simon používat tak, že udělíte přístup k Clarizen Azure jednotné přihlašování.

![Přiřazené testovacího uživatele][200]

1. Ve službě Azure klikněte na portálu, otevřete zobrazit aplikace, přejděte do zobrazení adresáře **podnikové aplikace, které**a potom klikněte na **všechny aplikace**.

    ![Kliknutím na tlačítko "Podnikové aplikace" a "Všechny aplikace"][201]

2. V seznamu aplikací vyberte **Clarizen**.

    ![V seznamu vyberete Clarizen](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. V levém podokně klikněte na **uživatelů a skupin**.

    ![Kliknutím na tlačítko "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **Přidat**. Potom v **přidat přiřazení** dialogové okno, vyberte **uživatelů a skupin**.

    ![Tlačítko "Přidat" a "Přidat přiřazení" dialogových oken][203]

5. V **uživatelů a skupin** dialogové okno, vyberte **Britta Simon** v seznamu uživatelů.

6. V **uživatelů a skupin** dialogové okno, klikněte **vyberte** tlačítko.

7. V **přidat přiřazení** dialogové okno, klikněte **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování
Azure AD jeden přihlašování konfiguraci otestujte pomocí přístupového panelu.

Když kliknete na dlaždici Clarizen na přístupovém panelu, můžete by měl být automaticky přihlášeni do vaší aplikace Clarizen.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png
