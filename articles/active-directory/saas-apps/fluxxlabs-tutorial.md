---
title: 'Kurz: Integrace Azure Active Directory s praktickými cvičeními Fluxx | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c48e41318ff5ba189e4cc8b8529bb3b81911052
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204385"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Kurz: Integrace Azure Active Directory s praktickými cvičeními Fluxx

V tomto kurzu se dozvíte, jak Fluxx Labs integrovat s Azure Active Directory (Azure AD).

Integrace s Azure AD Fluxx Labs poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k testovacím prostředím Fluxx.
- Uživatele, aby automaticky získat přihlášeného k testovacím prostředím Fluxx (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s praktickými cvičeními Fluxx, potřebujete následující položky:

- Předplatné Azure AD
- Fluxx Labs jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Fluxx Labs z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-fluxx-labs-from-the-gallery"></a>Přidání Fluxx Labs z Galerie
Ke konfiguraci integrace Fluxx Labs do služby Azure AD, budete muset přidat Fluxx Labs z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Fluxx Labs z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]

1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Fluxx Labs**vyberte **Fluxx Labs** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Cvičení Fluxx v seznamu výsledků](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Fluxx testovací prostředí podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Fluxx Labs je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Fluxx Labs.

V Fluxx Labs přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s praktickými cvičeními Fluxx, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Fluxx Labs](#create-a-fluxx-labs-test-user)**  – Pokud chcete mít protějšek Britta Simon Fluxx Labs, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování Fluxx Labs aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s praktickými cvičeními Fluxx, proveďte následující kroky:**

1. Na webu Azure Portal na **Fluxx Labs** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

1. Na **Fluxx Labs domény a adresy URL** části, proveďte následující kroky:

    ![Fluxx Labs domény a adresy URL jednotného přihlašování – informace](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io` |
    | Předvýroba | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Předvýroba | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory Fluxx Labs](mailto:travis@fluxxlabs.com) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/fluxxlabs-tutorial/tutorial_general_400.png)

1. Na **Fluxx Labs konfigurace** klikněte na tlačítko **konfigurace Labs Fluxx** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Fluxx Labs jako správce.

1. Vyberte **správce** níže **nastavení** oddílu.

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config1.png)

1. V panelu Správce vyberte **moduly plug-in** > **integrace** a pak vyberte **SAML SSO-(Disabled)**

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config2.png)

1. V části atribut proveďte následující kroky:

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config3.png)

    a. Vyberte **SAML SSO** zaškrtávací políčko.

    b. V **cestu požadavku** textové pole, typ **saml/ověření/**.

    c. V **cestu zpětného volání** textové pole, typ **/auth/saml/callback**.

    d. V **Assertion Consumer Service Url(Single Sign-On URL)** textového pole zadejte **adresy URL odpovědi** hodnotu, která jste zadali na webu Azure Portal.

    e. V **cílová skupina (SP Entity ID)** textového pole zadejte **identifikátor** hodnotu, která jste zadali na webu Azure Portal.

    f. V **cílová adresa URL pro jednotné přihlašování zprostředkovatele Identity** vložit do textového pole **SAML jednotné přihlašování – adresa URL služby** hodnotu, kterou jste zkopírovali z portálu Azure portal.

    g. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    h. V **identifikátor názvu formátu** textového pole zadejte hodnotu `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Klikněte na **Uložit**.

    > [!NOTE]
    > Jakmile je obsah uložen, se zobrazí pole prázdné, zabezpečení, ale hodnota byla uložena v konfiguraci.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/fluxxlabs-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/fluxxlabs-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/fluxxlabs-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/fluxxlabs-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Vytvoření zkušebního uživatele Fluxx Labs

Povolit Azure AD uživatelům přihlášení k testovacím prostředím Fluxx, musí být zřízená ve službě Lab Fluxx. V případě Fluxx Labs zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Fluxx Labs jako správce.

1. Klikněte na níže zobrazí **ikonu**.

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config6.png)

1. Na řídicím panelu, klikněte na níže zobrazené ikony otevřete **nových LIDÍ** karty.

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config4.png)

1. Na **nových LIDÍ** části, proveďte následující kroky:

    ![Konfigurace testovacích prostředí Fluxx](./media/fluxxlabs-tutorial/config5.png)

    a. Cvičení Fluxx pomocí e-mailu jako jedinečný identifikátor pro jednotné přihlášení. Naplnění **jednotného přihlašování UID** pole e-mailovou adresu uživatele, který odpovídá e-mailovou adresu, která používají jako přihlašovací údaje pomocí jednotného přihlašování.

    b. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k testovacím prostředím Fluxx.

![Přiřazení role uživatele][200]

**Britta Simon přiřadit Fluxx Labs, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Fluxx Labs**.

    ![Odkaz Fluxx testovacích prostředí v seznamu aplikací](./media/fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Fluxx Labs na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Fluxx Labs.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/fluxxlabs-tutorial/tutorial_general_203.png
