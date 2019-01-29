---
title: 'Kurz: Integrace Azure Active Directory s Pingboard | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2011458183944d9e645ec0f08327a38b545df343
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170279"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Kurz: Integrace Azure Active Directory s Pingboard

V tomto kurzu se dozvíte, jak integrovat Pingboard s Azure Active Directory (Azure AD).

Pingboard integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Pingboard
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Pingboard (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Pingboard, potřebujete následující položky:

- Předplatné Azure AD
- Pingboard jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Pingboard z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-pingboard-from-the-gallery"></a>Přidání Pingboard z Galerie
Konfigurace integrace Pingboard do služby Azure AD, budete muset přidat Pingboard z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Pingboard z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Podnikové aplikace][2]

1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Pingboard**vyberte **Pingboard** panel výsledek a pak klikněte na **přidat** tlačítko pro přidání aplikace.

    ![Pingboard v seznamu výsledků](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Pingboard podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Pingboard je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Pingboard potřeba navázat.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Pingboard.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pingboard, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Pingboard](#create-a-pingboard-test-user)**  – Pokud chcete mít protějšek Britta Simon Pingboard, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Pingboard.

**Ke konfiguraci Azure AD jednotné přihlašování s Pingboard, proveďte následující kroky:**

1. Na webu Azure Portal na **Pingboard** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1.  Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. Na **Pingboard domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Pingboard domény a adresy URL jednotného přihlašování informace zprostředkovatele identity](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. V **identifikátor** textového pole zadejte hodnotu jako: `http://app.pingboard.com/sp`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<entity-id>.pingboard.com/auth/saml/consume`

1. Zkontrolujte **zobrazit pokročilé nastavení URL**, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Pingboard domény a adresy URL jednotného přihlašování informace SP](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL, pomocí následujícího vzorce: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Mějte prosím na paměti, že tyto hodnoty nejsou skutečné. Aktualizujte tyto hodnoty se skutečná adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Pingboard klienta](https://support.pingboard.com/) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Soubor xml s metadaty Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pingboard-tutorial/tutorial_general_400.png)

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně Pingboard, otevřete nové okno prohlížeče a přihlaste se ke svému účtu Pingboard. Musíte být správce Pingboard nastavit jednotné přihlašování.

1. V horní nabídce, vyberte **aplikace > integrace**

    ![Konfigurace jednotného přihlašování](./media/pingboard-tutorial/Pingboard_integration.png)

1. Na **integrace** stránky, vyhledejte **"Azure Active Directory"** dlaždici a klikněte na něj.

    ![Pingboard Single Sign-On integrace](./media/pingboard-tutorial/Pingboard_aad.png)

1. V modální okno, který následuje kliknutím **"Konfigurace"**

    ![Tlačítko Pingboard konfigurace](./media/pingboard-tutorial/Pingboard_configure.png)

1. Na následující stránce Všimněte si, že "integrace jednotného přihlašování s Azure je povoleno". Otevřete stažený soubor metadat XML v Poznámkový blok a vložte obsah **metadat zprostředkovatele identity**.

    ![Obrazovka Konfigurace jednotného přihlašování Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. Soubor se ověří a pokud je všechno správně, jednotné přihlašování se teď povolí.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/pingboard-tutorial/create_aaduser_02.png)

1. V horní části dialogového okna, klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.

    ![Tlačítko Přidat](./media/pingboard-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/pingboard-tutorial/create_aaduser_04.png)

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="create-a-pingboard-test-user"></a>Vytvoření zkušebního uživatele Pingboard

Cílem této části je vytvořte uživatele Britta Simon v Pingboard. Pingboard podporuje automatické zřizování uživatelů, což je ve výchozím nastavení povolená. Další podrobnosti můžete najít [tady](pingboard-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se na web společnosti Pingboard jako správce.

1. Klikněte na tlačítko **"Přidat Employee"** tlačítko **Directory** stránky.

    ![Přidat zaměstnance](./media/pingboard-tutorial/create_testuser_add.png)

1. Na **"Přidat Employee"** dialogového okna stránky, proveďte následující kroky:

    ![Pozvat](./media/pingboard-tutorial/create_testuser_name.png)

    a. V **jméno a příjmení** , jako je textové pole, typ celé jméno uživatele **Britta Simon**.

    b. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele **brittasimon@contoso.com**.

    c. V **pracovní pozice** textového pole zadejte název úlohy Britta Simon.

    d. V **umístění** rozevíracím seznamu vyberte umístění Britta Simon.

    e. Klikněte na tlačítko **Add** (Přidat).

1. Potvrzovací obrazovka se zobrazí pro potvrzení přidání uživatele.

    ![confirm](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mailu a řídí se odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Pingboard použití Azure jednotného přihlašování.

![Přiřadit uživatele][200] 

**Přiřadit Pingboard Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Pingboard**.

    ![Odkaz Pingboard v seznamu aplikací](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

Po kliknutí na dlaždici Pingboard na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Pingboard.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace zřizování uživatelů](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png
