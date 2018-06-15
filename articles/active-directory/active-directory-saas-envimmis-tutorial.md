---
title: 'Kurz: Azure Active Directory integrace s Envi MMIS | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 0d338f7f56e668c25378403e88a6532b8b8573c5
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344265"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Kurz: Azure Active Directory integrace s Envi MMIS

V tomto kurzu zjistěte, jak integrovat Envi MMIS s Azure Active Directory (Azure AD).

Integrace Envi MMIS s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k Envi MMIS.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Envi MMIS (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Envi MMIS, potřebujete následující položky:

- Předplatné služby Azure AD
- Envi MMIS jednotné přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Envi MMIS z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-envi-mmis-from-the-gallery"></a>Přidání Envi MMIS z Galerie
Při konfiguraci integrace Envi MMIS do služby Azure AD musíte přidat do seznamu spravovaných aplikací SaaS Envi MMIS z galerie.

**Pokud chcete přidat Envi MMIS z galerie, proveďte následující kroky:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Envi MMIS**, vyberte **Envi MMIS** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Envi MMIS v seznamu výsledků](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s Envi MMIS podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co uživatel protějškem v Envi MMIS je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatelské v Envi MMIS musí navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Envi MMIS, je třeba dokončit následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovací uživatele s Envi MMIS](#create-an-envi-mmis-test-user)**  – Pokud chcete mít protějšek Britta Simon v Envi MMIS propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci Envi MMIS.

**Ke konfiguraci Azure AD jednotné přihlašování s Envi MMIS, proveďte následující kroky:**

1. Na portálu Azure na **Envi MMIS** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. Na **Envi MMIS domény a adresy URL** část, proveďte následující kroky, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Envi MMIS domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url.png)

    a. V **identifikátor** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. V **adresa URL odpovědi** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Envi MMIS domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Tyto hodnoty nejsou skutečné. Tyto hodnoty aktualizujte se skutečným identifikátorem, adresa URL odpovědi a přihlašovací adresa URL. Obraťte se na [tým podpory klienta MMIS Envi](mailto:support@ioscorp.com) k získání těchto hodnot.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/tutorial_general_400.png)

7. V okně prohlížeče jiný web Přihlaste se k webu Envi MMIS jako správce.

8. Klikněte na **Moje domény** kartě.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/configure1.png)

9. Klikněte na **Upravit**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/configure2.png)

10. Vyberte **ověřování vzdálené** zaškrtávací políčko a potom vyberte **přesměrování protokolu HTTP** z **typ ověřování** rozevíracího seznamu.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/configure3.png)

11. Vyberte **prostředky** a pak klikněte **nahrát Metadata**.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/configure4.png)

12. V **nahrát Metadata** místní, proveďte následující kroky:

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/configure5.png)

    a. Vyberte **soubor** možnost z **nahrát z** rozevíracího seznamu.

    b. Nahrát soubor metadat stažený z portálu Azure tak, že vyberete **zvolte ikonu souboru**.

    c. Klikněte na tlačítko **OK**.

13. Po nahrání souboru stažené metadata pole získat vyplní automaticky. Klikněte na tlačítko **aktualizace**

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-envimmis-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-envimmis-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-envimmis-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-envimmis-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-envi-mmis-test-user"></a>Vytvořit uživatele s Envi MMIS testu

Pokud chcete povolit uživatelům Azure AD přihlášení k Envi MMIS, musí být zřízená do Envi MMIS.  
V případě Envi MMIS zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k serveru vaší společnosti Envi MMIS jako správce.

2. Klikněte na **seznam uživatelů** kartě.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-envimmis-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele** tlačítko.

    ![Můžete přidat zaměstnance](./media/active-directory-saas-envimmis-tutorial/user2.png)

4. V **přidat uživatele** část, proveďte následující kroky:

    ![Můžete přidat zaměstnance](./media/active-directory-saas-envimmis-tutorial/user3.png)

    a. V **uživatelské jméno** jako typ uživatelské jméno účtu Britta Simon textovému poli, **brittasimon@contoso.com**.
    
    b. V **křestní jméno** jako typ křestní jméno BrittaSimon textovému poli, **Britta**.

    c. V **příjmení** jako typ příjmení BrittaSimon textovému poli, **Simon**.

    d. Zadejte název uživatele v **název** textového pole.
    
    e. V **e-mailovou adresu** jako typ e-mailovou adresu účtu Britta Simon textovému poli, **brittasimon@contoso.com**.

    f. V **jednotné přihlašování uživatelské jméno** jako typ uživatelské jméno účtu Britta Simon textovému poli, **brittasimon@contoso.com**.

    g. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu Envi MMIS.

![Přiřadit role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Envi MMIS, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Envi MMIS**.

    ![V seznamu aplikací na Envi MMIS odkaz](./media/active-directory-saas-envimmis-tutorial/tutorial_envimmis_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici Envi MMIS na přístupovém panelu, můžete by měl získat automaticky přihlášení k aplikaci Envi MMIS.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-envimmis-tutorial/tutorial_general_203.png

