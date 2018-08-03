---
title: 'Kurz: Integrace Azure Active Directory pomocí organizačního diagramu teď | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a nyní organizačního diagramu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: e23d76074f4b428b672e0cd5aeeaba99d080a4cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435930"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Kurz: Integrace Azure Active Directory se nyní organizačního diagramu

V tomto kurzu se dozvíte, jak integrovat organizačního diagramu teď s Azure Active Directory (Azure AD).

Integrace organizačního diagramu teď s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup do tohoto okamžiku organizačního diagramu.
- Uživatele, aby automaticky získat přihlášení k nyní organizační diagram (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s organizačního diagramu teď, budete potřebovat následující položky:

- S předplatným služby Azure AD
- Organizačního diagramu teď jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání organizačního diagramu teď z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-orgchart-now-from-the-gallery"></a>Přidání organizačního diagramu teď z Galerie
Konfigurace integrace organizačního diagramu teď do služby Azure AD, budete muset přidat organizačního diagramu teď z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat organizačního diagramu teď z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **organizačního diagramu teď**vyberte **organizačního diagramu teď** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Organizační diagram nyní v seznamu výsledků](./media/orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování pomocí organizačního diagramu teď podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšek istrovat hned organizačního diagramu je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v organizační diagram teď třeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s nyní organizačního diagramu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele organizačního diagramu teď](#create-an-orgchart-now-test-user)**  – Pokud chcete mít protějšek Britta Simon organizačního diagramu teď propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci nyní organizačního diagramu.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí organizačního diagramu teď, proveďte následující kroky:**

1. Na webu Azure Portal na **organizačního diagramu teď** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

1. Na **organizačního diagramu teď domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Organizační diagram nyní domény a adresy URL jednotného přihlašování – informace](./media/orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    V **identifikátor** textového pole zadejte adresu URL: `https://sso2.orgchartnow.com`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Organizační diagram nyní domény a adresy URL jednotného přihlašování – informace](./media/orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` zkopírováno z část Stručná referenční příručka, je popsáno dále v kurzu SAML Entity ID.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/orgchartnow-tutorial/tutorial_general_400.png)
    
1. Na **organizačního diagramu teď konfigurace** klikněte na tlačítko **konfigurace organizačního diagramu teď** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML Entity ID** z **Stručná referenční příručka části** a použijte ho k dokončení **přihlašovací adresa URL** v **organizačního diagramu teď domény a adresy URL části**.

    ![Konfigurace nyní organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

1. Ke konfiguraci jednotného přihlašování na **organizačního diagramu teď** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory organizačního diagramu teď](mailto:ocnsupport@officeworksoftware.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/orgchartnow-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/orgchartnow-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/orgchartnow-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/orgchartnow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-orgchart-now-test-user"></a>Vytvořit testovacího uživatele nyní organizačního diagramu

Pokud chcete povolit Azure AD uživatelům se přihlásit do tohoto okamžiku organizačního diagramu, musí být poskytnuty do organizačního diagramu teď. 

1. Organizačního diagramu teď podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Nový uživatel se vytvoří během pokusu o přístup k nyní organizačního diagramu, pokud ještě neexistuje. Zřizování funkce uživatelů v čase vytvoří pouze **jen pro čtení** uživatele, pokud požadavek jednotného přihlašování pochází z rozpoznaný zprostředkovatele identity a e-mailu v kontrolní výraz SAML nebyl nalezen v seznamu uživatelů. Pro tento automatické zřizování funkci je potřeba vytvořit skupinu přístupu s názvem **Obecné** istrovat hned organizačního diagramu. Postupujte prosím podle níže uvedený postup k vytvoření přístupové skupiny:

    a. Přejděte **spravovat skupiny** možnosti po kliknutí na tlačítko **ozubeného kolečka** v pravém horním rohu uživatelského rozhraní.

    ![Nyní organizačního diagramu skupiny](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Vyberte **přidat** ikonu a název skupiny **Obecné** klikněte **OK**. 

    ![Přidejte teď organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Vyberte složky, které chcete mít přístup k uživatelům obecné nebo jen pro čtení:

    ![Organizační diagram nyní složky](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zámek** složky tak, aby pouze správci je můžou upravit. Stiskněte klávesu **OK**.

    ![Nyní organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

1. Chcete-li vytvořit **správce** uživatelů a **r/w** uživatelů, musíte ručně vytvořit uživatel získal přístup k jejich úrovně oprávnění prostřednictvím jednotného přihlašování. K poskytnutí uživatelského účtu, postupujte následovně:

    a. Přihlaste se k organizační diagram nyní jako správce zabezpečení.

    b.  Klikněte na **nastavení** v pravém horním rohu a pak přejděte do **spravovat uživatele**.

    ![Organizační diagram nyní nastavení](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klikněte na **přidat** a proveďte následující kroky:

    ![Správa nyní organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * V **ID uživatele** textového pole zadejte ID uživatele jako **brittasimon@contoso.com**.

    * V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon@contoso.com**.

    * Klikněte na tlačítko **Add** (Přidat).
    
### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do tohoto okamžiku organizačního diagramu.

![Přiřazení role uživatele][200] 

**Přiřadit nyní organizačního diagramu Britta Simon, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **organizačního diagramu teď**.

    ![Odkaz organizačního diagramu teď v seznamu aplikací](./media/orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici organizačního diagramu teď na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci nyní organizačního diagramu.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/orgchartnow-tutorial/tutorial_general_203.png

