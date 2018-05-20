---
title: 'Kurz: Azure Active Directory integrace s SpaceIQ | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SpaceIQ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: f76be8db123558d87dfa5c1756f5928aed97d63f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Kurz: Azure Active Directory integrace s SpaceIQ

V tomto kurzu zjistěte, jak integrovat SpaceIQ s Azure Active Directory (Azure AD).

Integrace SpaceIQ s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k SpaceIQ.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k SpaceIQ (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s SpaceIQ, potřebujete následující položky:

- Předplatné služby Azure AD
- SpaceIQ jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání SpaceIQ z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-spaceiq-from-the-gallery"></a>Přidání SpaceIQ z Galerie
Při konfiguraci integrace SpaceIQ do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS SpaceIQ z galerie.

**Pokud chcete přidat SpaceIQ z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **SpaceIQ**, vyberte **SpaceIQ** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![SpaceIQ v seznamu výsledků](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s SpaceIQ podle testovacího uživatele názvem "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v SpaceIQ je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v SpaceIQ musí navázat.

V SpaceIQ, přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** k navázání vztahu odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s SpaceIQ, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele SpaceIQ](#create-a-spaceiq-test-user)**  – Pokud chcete mít protějšek Britta Simon v SpaceIQ propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci SpaceIQ.

**Ke konfiguraci Azure AD jednotné přihlašování s SpaceIQ, proveďte následující kroky:**

1. Na portálu Azure na **SpaceIQ** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

3. Na **SpaceIQ domény a adresy URL** část, proveďte následující kroky:

    ![SpaceIQ domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL: `https://api.spaceiq.com`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE] 
    > Tyto hodnoty aktualizujte skutečná adresa URL odpovědi a identifikátor, který je vysvětlen později v tomto kurzu.
 
4. Na **SAML podpisový certifikát** klikněte na tlačítko **(certifikát Base64)** a potom uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-spaceiq-tutorial/tutorial_general_400.png)

6. Na **SpaceIQ konfigurace** klikněte na tlačítko **konfigurace SpaceIQ** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části.**

    ![Konfigurace SpaceIQ](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_configure.png) 

7.  Otevřete nové okno prohlížeče a pak se přihlaste k prostředí SpaceIQ jako správce.

8. Jakmile jste přihlášeni, klikněte na přihlašovací stavebnice v pravé horní a pak klikněte na **"Integrace"**

    ![Nastavení účtu](./media/active-directory-saas-spaceiq-tutorial/setting1.png) 

9. V části **všechny zřizování & jednotného přihlašování k**, klikněte na **Azure** dlaždice přidat instanci Azure jako poskytovatel identity.

    ![Ikona SAML](./media/active-directory-saas-spaceiq-tutorial/setting2.png)

10. V **jednotného přihlašování k** dialogové okno pole, proveďte následující kroky:

    ![Nastavení ověřování SAML](./media/active-directory-saas-spaceiq-tutorial/setting3.png)

    a. V **URL vystavitele SAML** pole, vložte **SAML Entity ID** hodnota zkopírována z okna konfigurace aplikace Azure AD.
    
    b. Kopírování **SAML zpětného volání adresu URL koncového bodu (jen pro čtení)** a vložte hodnotu v **adresa URL odpovědi** pole na portálu Azure v části SpaceIQ **domény a adresy URL** části.
    
    c. Kopírování **identifikátor URI cílové skupiny SAML (jen pro čtení)** a vložte hodnotu v **identifikátor** pole na portálu Azure v části SpaceIQ **domény a adresy URL** části.

    d. V poznámkovém bloku otevřete soubor stažený certifikát, kopírovat obsah a vložte ho **certifikát X.509** pole.
    
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-spaceiq-test-user"></a>Vytvoření zkušebního uživatele SpaceIQ

V této části vytvoříte volal Britta Simon v SpaceIQ uživatele. Pracovní [tým podpory SpaceIQ](mailto:eng@spaceiq.com) přidat uživatele do SpaceIQ platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu SpaceIQ.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon SpaceIQ, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **SpaceIQ**.

    ![V seznamu aplikací na SpaceIQ odkaz](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici SpaceIQ na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci SpaceIQ.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_203.png

