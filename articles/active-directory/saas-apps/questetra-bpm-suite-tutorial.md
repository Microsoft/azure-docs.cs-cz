---
title: 'Kurz: Azure Active Directory integrace se sadou Questetra BPM Suite | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: e9a4ab5bb262b274f4c53741f7a9ea1ce3956848
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220124"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Kurz: Azure Active Directory integrace se sadou Questetra BPM Suite

V tomto kurzu zjistěte, jak integrovat Questetra BPM Suite s Azure Active Directory (Azure AD).

Integrace sady BPM Questetra s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Questetra BPM Suite
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Suite BPM Questetra (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace Azure AD integrace se sadou Questetra BPM Suite, potřebujete následující položky:

- Předplatné služby Azure AD
- Questetra BPM Suite jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidat sady BPM Questetra z Galerie
2. Konfigurace a otestování Azure AD jednotné přihlašování

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Přidat sady BPM Questetra z Galerie
Pokud chcete nakonfigurovat integraci sady BPM Questetra do služby Azure AD, potřebujete přidat Questetra BPM Suite z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Questetra BPM Suite z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Questetra BPM Suite**, vyberte **Questetra BPM Suite** z panelu výsledek a pak klikněte na tlačítko **přidat** tlačítko Přidat aplikaci.

    ![Přidat z Galerie](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování
V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Questetra BPM Suite podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v sadě BPM Questetra je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v sadě BPM Questetra musí navázat.

V sadě BPM Questetra, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Questetra BPM Suite, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Questetra BPM Suite](#create-a-questetra-bpm-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon BPM sady Questetra, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Questetra BPM Suite.

**Ke konfiguraci Azure AD jednotné přihlašování s Questetra BPM Suite, proveďte následující kroky:**

1. Na portálu Azure na **Questetra BPM Suite** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Přihlašování založené na SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. Na **Questetra BPM Suite domény a adresy URL** část, proveďte následující kroky:

    ![Část Questetra BPM Suite domény a adresy URL](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Můžete získat tyto hodnoty z **SP informace** části na vaše **Questetra BPM Suite** společnosti lokality, který je vysvětlen později v kurzu nebo kontaktujte [podpora Questetra BPM Suite klientů tým](https://www.questetra.com/contact/). 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base 64)** a potom uložte soubor certifikátu v počítači.

    ![Certifikát pro podpis SAML části](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Tlačítko Uložit](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. Na **Questetra BPM Suite konfigurace** klikněte na tlačítko **konfigurace Suite BPM Questetra** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Questetra BPM Suite konfigurační oddíl](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. V okně prohlížeče jiný web, přihlaste se k vaší **Questetra BPM Suite** společnosti lokality jako správce.

8. V nabídce v horní části, klikněte na tlačítko **nastavení systému**. 
   
    ![Azure AD jednotné přihlášení][10]

9. Chcete-li otevřít **SingleSignOnSAML** klikněte na tlačítko **jednotné přihlašování (SAML)**. 
   
    ![Azure AD jednotné přihlášení][11]

10. Na vaše **Questetra BPM Suite** společnosti v lokalitě, **SP informace** část, proveďte následující kroky:

    a. Kopírování **adresa URL služby ACS**a vložte ji do **přihlašovací adresa URL** textového pole v **Questetra BPM Suite domény a adresy URL** části z portálu Azure.
    
    b. Kopírování **Entity ID**a vložte ji do **identifikátor** textového pole v **Questetra BPM Suite domény a adresy URL** části z portálu Azure.

11. Na vaše **Questetra BPM Suite** společnosti lokality, proveďte následující kroky: 
   
    ![Konfigurovat jednotné přihlašování][15]
   
    a. Vyberte **povolit jednotné přihlašování**.
   
    b. V **Entity ID** textovému poli, vložte hodnotu **SAML Entity ID** který jste zkopírovali z portálu Azure.
    
    c. V **přihlašovací adresa URL stránky** textovému poli, vložte hodnotu **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.
    
    d. V **adresy URL odhlašovací stránky** textovému poli, vložte hodnotu **Sign-Out URL** který jste zkopírovali z portálu Azure.
    
    e. V **NameID formátu** textovému poli, typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Otevřete váš **Base-64** kódovaného certifikátu v poznámkovém bloku stáhli z portálu Azure, zkopírujte obsah ho do schránky a vložte ji do **ověření certifikátu** textové pole. 

    g. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Vytvoření zkušebního uživatele Questetra BPM Suite

Cílem této části je vytvoření uživatele volal Britta Simon v Questetra BPM Suite.

**Pokud chcete vytvořit uživateli volat Britta Simon v sadě BPM Questetra, proveďte následující kroky:**

1. Přihlásit se k serveru vaší společnosti Questetra BPM Suite jako správce.
2. Přejděte na **nastavení systému > seznam uživatelů > Nový uživatel**. 
3. V dialogovém okně Nový uživatel proveďte následující kroky: 
   
    ![Vytvoření zkušebního uživatele][300] 
   
    a. V **název** textovému poli, typ **název** uživatele **britta.simon@contoso.com**.
   
    b. V **e-mailu** textovému poli, typ **e-mailu** uživatele **britta.simon@contoso.com**
   
    c. V **heslo** textovému poli, zadejte **heslo** uživatele.
    
    d. Klikněte na tlačítko **přidat nové uživatele**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu k Questetra BPM Suite.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Questetra BPM Suite, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Questetra BPM Suite**.

    ![Sada BPM Questetra v seznamu aplikací](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Questetra BPM Suite na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Questetra BPM Suite.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

