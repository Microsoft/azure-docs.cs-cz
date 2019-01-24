---
title: 'Kurz: Integrace s Azure Active Directory s IMPAC riziko správce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a správce IMPAC rizika.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: ca0ea482b1cfb2f7af962ae1b7537f79bb60a62b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823143"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Kurz: Integrace s Azure Active Directory s IMPAC riziko správce

V tomto kurzu se dozvíte, jak integrovat IMPAC riziko správce Azure Active Directory (Azure AD).

Integrace správce IMPAC rizika s využitím Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke Správci riziko IMPAC.
- Uživatele, aby automaticky získat přihlášeného k IMPAC riziko správce (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s IMPAC riziko Manager, potřebujete následující položky:

- Předplatné Azure AD
- IMPAC riziko správce jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání správce IMPAC rizika z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Přidání správce IMPAC rizika z Galerie
Konfigurace integrace nástroje IMPAC riziko Manager do služby Azure AD, musíte přidat správce IMPAC rizika z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání správce IMPAC rizika z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **IMPAC riziko správce**vyberte **IMPAC riziko správce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Správce IMPAC rizika v seznamu výsledků](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a vyzkoušejte Azure AD jednotné přihlašování s IMPAC riziko program Manager podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v IMPAC riziko správce je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatele v nástroji IMPAC riziko Manager je potřeba navázat.

Ve Správci riziko IMPAC přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s IMPAC riziko správce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele IMPAC riziko správce](#create-a-impac-risk-manager-test-user)**  – Pokud chcete mít protějšek Britta Simon v IMPAC riziko správce, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci správce IMPAC rizika.

**Konfigurace služby Azure AD jednotné přihlašování s IMPAC riziko Manager, proveďte následující kroky:**

1. Na webu Azure Portal na **IMPAC riziko správce** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

1. Na **IMPAC riziko správce domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![IMPAC riziko správce domény a adresy URL jednotného přihlašování – informace](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. V **identifikátor** textové pole, typ a hodnotu poskytovanou infrastrukturou IMPAC

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | Prostředí | Vzor adresy URL |
    | ---------------|--------------- |    
    | Pro produkční prostředí |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Při přípravě a školení  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pro vývojové prostředí  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pro kontrolu kvality |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Pro Test |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![IMPAC riziko správce domény a adresy URL jednotného přihlašování – informace](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | Prostředí | Vzor adresy URL |
    | ---------------|--------------- |    
    | Pro produkční prostředí |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Při přípravě a školení  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pro vývojové prostředí  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pro kontrolu kvality |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Pro Test |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory IMPAC riziko správce klienta](mailto:rmsupport@Impac.co.nz) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/impacriskmanager-tutorial/tutorial_general_400.png)
    
1. Na **Manager riziko IMPAC Configuration** klikněte na **konfigurace IMPAC riziko Manager** otevřete **nakonfigurovat přihlašování** okno. Kopírování **SAML jednotné přihlašování – adresa URL služby, SAML Entity ID** a **odhlašování URL** z **Stručná referenční příručka oddílu.**

    ![Konfigurace jednotného přihlašování](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

1. Ke konfiguraci jednotného přihlašování na **IMPAC riziko správce** straně, je nutné odeslat na stažený **Certificate(Base64)**, **URL odhlašování, SAML Entity ID,** a  **SAML jednotné přihlašování – adresa URL služby** k [tým podpory IMPAC riziko správce](mailto:rmsupport@Impac.co.nz). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/impacriskmanager-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/impacriskmanager-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/impacriskmanager-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/impacriskmanager-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-impac-risk-manager-test-user"></a>Vytvoření zkušebního uživatele IMPAC riziko správce

V této části vytvoříte uživatele MS DTC volal Britta Simon v IMPAC riziko správce. Práce s [tým podpory IMPAC riziko správce](mailto:rmsupport@Impac.co.nz) přidat uživatele na platformě IMPAC riziko správce. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k IMPAC riziko správce.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon správci riziko IMPAC, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **IMPAC riziko správce**.

    ![Správce riziko IMPAC odkaz v seznamu aplikací](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici IMPAC riziko správce na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci IMPAC riziko správce.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/impacriskmanager-tutorial/tutorial_general_203.png

