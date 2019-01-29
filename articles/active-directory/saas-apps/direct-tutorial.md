---
title: 'Kurz: Integrace Azure Active Directory s přímým | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a direct.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7c2cd1f0-d14c-42f0-94a8-9b800008b285
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: jeedes
ms.openlocfilehash: f45e3c5a3416c28ebe16fcf0ba1f016478c46c7c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176220"
---
# <a name="tutorial-azure-active-directory-integration-with-direct"></a>Kurz: Integrace Azure Active Directory s přímým

V tomto kurzu se dozvíte, jak integrovat přímo s Azure Active Directory (Azure AD).

Integrace přímo s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke směrování
- Můžete povolit uživatelům, aby automaticky získat přihlášeného ke směrování (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přímým přístupem, potřebujete následující položky:

- Předplatné Azure AD
- Přímým přístupem jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání přímo z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-direct-from-the-gallery"></a>Přidání přímo z Galerie

Pokud chcete nakonfigurovat integraci s přímým přístupem do služby Azure AD, budete muset přidat na seznam spravovaných aplikací SaaS přímo z galerie.

**Chcete-li přidat přímo z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

4. Do vyhledávacího pole zadejte **přímé**. Vyberte **přímé** z panel výsledků a pak vyberte **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/direct-tutorial/tutorial_direct_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s přímým podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v přímém je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele služby Azure AD a souvisejících uživatelem v s přímým přístupem musí být vytvořeno.

V přímé, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s přímým, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytváří se uživatel – přímý test](#creating-a-direct-test-user)**  – Pokud chcete mít protějšek Britta Simon přímo, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci s přímým přístupem.

**Ke konfiguraci Azure AD jednotné přihlašování s přímým, proveďte následující kroky:**

1. Na webu Azure Portal na **přímé** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/direct-tutorial/tutorial_direct_samlbase.png)

3. Na **přímé domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/direct-tutorial/tutorial_direct_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://direct4b.com/`

4. Zkontrolujte **zobrazit pokročilé nastavení URL**, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Konfigurace jednotného přihlašování](./media/direct-tutorial/tutorial_direct_url1.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://direct4b.com/sso` 

5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/direct-tutorial/tutorial_direct_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/direct-tutorial/tutorial_general_400.png)

7. Ke konfiguraci jednotného přihlašování na **přímé** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [přímou podporu týmu](https://direct4b.com/ja/support.html#inquiry).

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/direct-tutorial/create_aaduser_01.png) 

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/direct-tutorial/create_aaduser_02.png) 

3. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.

    ![Vytváří se testovací uživatele služby Azure AD](./media/direct-tutorial/create_aaduser_03.png) 

4. Na **uživatele** dialogového okna stránky, proveďte následující kroky:

    ![Vytváří se testovací uživatele služby Azure AD](./media/direct-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.

### <a name="creating-a-direct-test-user"></a>Vytváří se uživatel – přímý test

V této části vytvořte uživatele Britta Simon přímo. Práce s [přímou podporu týmu](https://direct4b.com/ja/support.html#inquiry) přidat uživatele na platformě s přímým přístupem. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke směrování.

![Přiřadit uživatele][200] 

**Přiřadit Britta Simon ke směrování, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **přímé**.

    ![Konfigurace jednotného přihlašování](./media/direct-tutorial/tutorial_direct_app.png) 

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.

### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Pokud chcete otestovat v **zprostředkovatele identity iniciované režimu**:

    Po kliknutí **s přímým přístupem** dlaždici na přístupovém panelu, měli byste obdržet automaticky přihlášení k vaší **s přímým přístupem** aplikace.

2. Pokud chcete otestovat v **SP iniciované režimu**:

    a. Klikněte na **přímé** dlaždici na přístupovém panelu a budete přesměrováni na přihlašovací stránce aplikace.

    b. Zadejte vaše `subdomain` zobrazí textové pole a stiskněte by měl získat "次へ (Další)" a budete automaticky přihlášení k vaší **přímé** aplikace.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/direct-tutorial/tutorial_general_01.png
[2]: ./media/direct-tutorial/tutorial_general_02.png
[3]: ./media/direct-tutorial/tutorial_general_03.png
[4]: ./media/direct-tutorial/tutorial_general_04.png

[100]: ./media/direct-tutorial/tutorial_general_100.png

[200]: ./media/direct-tutorial/tutorial_general_200.png
[201]: ./media/direct-tutorial/tutorial_general_201.png
[202]: ./media/direct-tutorial/tutorial_general_202.png
[203]: ./media/direct-tutorial/tutorial_general_203.png
