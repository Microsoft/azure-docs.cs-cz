---
title: 'Kurz: Azure Active Directory integrace s ZwayamSSO | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ZwayamSSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7754344c-34d6-4764-a368-e1dbfe876c0c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: 4d834cfb5bd6cff30b7e1dfebb3dfd6e651d315a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zwayamsso"></a>Kurz: Azure Active Directory integrace s ZwayamSSO

V tomto kurzu zjistěte, jak integrovat ZwayamSSO s Azure Active Directory (Azure AD).

Integrace ZwayamSSO s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k ZwayamSSO.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k ZwayamSSO (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s ZwayamSSO, potřebujete následující položky:

- Předplatné služby Azure AD
- ZwayamSSO jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání ZwayamSSO z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zwayamsso-from-the-gallery"></a>Přidání ZwayamSSO z Galerie
Při konfiguraci integrace ZwayamSSO do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS ZwayamSSO z galerie.

**Pokud chcete přidat ZwayamSSO z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **ZwayamSSO**, vyberte **ZwayamSSO** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![ZwayamSSO v seznamu výsledků](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s ZwayamSSO podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v ZwayamSSO je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v ZwayamSSO musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s ZwayamSSO, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele ZwayamSSO](#create-a-zwayamsso-test-user)**  – Pokud chcete mít protějšek Britta Simon v ZwayamSSO propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci ZwayamSSO.

**Ke konfiguraci Azure AD jednotné přihlašování s ZwayamSSO, proveďte následující kroky:**

1. Na portálu Azure na **ZwayamSSO** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_samlbase.png)

3. Na **ZwayamSSO domény a adresy URL** část, proveďte následující kroky:

    ![ZwayamSSO domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_url.png)

    a. V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://sso.zwayam.com/zwayam-saml/zwayam-saml/saml/login?idp=<SAML Entity ID>`

    b. V **identifikátor** textovému poli, zadejte adresu URL: `https://sso.zwayam.com/zwayam-saml/saml/metadata`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečné. **SAML Entity ID** hodnota se vysvětluje dále v tomto kurzu.

4. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_400.png)

6. Na **ZwayamSSO konfigurace** klikněte na tlačítko **konfigurace ZwayamSSO** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **části Stručná referenční příručka** a vložte **SAML Entity ID** hodnotu **přihlašovací adresa URL** textového pole v  **ZwayamSSO domény a adresy URL** části.

    ![Konfigurace ZwayamSSO](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_configure.png) 

7. Konfigurace jednotného přihlašování na **ZwayamSSO** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory ZwayamSSO](mailto:opendoors@zwayam.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-zwayamsso-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zwayamsso-test-user"></a>Vytvoření zkušebního uživatele ZwayamSSO

V této části vytvoříte volal Britta Simon v ZwayamSSO uživatele. Práce s [tým podpory ZwayamSSO](mailto:opendoors@zwayam.com) přidat uživatele do ZwayamSSO platformy. Uživatelé musí být vytvořen a aktivovat dříve, než použijete jednotné přihlašování.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu ZwayamSSO.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ZwayamSSO, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **ZwayamSSO**.

    ![V seznamu aplikací na ZwayamSSO odkaz](./media/active-directory-saas-zwayamsso-tutorial/tutorial_zwayamsso_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici ZwayamSSO na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci ZwayamSSO.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zwayamsso-tutorial/tutorial_general_203.png

