---
title: 'Kurz: Azure Active Directory integrace s Schoox | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Schoox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f8b4cdcc-cbf6-4229-9427-05632e33f942
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: jeedes
ms.openlocfilehash: bc11bf33dd257219dad2313eb6e2bf25a0732347
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210848"
---
# <a name="tutorial-azure-active-directory-integration-with-schoox"></a>Kurz: Azure Active Directory integrace s Schoox

V tomto kurzu zjistěte, jak integrovat Schoox s Azure Active Directory (Azure AD).

Integrace Schoox s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Schoox.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Schoox (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Schoox, potřebujete následující položky:

- Předplatné služby Azure AD
- Schoox jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Schoox z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-schoox-from-the-gallery"></a>Přidání Schoox z Galerie
Při konfiguraci integrace Schoox do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Schoox z galerie.

**Pokud chcete přidat Schoox z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Schoox**, vyberte **Schoox** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Schoox v seznamu výsledků](./media/schoox-tutorial/tutorial_schoox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s Schoox podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Schoox je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Schoox musí navázat.

V Schoox, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Schoox, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Schoox](#create-a-schoox-test-user)**  – Pokud chcete mít protějšek Britta Simon v Schoox propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Schoox.

**Ke konfiguraci Azure AD jednotné přihlašování s Schoox, proveďte následující kroky:**

1. Na portálu Azure na **Schoox** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/schoox-tutorial/tutorial_schoox_samlbase.png)

3. Na **Schoox domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Schoox domény a adresy URL jednotné přihlašování informace](./media/schoox-tutorial/tutorial_schoox_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL: `https://saml.schoox.com/saml/adfsmetadata`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Schoox domény a adresy URL jednotné přihlašování informace](./media/schoox-tutorial/tutorial_schoox_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://saml.schoox.com/saml/login?idpUrl=<entityID>`
    
    > [!Note]
    > `<entityID>` SAML Entity ID zkopíruje z části Stručná referenční příručka popsané později v kurzu. 

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/schoox-tutorial/tutorial_schoox_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/schoox-tutorial/tutorial_general_400.png)

7. Na **Schoox konfigurace** klikněte na tlačítko **konfigurace Schoox** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části** a použít ho k dokončení **přihlašovací adresa URL** v **Schoox domény a adresy URL** části. 

    ![Konfigurace Schoox](./media/schoox-tutorial/config.png)

8. Konfigurace jednotného přihlašování na **Schoox** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Schoox](https://www.schoox.com/help/). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/schoox-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/schoox-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/schoox-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/schoox-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-schoox-test-user"></a>Vytvoření zkušebního uživatele Schoox

V této části vytvoříte volal Britta Simon v Schoox uživatele. Práce s [tým podpory Schoox](https://www.schoox.com/help/) přidat uživatele do Schoox platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Schoox.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Schoox, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Schoox**.

    ![V seznamu aplikací na Schoox odkaz](./media/schoox-tutorial/tutorial_schoox_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Schoox na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Schoox.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/schoox-tutorial/tutorial_general_01.png
[2]: ./media/schoox-tutorial/tutorial_general_02.png
[3]: ./media/schoox-tutorial/tutorial_general_03.png
[4]: ./media/schoox-tutorial/tutorial_general_04.png

[100]: ./media/schoox-tutorial/tutorial_general_100.png

[200]: ./media/schoox-tutorial/tutorial_general_200.png
[201]: ./media/schoox-tutorial/tutorial_general_201.png
[202]: ./media/schoox-tutorial/tutorial_general_202.png
[203]: ./media/schoox-tutorial/tutorial_general_203.png

