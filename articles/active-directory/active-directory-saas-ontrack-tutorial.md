---
title: 'Kurz: Azure Active Directory integrace s OnTrack | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OnTrack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2cafba2-3b4a-4471-ba34-80f6a96ff2b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jeedes
ms.openlocfilehash: 76a7bd34c3fddc70e025af52235be619f04fa0d9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349824"
---
# <a name="tutorial-azure-active-directory-integration-with-ontrack"></a>Kurz: Azure Active Directory integrace s OnTrack

V tomto kurzu zjistěte, jak integrovat OnTrack s Azure Active Directory (Azure AD).

Integrace OnTrack s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k OnTrack.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k OnTrack (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OnTrack, potřebujete následující položky:

- Předplatné služby Azure AD
- OnTrack jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání OnTrack z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-ontrack-from-the-gallery"></a>Přidání OnTrack z Galerie
Při konfiguraci integrace OnTrack do služby Azure AD potřebujete přidat OnTrack z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat OnTrack z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **OnTrack**, vyberte **OnTrack** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![OnTrack v seznamu výsledků](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s OnTrack podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v OnTrack je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v OnTrack musí navázat.

V OnTrack, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OnTrack, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s OnTrack](#create-an-ontrack-test-user)**  – Pokud chcete mít protějšek Britta Simon v OnTrack propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci OnTrack.

**Ke konfiguraci Azure AD jednotné přihlašování s OnTrack, proveďte následující kroky:**

1. Na portálu Azure na **OnTrack** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_samlbase.png)

3. Na **OnTrack domény a adresy URL** část, proveďte následující kroky:

    ![OnTrack domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_url.png)

    a. V **identifikátor** textovému poli,
    
    Pro testovací prostředí zadejte adresu URL: `https://staging.insigniagroup.com/sso`

    Pro produkční prostředí zadejte adresu URL: `https://oeaccessories.com/sso`

    b. V **adresa URL odpovědi** textovému poli,
    
    Pro testovací prostředí zadejte adresu URL: `https://indie.staging.insigniagroup.com/sso/autonation.aspx`

    Pro produkční prostředí zadejte adresu URL: `https://igaccessories.com/sso/autonation.aspx`

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_certificate.png)

5. Aplikace OnTrack očekává SAML kontrolní výrazy ve specifickém formátu, který vyžaduje, můžete přidat mapování vlastních atributů do vaší konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnoty těchto atributů z "**uživatelské atributy**" části na stránce integrace aplikace. 

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute.png)

6. V **uživatelské atributy** části na **jednotného přihlašování** dialogové okno, nakonfigurujte atribut tokenu SAML, jak je vidět na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | -------------- | ----------------|    
    | Role uživatele      | "42F432" |
    | Hyperion kódu  | "12345" |

    > [!NOTE]
    > **Role uživatele** a **Hyperion kódu** atributy jsou namapované s Autonation uživatelské Role a obchodník kódu. Tyto hodnoty jsou pouze příklad, použijte správný kód pro svoji integraci. Obraťte se na [Autonation podporu](mailto:CustomerService@insigniagroup.com) pro tyto hodnoty.
    
    a. Klikněte na tlačítko **přidat atribut** otevřete **přidat atribut** dialogové okno.

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_04.png)   

    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-ontrack-tutorial/tutorial_attribute_05.png)

    b. V **název** textovému poli, zadejte název atributu, který je uvedený na příslušném řádku.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu, který je uvedený na příslušném řádku.
    
    d. Klikněte na tlačítko **OK**.

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-ontrack-tutorial/tutorial_general_400.png)

8. Konfigurace jednotného přihlašování na **OnTrack** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory OnTrack](mailto:CustomerService@insigniagroup.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-ontrack-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-ontrack-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-ontrack-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-ontrack-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-ontrack-test-user"></a>Vytvořit uživatele s OnTrack testu

V této části vytvoříte volal Britta Simon v OnTrack uživatele. Práce s [tým podpory OnTrack](mailto:CustomerService@insigniagroup.com) přidat uživatele do OnTrack platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu OnTrack.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon OnTrack, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **OnTrack**.

    ![V seznamu aplikací na OnTrack odkaz](./media/active-directory-saas-ontrack-tutorial/tutorial_ontrack_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici OnTrack na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci OnTrack.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ontrack-tutorial/tutorial_general_203.png

