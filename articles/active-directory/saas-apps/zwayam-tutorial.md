---
title: 'Kurz: Azure Active Directory integrace s Zwayam | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zwayam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7754344c-34d6-4764-a368-e1dbfe876c0c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: jeedes
ms.openlocfilehash: f51f44e7564991231746b8448d26057995b15510
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220975"
---
# <a name="tutorial-azure-active-directory-integration-with-zwayam"></a>Kurz: Azure Active Directory integrace s Zwayam

V tomto kurzu zjistěte, jak integrovat Zwayam s Azure Active Directory (Azure AD).

Integrace Zwayam s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Zwayam.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Zwayam (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Zwayam, potřebujete následující položky:

- Předplatné služby Azure AD
- Zwayam jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zwayam z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zwayam-from-the-gallery"></a>Přidání Zwayam z Galerie
Při konfiguraci integrace Zwayam do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Zwayam z galerie.

**Pokud chcete přidat Zwayam z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zwayam**, vyberte **Zwayam** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Zwayam v seznamu výsledků](./media/zwayam-tutorial/tutorial_Zwayam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zwayam podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Zwayam je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Zwayam musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zwayam, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zwayam](#create-a-zwayam-test-user)**  – Pokud chcete mít protějšek Britta Simon v Zwayam propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Zwayam.

**Ke konfiguraci Azure AD jednotné přihlašování s Zwayam, proveďte následující kroky:**

1. Na portálu Azure na **Zwayam** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/zwayam-tutorial/tutorial_Zwayam_samlbase.png)

3. Na **Zwayam domény a adresy URL** část, proveďte následující kroky:

    ![Zwayam domény a adresy URL jednotné přihlašování informace](./media/zwayam-tutorial/tutorial_Zwayam_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://sso.zwayam.com/zwayam-saml/zwayam-saml/saml/login?idp=<SAML Entity ID>`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://sso.zwayam.com/zwayam-saml/saml/metadata`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. **SAML Entity ID** hodnota se vysvětluje dále v tomto kurzu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/zwayam-tutorial/tutorial_Zwayam_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/zwayam-tutorial/tutorial_general_400.png)

6. Na **Zwayam konfigurace** klikněte na tlačítko **konfigurace Zwayam** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **části Stručná referenční příručka** a vložte **SAML Entity ID** hodnotu **přihlašovací adresa URL** textového pole v  **Zwayam domény a adresy URL** části.

    ![Konfigurace Zwayam](./media/zwayam-tutorial/tutorial_Zwayam_configure.png) 

7. Konfigurace jednotného přihlašování na **Zwayam** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory Zwayam](mailto:opendoors@zwayam.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zwayam-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/zwayam-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zwayam-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zwayam-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zwayam-test-user"></a>Vytvoření zkušebního uživatele Zwayam

V této části vytvoříte volal Britta Simon v Zwayam uživatele. Práce s [tým podpory Zwayam](mailto:opendoors@zwayam.com) přidat uživatele do Zwayam platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Zwayam.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Zwayam, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zwayam**.

    ![V seznamu aplikací na Zwayam odkaz](./media/zwayam-tutorial/tutorial_Zwayam_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Zwayam na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci Zwayam.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zwayam-tutorial/tutorial_general_01.png
[2]: ./media/zwayam-tutorial/tutorial_general_02.png
[3]: ./media/zwayam-tutorial/tutorial_general_03.png
[4]: ./media/zwayam-tutorial/tutorial_general_04.png

[100]: ./media/zwayam-tutorial/tutorial_general_100.png

[200]: ./media/zwayam-tutorial/tutorial_general_200.png
[201]: ./media/zwayam-tutorial/tutorial_general_201.png
[202]: ./media/zwayam-tutorial/tutorial_general_202.png
[203]: ./media/zwayam-tutorial/tutorial_general_203.png

