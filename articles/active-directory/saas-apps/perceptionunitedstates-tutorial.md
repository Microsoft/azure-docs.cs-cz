---
title: 'Kurz: Integrace Azure Active Directory s vnímání Spojené státy (Non-UltiPro) | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a dojem Spojené státy (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 8c29d054f2e4e9ff4b57785a57e5c6ea512623a6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840654"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Kurz: Integrace Azure Active Directory s vnímání Spojené státy (Non-UltiPro)

V tomto kurzu se dozvíte, jak integrovat vnímání Spojené státy (Non-UltiPro) se službou Azure Active Directory (Azure AD).

Integrace vnímání Spojené státy (Non-UltiPro) s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k vnímání Spojené státy (Non-UltiPro).
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k vnímání Spojené státy (Non-UltiPro) (jednotné přihlašování) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s vnímání Spojené státy (Non-UltiPro), potřebujete následující položky:

- Předplatné Azure AD
- Vnímání Spojené státy (Non-UltiPro) jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání vnímání Spojené státy (Non-UltiPro) z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Přidání vnímání Spojené státy (Non-UltiPro) z Galerie
Konfigurace integrace nástroje vnímání Spojené státy (Non-UltiPro) do služby Azure AD, musíte přidat vnímání Spojené státy (Non-UltiPro) z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat vnímání Spojené státy (Non-UltiPro) z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **vnímání Spojené státy (Non-UltiPro)** vyberte **vnímání Spojené státy (Non-UltiPro)** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Vnímání Spojené státy (Non-UltiPro) v seznamu výsledků](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s vnímání Spojené státy (Non-UltiPro) na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v vnímání Spojené státy (Non-UltiPro) je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v vnímání Spojené státy (Non-UltiPro) musí být vytvořeno.

V vnímání Spojené státy (Non-UltiPro), přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s vnímání Spojené státy (Non-UltiPro), které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele vnímání Spojené státy (Non-UltiPro)](#create-a-perception-united-states-non-ultipro-test-user)**  – Pokud chcete mít protějšek Britta Simon v vnímání Spojené státy (Non-UltiPro), který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci vnímání Spojené státy (Non-UltiPro).

**Ke konfiguraci Azure AD jednotné přihlašování s vnímání Spojené státy (Non-UltiPro), proveďte následující kroky:**

1. Na webu Azure Portal na **vnímání Spojené státy (Non-UltiPro)** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

1. Na **vnímání Spojené státy (Non-UltiPro) domény a adresy URL** části, proveďte následující kroky:

    ![Vnímání Spojené státy (Non-UltiPro) domény a adresy URL jednotného přihlašování – informace](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://perception.kanjoya.com/sp`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Hodnota není skutečný. Skutečná adresa URL odpovědi, který je vysvětlen později v tomto kurzu jste se aktualizuje hodnotu.
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

1. Na **vnímání Spojené státy (Non-UltiPro) konfigurace** klikněte na tlačítko **konfigurace vnímání Spojené státy (Non-UltiPro)** otevřete **nakonfigurovat přihlašování** okna . Kopírovat **SAML Entity ID** z **Stručná referenční příručka oddílu.**

    a. **Vnímání Spojené státy (Non-UltiPro)** aplikace vyžaduje **SAML Entity ID** hodnotu, kterou jste zkopírovali, aby zakódovat do identifikátoru uri. K získání hodnoty zakódovat do identifikátoru uri, pomocí následujícího odkazu:**http://www.url-encode-decode.com/**.

    b. Po získání identifikátoru uri kódovanou hodnotu kombinaci se službou **adresy URL odpovědi** jak je uvedeno níže –

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Vložte hodnotu uvedenou výše v **adresy URL odpovědi** textového pole v **vnímání Spojené státy (Non-UltiPro) domény a adresy URL** oddílu.

    ![Konfigurace vnímání Spojené státy (bez UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

1. V jiném okně prohlížeče Přihlaste se k webu společnosti vnímání Spojené státy (Non-UltiPro) jako správce.

1. Na hlavním panelu nástrojů klikněte na tlačítko **nastavení účtu**.

    ![Uživatel vnímání Spojené státy (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

1. Na **nastavení účtu** stránce, proveďte následující kroky:

    ![Uživatel vnímání Spojené státy (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. V **název společnosti** textového pole zadejte název **společnosti**.
    
    b. V **název účtu** textového pole zadejte název **účet**.

    c. V **výchozí odpověď na E-mail** text zadejte platnými **e-mailu**.

    d. Vyberte **zprostředkovatele Identity jednotného přihlašování** jako **SAML 2.0**.

1. Na **Konfigurace jednotného přihlašování** stránce, proveďte následující kroky:

    ![Spojené státy (bez UltiPro) SSOConfig vnímání](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Vyberte **SAML NameID typ** jako **e-MAILU**.

    b. V **název konfigurace jednotného přihlašování** textového pole zadejte název vaší **konfigurace**.
    
    c. V **název zprostředkovatele identit** textového pole vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure portal. 

    d. V **pole pro doménu SAML**, zadejte doménu, jako je **@contoso.com**.

    e. Klikněte na **nahrát znovu** k nahrání **soubor XML s metadaty** souboru.

    f. Klikněte na tlačítko **aktualizace**.


> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Vytvoření zkušebního uživatele vnímání Spojené státy (Non-UltiPro)

V této části vytvoříte uživateli Britta Simon v vnímání Spojené státy (Non-UltiPro). Práce s [tým podpory vnímání Spojené státy (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) přidat uživatele na platformě vnímání Spojené státy (Non-UltiPro).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k vnímání Spojené státy (Non-UltiPro).

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon na vnímání Spojené státy (Non-UltiPro), postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **vnímání Spojené státy (Non-UltiPro)**.

    ![Odkaz vnímání Spojené státy (Non-UltiPro) v seznamu aplikací](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici vnímání Spojené státy (Non-UltiPro) na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci vnímání Spojené státy (Non-UltiPro).
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

