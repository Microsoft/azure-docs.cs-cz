---
title: 'Kurz: Azure Active Directory integrace s organizačního diagramu nyní | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a nyní organizačního diagramu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: c46a2e68699365d818c83be2b1b0ff2ce2ef88ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Kurz: Azure Active Directory integrace s nyní organizačního diagramu

V tomto kurzu zjistěte, jak integrovat nyní organizačního diagramu s Azure Active Directory (Azure AD).

Integrace nyní organizačního diagramu s Azure AD poskytuje následující výhody:

- Můžete ovládat ve službě Azure AD, který má přístup k nyní organizačního diagramu.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k organizačního diagramu nyní (jednotné přihlášení) s jejich účty Azure AD.
- Můžete spravovat vaše účty v jednom centrálním místě - portálu Azure.

Pokud chcete vědět, další informace o integraci aplikací SaaS v Azure AD, najdete v části [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s nyní organizačního diagramu, potřebujete následující položky:

- Předplatné služby Azure AD
- Organizačního diagramu nyní jednotného přihlašování povolené předplatné

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu můžete otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénáři uvedeném v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání nyní organizačního diagramu z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-orgchart-now-from-the-gallery"></a>Přidání nyní organizačního diagramu z Galerie
Konfigurace integrace organizačního diagramu nyní do Azure AD, potřebujete přidat nyní organizačního diagramu z Galerie si na seznam spravovaných aplikací SaaS.

**Pokud chcete přidat nyní organizačního diagramu z galerie, postupujte takto:**

1. V  **[portál Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na tlačítko **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte na **podnikové aplikace, které**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko horní dialogové okno.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **organizačního diagramu nyní**, vyberte **organizačního diagramu nyní** z panelu výsledků klikněte **přidat** tlačítko Přidat aplikaci.

    ![Organizační diagram nyní v seznamu výsledků](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování Azure AD jednotné přihlašování

V této části můžete nakonfigurovat, testovací Azure AD jednotné přihlašování s organizačního diagramu nyní podle testovacího uživatele názvem "Britta Simon".

Azure AD pro jednotné přihlašování pro práci, musí vědět, co příslušného uživatele v organizační diagram nyní je pro uživatele ve službě Azure AD. Jinými slovy odkaz vztah mezi uživatele Azure AD a související uživatele v organizační diagram teď je potřeba vytvořit.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s organizačního diagramu nyní, budete muset provést následující stavební bloky:

1. **[Konfigurovat Azure AD jednotné přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete povolit uživatelům tuto funkci používat.
2. **[Vytvořit testovací uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit uživatele s nyní organizačního diagramu testovací](#create-an-orgchart-now-test-user)**  – Pokud chcete mít protějšek Britta Simon v organizační diagram nyní propojeném s Azure AD reprezentace daného uživatele.
4. **[Přiřadit testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotné přihlašování.
5. **[Test jednotného přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, zda je funkční konfigurace.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurovat Azure AD jednotné přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure a nakonfigurovat jednotné přihlašování v aplikaci teď organizačního diagramu.

**Ke konfiguraci Azure AD jednotné přihlašování s nyní organizačního diagramu, proveďte následující kroky:**

1. Na portálu Azure na **organizačního diagramu nyní** stránky integrace aplikací, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace propojení přihlášení][4]

2. Na **jednotného přihlašování** dialogovém okně, vyberte **režimu** jako **na základě SAML přihlašování** umožňující jednotného přihlašování.
 
    ![Jediné přihlášení dialogové okno](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. Na **organizačního diagramu teď domény a adresy URL** část, pokud chcete nakonfigurovat aplikace **IDP** iniciované režimu:

    ![Organizační diagram teď domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    V **identifikátor** textovému poli, zadejte adresu URL: `https://sso2.orgchartnow.com`

4. Zkontrolujte **zobrazit upřesňující nastavení adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace **SP** iniciované režimu:

    ![Organizační diagram teď domény a adresy URL jednotné přihlašování informace](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    V **přihlašovací adresa URL** textovému poli, zadejte adresu URL pomocí následujícího vzorce: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` SAML Entity ID zkopíruje z části Stručná referenční příručka popsané později v kurzu.

5. Na **SAML podpisový certifikát** klikněte na tlačítko **soubor XML s metadaty** a potom uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurujte jeden přihlašování uložit tlačítko](./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_400.png)
    
7. Na **organizačního diagramu teď konfigurace** klikněte na tlačítko **teď nakonfigurovat organizační diagram** otevřete **konfigurovat přihlášení** okno. Kopírování **SAML Entity ID** z **Stručná referenční příručka části** a použít ho k dokončení **přihlašovací adresa URL** v **organizačního diagramu teď domény a adresy URL části**.

    ![Konfigurace nyní organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. Konfigurace jednotného přihlašování na **organizačního diagramu nyní** straně, budete muset odeslat stažené **soubor XML s metadaty** k [tým podpory organizačního diagramu nyní](mailto:ocnsupport@officeworksoftware.com). Nastavují toto nastavení tak, aby měl jednotné přihlašování SAML připojení správně nastavena na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovací uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na portálu Azure, názvem Britta Simon.

   ![Vytvořit testovací uživatele Azure AD][100]

**Vytvoření zkušebního uživatele ve službě Azure AD, proveďte následující kroky:**

1. Na portálu Azure, v levém podokně klikněte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na **všichni uživatelé**.

    !["Uživatelé a skupiny" a "Všichni uživatelé" odkazy](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit hesla** zaškrtněte políčko a zapište si ji hodnotu, která se zobrazí v **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-orgchart-now-test-user"></a>Vytvořit testovací uživatele s nyní organizačního diagramu

Povolit uživatelům Azure AD Přihlaste se k organizační diagram nyní, musí být zřízená do nyní organizačního diagramu. 

1. Organizační diagram nyní podporuje za běhu zřizování, který je ve výchozím nastavení povolené. Nový uživatel se vytvoří během pokusu o přístup k organizačního diagramu nyní, pokud ještě neexistuje. Zřizování funkce uživatelů za běhu pouze vytvoří **jen pro čtení** uživatele při požadavek jednotné přihlašování pochází z rozpoznaný IDP a e-mailu v kontrolního výrazu SAML nebyl nalezen v seznamu uživatelů. Pro tento automatické zřizování funkce budete muset vytvořit skupinu přístupu s názvem **Obecné** v nyní organizačního diagramu. Postupujte níže uvedených pokynů k vytvoření přístupové skupiny:

    a. Přejděte na **spravovat skupiny** po kliknutí na možnost **zařízení** v pravém horním rohu uživatelského rozhraní.

    ![Skupiny teď organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manage.png)  

    b. Vyberte **přidat** ikonu a název skupiny **Obecné** klikněte **OK**. 

    ![Přidejte teď organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Vyberte složky, které chcete mít přístup k uživatelům obecné nebo jen pro čtení:

    ![Složky teď organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zámek** složky, aby je mohl upravovat pouze správci. Stiskněte klávesu **OK**.

    ![Zámek nyní organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Chcete-li vytvořit **správce** uživatelů a **pro čtení a zápis** uživatele, musíte ručně vytvořit uživatele Chcete-li získat přístup k jejich úroveň oprávnění prostřednictvím jednotného přihlašování. K poskytnutí uživatelského účtu, proveďte následující kroky:

    a. Přihlaste se k organizační diagram nyní jako správce zabezpečení.

    b.  Klikněte na **nastavení** v pravém horním rohu a pak přejděte do **spravovat uživatele**.

    ![Nastavení nyní organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klikněte na **přidat** a proveďte následující kroky:

    ![Spravovat nyní organizačního diagramu](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * V **ID uživatele** textovému poli, zadejte ID uživatele jako **brittasimon@contoso.com**.

    * V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele jako **brittasimon@contoso.com**.

    * Klikněte na tlačítko **Add** (Přidat).
    
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit testovacího uživatele Azure AD

V této části povolíte Britta Simon používat Azure jednotné přihlašování pomocí udělení přístupu nyní organizačního diagramu.

![Přiřadit role uživatele][200] 

**Chcete nyní organizačního diagramu přiřadit Britta Simon, proveďte následující kroky:**

1. Na portálu Azure otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace, které** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **organizačního diagramu nyní**.

    ![Odkaz organizačního diagramu nyní v seznamu aplikací](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatelé a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogové okno.

    ![V podokně Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogovém okně, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogové okno.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části můžete vyzkoušet Azure AD jeden přihlašování konfiguraci pomocí přístupového panelu.

Když kliknete na dlaždici nyní organizačního diagramu na přístupovém panelu, jste měli získat automaticky přihlášení k aplikaci nyní organizačního diagramu.
Další informace o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů k integraci aplikací SaaS službou Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_203.png

