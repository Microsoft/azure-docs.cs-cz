---
title: 'Kurz: Azure Active Directory integrace s CylancePROTECT | Microsoft Docs'
description: "Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CylancePROTECT."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea392d8c-c8aa-4475-99d0-b08524ef0f3a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: jeedes
ms.openlocfilehash: 312e8ee3b924fe76e770d8e24e4fc8221e31bffb
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cylanceprotect"></a>Kurz: Azure Active Directory integrace s CylancePROTECT

V tomto kurzu zjistěte, jak integrovat CylancePROTECT s Azure Active Directory (Azure AD).

Integrace CylancePROTECT s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k CylancePROTECT.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k CylancePROTECT (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s CylancePROTECT, potřebujete následující položky:

- Předplatné služby Azure AD
- CylancePROTECT jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání CylancePROTECT z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-cylanceprotect-from-the-gallery"></a>Přidání CylancePROTECT z Galerie
Při konfiguraci integrace CylancePROTECT do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS CylancePROTECT z galerie.

**Pokud chcete přidat CylancePROTECT z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **CylancePROTECT**, vyberte **CylancePROTECT** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![CylancePROTECT v seznamu výsledků](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s CylancePROTECT podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v CylancePROTECT je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v CylancePROTECT musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s CylancePROTECT, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele CylancePROTECT](#create-a-cylanceprotect-test-user)**  – Pokud chcete mít protějšek Britta Simon v CylancePROTECT propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci CylancePROTECT.

**Ke konfiguraci Azure AD jednotné přihlašování s CylancePROTECT, proveďte následující kroky:**

1. Na portálu Azure na **CylancePROTECT** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_samlbase.png)

3. Na **CylancePROTECT domény a adresy URL** část, proveďte následující kroky:

    ![CylancePROTECT domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL:
    
    | Oblast | Hodnota adresy URL |
    |----------|---------|
    | Asie a Tichomoří – severovýchod (APNE1)| ` https://login-apne1.cylance.com`|
    | Asie a Tichomoří – jihovýchod (AU) | `https://login-au.cylance.com` |
    | Střední Evropa (EUC1)|`https://login-euc1.cylance.com`|
    | Severní Amerika|`https://login.cylance.com`|
    | Jižní Amerika (SAE1)|`https://login-sae1.cylance.com`|
    
    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL:
    
    | Oblast | Hodnota adresy URL |
    |----------|---------|
    | Asie a Tichomoří – severovýchod (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Asie a Tichomoří – jihovýchod (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Střední Evropa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Severní Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Jižní Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

4. Na **SAML podpisový certifikát** klikněte na **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_certificate.png) 

5. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_400.png)

6. Na **CylancePROTECT konfigurace** klikněte na tlačítko **konfigurace CylancePROTECT** otevřete **konfigurovat přihlášení** okno. Kopírování **Sign-Out adresu URL, SAML Entity ID a SAML jeden přihlašování adresa URL služby** z **Stručná referenční příručka části.**

    ![Konfigurace CylancePROTECT](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_configure.png) 

7. Konfigurace jednotného přihlašování na **CylancePROTECT** straně, budete muset odeslat stažené **Certificate(Base64), adresa URL Sign-Out, SAML Entity ID a SAML jeden přihlašování adresa URL služby** k [ Tým podpory CylancePROTECT](mailto:Ibrahim.nafea@toyota.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

> [!TIP]
> Teď si můžete přečíst stručným verzi tyto pokyny uvnitř [portál Azure](https://portal.azure.com), zatímco nastavujete aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace, které** jednoduše klikněte na položku **jednotné přihlašování** kartě a přístup v embedded dokumentaci prostřednictvím **konfigurace** v dolní části. Můžete přečíst další informace o funkci embedded dokumentace: [vložených dokumentace k Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-cylanceprotect-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-cylanceprotect-test-user"></a>Vytvoření zkušebního uživatele CylancePROTECT

V této části vytvoříte volal Britta Simon v CylancePROTECT uživatele. Práce s [tým podpory CylancePROTECT](mailto:Ibrahim.nafea@toyota.com) přidat uživatele do CylancePROTECT platformy. Držitel účtu Azure Active Directory bude dostávat e-mailu a postupujte podle odkaz potvrďte svůj účet, pak se změní na aktivní.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu CylancePROTECT.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon CylancePROTECT, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **CylancePROTECT**.

    ![V seznamu aplikací na CylancePROTECT odkaz](./media/active-directory-saas-cylanceprotect-tutorial/tutorial_cylanceprotect_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici CylancePROTECT na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci CylancePROTECT.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cylanceprotect-tutorial/tutorial_general_203.png

