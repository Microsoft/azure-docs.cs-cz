---
title: 'Kurz: Azure Active Directory integrace s Menlo zabezpečení | Microsoft Docs'
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
ms.openlocfilehash: 1800c6be30af57707ef2a0f06077ea5a0634124d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221559"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>Kurz: Azure Active Directory integrace s Menlo zabezpečení

V tomto kurzu zjistěte, jak integrovat Menlo zabezpečení v Azure Active Directory (Azure AD).

Integrace Menlo zabezpečení s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Menlo zabezpečení
- Můžete povolit uživatelům, aby automaticky získat přihlášení k zabezpečení Menlo (jednotné přihlášení) s jejich účty Azure AD
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, přečtěte si téma. [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Menlo zabezpečení, potřebujete následující položky:

- Předplatné služby Azure AD
- Zabezpečení Menlo jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete získat zkušební verze jeden měsíc [zde](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Menlo zabezpečení z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-menlo-security-from-the-gallery"></a>Přidání Menlo zabezpečení z Galerie
Při konfiguraci integrace Menlo zabezpečení do služby Azure AD, musíte zvýšit zabezpečení Menlo z Galerie váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Menlo zabezpečení z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **Menlo zabezpečení**.

    ![Vytváření testovacího uživatele Azure AD](./media/menlosecurity-tutorial/tutorial_menlosecurity_search.png)

5. Na panelu výsledků vyberte **Menlo zabezpečení**a potom klikněte na **přidat** tlačítko Přidat aplikaci.

    ![Vytváření testovacího uživatele Azure AD](./media/menlosecurity-tutorial/tutorial_menlosecurity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Menlo zabezpečení založené na testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Menlo zabezpečení je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Menlo zabezpečení je nutné stanovit.

Tento vztah propojení se navazuje se hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Menlo zabezpečení.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Menlo zabezpečení, je třeba dokončit následující stavební bloky:

1. **[Konfigurace Azure AD jednotné přihlašování](#configuring-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytváření testovacího uživatele Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Menlo zabezpečení](#creating-a-menlo-security-test-user)**  – Pokud chcete mít protějšek Britta Simon v Menlo zabezpečení, který je propojený s Azure AD reprezentace daného uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Testování jednotné přihlašování](#testing-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Menlo zabezpečení.

**Ke konfiguraci Azure AD jednotné přihlašování s Menlo zabezpečení, proveďte následující kroky:**

1. Na portálu Azure na **Menlo zabezpečení** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurovat jednotné přihlašování][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_samlbase.png)

3. Na **Menlo zabezpečení domény a adresy URL** část, proveďte následující kroky:

    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.menlosecurity.com/account/login`

    b. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE] 
    > Tyto hodnoty nejsou reálné. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL a identifikátor. Obraťte se na [tým podpory zabezpečení klienta Menlo](https://www.menlosecurity.com/menlo-contact) k získání těchto hodnot. 
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **certifikátu (Base64)** a potom uložte soubor certifikátu v počítači.

    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/tutorial_general_400.png)

6. Na **konfigurace zabezpečení Menlo** klikněte na tlačítko **konfigurace zabezpečení Menlo** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID**, a **SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_configure.png) 

7. Konfigurace jednotného přihlašování na **Menlo zabezpečení** straně, přihlášení, které **Menlo zabezpečení** webu jako správce.

8. V části **nastavení** přejděte na **ověřování** a proveďte následující akce:
    
    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. Zaškrtněte políčka **povolení ověřování uživatele pomocí SAML**.

    b. Vyberte **povolit externí přístup** k **Ano**.

    c. V části **SAML zprostředkovatele**, vyberte **Azure Active Directory**.

    d. **Koncový bod SAML 2.0** : vložení **SAML jeden přihlašování adresa URL služby** který jste zkopírovali z portálu Azure.

    e. **Identifikátor služby (Vystavitel)** : vložení **SAML Entity ID** který jste zkopírovali z portálu Azure.

    f. **Certifikát X.509** : Otevřete **certifikátu (Base64)** stáhli z portálu Azure v programu Poznámkový blok a vložte ho v tomto poli.

    g. Kliknutím na **Uložit** nastavení uložte.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Vytváření testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

![Vytvořit uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. V **portál Azure**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváření testovacího uživatele Azure AD](./media/menlosecurity-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváření testovacího uživatele Azure AD](./media/menlosecurity-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** horní dialogové okno.
 
    ![Vytváření testovacího uživatele Azure AD](./media/menlosecurity-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogové okno stránky, proveďte následující kroky:
 
    ![Vytváření testovacího uživatele Azure AD](./media/menlosecurity-tutorial/create_aaduser_04.png) 

    a. V **název** textovému poli, typ **BrittaSimon**.

    b. V **uživatelské jméno** textovému poli, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit hesla** a poznamenejte si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-menlo-security-test-user"></a>Vytvoření zkušebního uživatele Menlo zabezpečení
 
V této části vytvoříte volal Britta Simon v Menlo zabezpečení uživatele. Práce s [tým podpory Menlo zabezpečení klienta](https://www.menlosecurity.com/menlo-contact) přidat uživatele do platformy Menlo zabezpečení. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Menlo zabezpečení.

![Přiřadit uživatele][200] 

**Pokud chcete přiřadit Britta Simon Menlo zabezpečení, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Menlo zabezpečení**.

    ![Konfigurovat jednotné přihlašování](./media/menlosecurity-tutorial/tutorial_menlosecurity_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="testing-single-sign-on"></a>Testování jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování v konfiguraci.

Otevřete okno prohlížeče v režimu "InPrivate" nebo "Incognito" k aktivaci nové ověřování.  V aplikaci Internet Explorer použijte kombinaci kláves Ctrl + Shift + P.  V prohlížeči Chrome použijte kombinaci kláves Ctrl + Shift + N.  V okně privátní procházení přejděte k chráněnému prostředku a provést přihlášení Azure AD.  Po úspěšném přihlášení ověření budete přesměrováni na požadovaný server v relaci izolace.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

