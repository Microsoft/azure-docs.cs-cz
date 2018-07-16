---
title: 'Kurz: Integrace Azure Active Directory se Zscalerem privátní přístup správce | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Zscalerem privátní přístup správce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 0be7f71f5995be269bb630491003678e0640699b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055164"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Kurz: Integrace Azure Active Directory se Zscalerem privátní přístup správce

V tomto kurzu se dozvíte, jak integrovat Zscalerem privátní přístup správce Azure Active Directory (Azure AD).

Integrace Zscalerem privátní přístup správce s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Zscalerem privátní přístup správce.
- Uživatele, aby automaticky získat přihlášeného k Zscalerem privátní přístup správce (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD se Zscalerem privátní přístup správce, potřebujete následující položky:

- S předplatným služby Azure AD
- Zscalerem privátní přístup správce jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Zscalerem privátní přístup správce z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Přidání Zscalerem privátní přístup správce z Galerie
Ke konfiguraci integrace správce přístupu Zscalerem privátní do služby Azure AD, budete muset přidat Zscalerem privátní přístup správce v galerii na váš seznam spravovaných aplikací SaaS.

**Pro přidání správce Zscalerem privátní přístup z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Zscalerem privátní přístup správce**vyberte **Zscalerem privátní přístup správce** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Zscalerem privátní přístup správce v seznamu výsledků](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Zscalerem privátní přístup správce na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Zscalerem privátní přístup správce je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v soukromé Zscalerem přístup správce.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se Zscalerem privátní přístup správce, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele Zscalerem privátní přístup správce](#create-a-zscaler-private-access-administrator-test-user)**  – Pokud chcete mít protějšek Britta Simon v privátní Zscalerem správce přístupu, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Zscalerem privátní přístup správce.

**Ke konfiguraci Azure AD jednotného přihlašování se Zscalerem privátní přístup správce, postupujte následovně:**

1. Na webu Azure Portal na **Zscalerem privátní přístup správce** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Na **Zscalerem privátní přístup správce domény a adresy URL** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Zscalerem privátní přístup správce domény a adresy URL jednotného přihlašování – informace](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Zkontrolujte **zobrazit pokročilé nastavení URL**

    d. V **RelayState** textového pole zadejte hodnotu: `idpadminsso`

4.  Pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu proveďte následující kroky:

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory Zscalerem privátní přístup správce](https://help.zscaler.com/zpa-submit-ticket) k získání těchto hodnot.
 
5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. V okně prohlížeče jiných webových, přihlášení k Zscalerem privátní přístup správce jako správce.

8. V horní části klikněte na **správu** a přejděte do **ověřování** klikněte na **konfigurace zprostředkovatele identity**.

    ![Správce Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. V pravém horním rohu, klikněte na tlačítko **přidat konfigurace zprostředkovatele identity**. 

    ![Addidp Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Na **přidat konfigurace zprostředkovatele identity** stránku, proveďte následující kroky:
 
    ![Idpselect Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klikněte na tlačítko **vybrat soubor** nahrát stažený soubor metadat z Azure AD **nahrát soubor metadat zprostředkovatele identity** pole.

    b. Načte **metadat zprostředkovatele identity** ze služby Azure AD a naplní všechny informace o polích, jak je znázorněno níže.

    ![Idpconfig Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Vyberte **jednotné přihlašování** jako **správce**.

    d. Zadejte název domény z **domén** pole.
    
    e. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Vytvoření zkušebního uživatele Zscalerem privátní přístup správce

Přihlaste se k Zscalerem privátní přístup správce Azure AD uživatelům povolit, se musí být poskytnuty do Zscalerem privátní přístup správce. V případě Zscalerem privátní přístup správce, zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Připojte se k webu Zscalerem privátní přístup správce společnosti jako správce.

2. V horní části klikněte na **správu** a přejděte do **ověřování** klikněte na **konfigurace zprostředkovatele identity**.

    ![Správce Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klikněte na tlačítko **správci** z levé nabídky.

    ![Správce Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. V pravém horním rohu, klikněte na tlačítko **přidat správce**:

    ![Zscalerem privátní přístup správce přidat správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. V **přidat správce** stránce, proveďte následující kroky:

    ![Správce uživatelů Zscalerem privátní přístup správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. V **uživatelské jméno** textového pole zadejte e-mailu uživatele, jako je **BrittaSimon@contoso.com**.

    b. V **heslo** textového pole zadejte heslo.

    c. V **potvrzení hesla** textového pole zadejte heslo.

    d. Vyberte **Role** jako **Zscalerem privátní přístup správce**.

    e. V **e-mailu** textového pole zadejte e-mailu uživatele, jako je **BrittaSimon@contoso.com**.

    f. V **Phone** textového pole zadejte telefonní číslo.

    g. V **časové pásmo** textové pole, vyberte časové pásmo.

    h. Klikněte na **Uložit**.  

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Zscalerem privátní přístup správce.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon k Zscalerem privátní přístup správce, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Zscalerem privátní přístup správce**.

    ![Odkaz Zscalerem privátní přístup správce v seznamu aplikací](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Zscalerem privátní přístup správce na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Zscalerem privátní přístup správce.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

