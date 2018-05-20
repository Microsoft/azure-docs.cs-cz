---
title: 'Kurz: Azure Active Directory integrace s Carlson Wagonlit cesta | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Carlson Wagonlit cesta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2745e165-94ab-43b1-970a-4547b4e5b501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: jeedes
ms.openlocfilehash: 80873f9dfe94a5e83865b3617ad8dba3198a2dd7
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-carlson-wagonlit-travel"></a>Kurz: Azure Active Directory integrace s Carlson Wagonlit cesta

V tomto kurzu zjistěte, jak integrovat Carlson Wagonlit cesta s Azure Active Directory (Azure AD).

Integrace Carlson Wagonlit cesta s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Carlson Wagonlit cesta.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Carlson Wagonlit cesta (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Carlson Wagonlit cesta, potřebujete následující položky:

- Předplatné služby Azure AD
- Carlson Wagonlit cesta jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Carlson Wagonlit cesta z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-carlson-wagonlit-travel-from-the-gallery"></a>Přidání Carlson Wagonlit cesta z Galerie
Při konfiguraci integrace Carlson Wagonlit cesta do služby Azure AD potřebujete přidat Carlson Wagonlit cesta z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat Carlson Wagonlit cesta z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Carlson Wagonlit cesta**, vyberte **Carlson Wagonlit cesta** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Cesta Wagonlit Carlson v seznamu výsledků](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Carlson Wagonlit cesta podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Carlson Wagonlit cesta je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Carlson Wagonlit cesta musí navázat.

V Carlson Wagonlit cesta, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Carlson Wagonlit cesta, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele cesta Wagonlit Carlson](#create-a-carlson-wagonlit-travel-test-user)**  – Pokud chcete mít protějšek Britta Simon v Carlson Wagonlit cesta, propojené služby Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Carlson Wagonlit cesta.

**Ke konfiguraci Azure AD jednotné přihlašování s Carlson Wagonlit cesta, proveďte následující kroky:**

1. Na portálu Azure na **Carlson Wagonlit cesta** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_samlbase.png)

3. Na **Carlson Wagonlit cesta domény a adresy URL** část, proveďte následující kroky:

    ![Carlson Wagonlit cesta domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_url.png)

    V **identifikátor** textovému poli, zadejte hodnotu: `cwt-stage`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_400.png)

6. Konfigurace jednotného přihlašování na **Carlson Wagonlit cesta** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory cesta Wagonlit Carlson](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-carlson-wagonlit-travel-test-user"></a>Vytvoření zkušebního uživatele Carlson Wagonlit cesta

V této části vytvoříte uživatele volal Britta Simon v Carlson Wagonlit cesta. Práce s [tým podpory Carlson Wagonlit cesta](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html) přidat uživatele do platformy Carlson Wagonlit cesta. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Carlson Wagonlit cesta.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit cestovní Wagonlit Carlson Britta Simon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Carlson Wagonlit cesta**.

    ![Cesta Wagonlit Carlson odkaz v seznamu aplikací](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Carlson Wagonlit cesta na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Carlson Wagonlit cesta.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_203.png

