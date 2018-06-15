---
title: 'Kurz: Azure Active Directory integrace s Fluxx Labs | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fluxx laboratoře.
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
ms.date: 04/12/2018
ms.author: jeedes
ms.openlocfilehash: 15c574bf48cbbbeacf06c0a5531c6921a2d01269
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343704"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Kurz: Azure Active Directory integrace s Fluxx Labs

V tomto kurzu zjistěte, jak integrovat Fluxx Labs s Azure Active Directory (Azure AD).

Integrace Fluxx Labs s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k testovacímu prostředí Fluxx.
- Můžete povolit uživatelům, aby automaticky získat přihlášeného k testovacímu prostředí Fluxx (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Fluxx Labs, potřebujete následující položky:

- Předplatné služby Azure AD
- Fluxx Labs jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Fluxx Labs z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-fluxx-labs-from-the-gallery"></a>Přidání Fluxx Labs z Galerie
Při konfiguraci integrace Fluxx Labs do služby Azure AD potřebujete přidat Fluxx Labs z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Fluxx Labs z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Fluxx Labs**, vyberte **Fluxx Labs** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Labs Fluxx v seznamu výsledků](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Fluxx prostředí podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Fluxx Labs je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Fluxx Labs musí navázat.

V prostředích Fluxx přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Fluxx Labs, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Fluxx Labs](#create-a-fluxx-labs-test-user)**  – Pokud chcete mít protějšek Britta Simon v Fluxx Labs propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Fluxx laboratoře.

**Ke konfiguraci Azure AD jednotné přihlašování s Fluxx Labs, proveďte následující kroky:**

1. Na portálu Azure na **Fluxx Labs** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_samlbase.png)

3. Na **Fluxx Labs domény a adresy URL** část, proveďte následující kroky:

    ![Fluxx Labs domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io` |
    | Předvýroba | `https://<subdomain>.preprod.fluxxlabs.com`|
        
    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce:

    | Prostředí | Vzor adresy URL|
    |-------------|------------|
    | Výroba | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Předvýroba | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|
        
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem a adresa URL odpovědi. Obraťte se na [tým podpory Fluxx Labs](mailto:travis@fluxxlabs.com) k získání těchto hodnot.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_400.png)

6. Na **Fluxx Labs konfigurace** klikněte na tlačítko **konfigurace Labs Fluxx** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID a SAML jeden přihlašování adresu URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_configure.png)

7. V okně prohlížeče jiný web Přihlaste se k serveru vaší společnosti Fluxx Labs jako správce.

8. Vyberte **správce** níže **nastavení** části.

    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config1.png)

9. V panelu Správce vyberte **moduly plug-in** > **integrace** a pak vyberte **SAML SSO-(Disabled)**

    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config2.png)
    
10. V části atribut proveďte následující kroky:
    
    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config3.png)

    a. Vyberte **jednotné přihlašování SAML** zaškrtávací políčko.

    b. V **cestu požadavku** textovému poli, typ **/auth/saml**.

    c. V **cesta zpětné volání** textovému poli, typ **/auth/saml/callback**.

    d. V **Assertion příjemce služby Url(Single Sign-On URL)** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby**, který jste zkopírovali z portálu Azure.

    e. V **cílová skupina (SP Entity ID)** textovému poli, vložte hodnotu **SAML Entity ID**, který jste zkopírovali z portálu Azure.

    f. V poznámkovém bloku otevřete váš kódování base-64 kódovaného certifikátu, zkopírujte obsah ho do schránky a vložte jej do **certifikát zprostředkovatele Identity** textové pole.

    g. V **identifikátor název formátu** textovému poli, zadejte hodnotu `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    h. Klikněte na **Uložit**.

    > [!NOTE]
    > Jakmile obsah uložit, zobrazí toto pole prázdné, zabezpečení, ale hodnota byla uložena v konfiguraci.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-fluxxlabs-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-fluxx-labs-test-user"></a>Vytvoření zkušebního uživatele Fluxx Labs

Pokud chcete povolit uživatelům Azure AD přihlášení k testovacímu prostředí Fluxx, musí být zřízená do Fluxx laboratoře. V případě Fluxx Labs zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Fluxx Labs jako správce.

2. Klikněte na níže zobrazené **ikonu**.

    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config6.png)

3. Na řídicím panelu klikněte na níže zobrazené ikonu Otevřít **nové osoby** karty.

    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config4.png)

4. Na **nové osoby** část, proveďte následující kroky:
    
    ![Konfigurace Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs použijte e-mailu jako jedinečný identifikátor pro přihlášení SSO. Naplnění **jednotné přihlašování UID** pole s e-mailovou adresu uživatele, který odpovídá e-mailovou adresu, která používají jako přihlašovací údaje pomocí jednotného přihlašování.

    b. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k testovacímu prostředí Fluxx.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Fluxx Labs, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Fluxx Labs**.

    ![V seznamu aplikací na odkaz Fluxx Labs](./media/active-directory-saas-fluxxlabs-tutorial/tutorial_fluxxlabs_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Fluxx Labs na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Fluxx laboratoře.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-fluxxlabs-tutorial/tutorial_general_203.png
