---
title: 'Kurz: Integrace Azure Active Directory se službou Envi MMIS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Envi MMIS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 70066f1c29849b77c67710eb908ef2a340cdc45f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047656"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Kurz: Integrace Azure Active Directory se službou Envi MMIS

V tomto kurzu se dozvíte, jak integrovat Envi MMIS s Azure Active Directory (Azure AD).

Envi MMIS integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Envi MMIS.
- Uživatele, aby automaticky získat přihlášení k Envi MMIS (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Envi MMIS, potřebujete následující položky:

- S předplatným služby Azure AD
- Envi MMIS jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Envi MMIS z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-envi-mmis-from-the-gallery"></a>Přidání Envi MMIS z Galerie
Konfigurace integrace Envi MMIS do služby Azure AD, budete muset přidat Envi MMIS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Envi MMIS z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Envi MMIS**vyberte **Envi MMIS** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Envi MMIS v seznamu výsledků](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Envi MMIS podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Envi MMIS je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Envi MMIS potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Envi MMIS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele Envi MMIS](#create-an-envi-mmis-test-user)**  – Pokud chcete mít protějšek Britta Simon Envi MMIS, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Envi MMIS.

**Ke konfiguraci Azure AD jednotné přihlašování s Envi MMIS, proveďte následující kroky:**

1. Na webu Azure Portal na **Envi MMIS** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

3. Na **Envi MMIS domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Envi MMIS domény a adresy URL jednotného přihlašování – informace](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.<CUSTOMER DOMAIN>.com/Account`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

4. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Envi MMIS domény a adresy URL jednotného přihlašování – informace](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory Envi MMIS klienta](mailto:support@ioscorp.com) k získání těchto hodnot.

5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/tutorial_general_400.png)

7. V okně jiné webové prohlížeče Přihlaste se ke webu Envi MMIS jako správce.

8. Klikněte na **Moje doména** kartu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure1.png)

9. Klikněte na **Upravit**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure2.png)

10. Vyberte **použít vzdálené ověření** zaškrtávací políčko a potom vyberte **přesměrování protokolu HTTP** z **typ ověřování** rozevíracího seznamu.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure3.png)

11. Vyberte **prostředky** kartu a potom klikněte na tlačítko **nahrát metadat**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure4.png)

12. V **nahrát metadat** automaticky otevírané okno, proveďte následující kroky:

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure5.png)

    a. Vyberte **souboru** možnost **nahrát z** rozevíracího seznamu.

    b. Nahrát soubor metadat stažené z webu Azure portal tak, že vyberete **zvolte ikonu souboru**.

    c. Klikněte na tlačítko **OK**.

13. Po nahrání souboru staženého metadat budou obsazeny pole, který automaticky. Klikněte na tlačítko **aktualizace**

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/envimmis-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/envimmis-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/envimmis-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/envimmis-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-envi-mmis-test-user"></a>Vytvořit testovacího uživatele Envi MMIS

Přihlaste se k Envi MMIS Azure AD uživatelům umožnit, musí být poskytnuty do Envi MMIS.  
V případě Envi MMIS zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se na web společnosti Envi MMIS jako správce.

2. Klikněte na **seznamu uživatelů** kartu.

    ![Přidat zaměstnance](./media/envimmis-tutorial/user1.png)

3. Klikněte na tlačítko **přidat uživatele** tlačítko.

    ![Přidat zaměstnance](./media/envimmis-tutorial/user2.png)

4. V **přidat uživatele** části, proveďte následující kroky:

    ![Přidat zaměstnance](./media/envimmis-tutorial/user3.png)

    a. V **uživatelské jméno** , jako je textové pole, zadejte uživatelské jméno účtu Britta Simon **brittasimon@contoso.com**.
    
    b. V **křestní jméno** , jako je textové pole, typ křestní jméno BrittaSimon **Britta**.

    c. V **příjmení** , jako je textové pole, typ příjmení BrittaSimon **Simon**.

    d. Zadejte název uživatele v **název** textového pole.
    
    e. V **e-mailovou adresu** , jako je textové pole, typ e-mailovou adresu účtu Britta Simon **brittasimon@contoso.com**.

    f. V **jednotné uživatelské jméno** , jako je textové pole, zadejte uživatelské jméno účtu Britta Simon **brittasimon@contoso.com**.

    g. Klikněte na **Uložit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Envi MMIS.

![Přiřazení role uživatele][200] 

**Přiřadit Envi MMIS Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **Envi MMIS**.

    ![Odkaz Envi MMIS v seznamu aplikací](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Envi MMIS na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Envi MMIS.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png

