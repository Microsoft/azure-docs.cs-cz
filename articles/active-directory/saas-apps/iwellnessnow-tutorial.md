---
title: 'Kurz: Integrace Azure Active Directory se službou iWellnessNow | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iWellnessNow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24ffc841-7a77-481c-9cc4-6f8bda58fe66
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 4f5ff9906ec5a4f57d64704a4b10cf7027878e72
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39047072"
---
# <a name="tutorial-azure-active-directory-integration-with-iwellnessnow"></a>Kurz: Integrace Azure Active Directory se službou iWellnessNow

V tomto kurzu se dozvíte, jak integrovat iWellnessNow s Azure Active Directory (Azure AD).

IWellnessNow integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k iWellnessNow.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iWellnessNow (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iWellnessNow, potřebujete následující položky:

- S předplatným služby Azure AD
- IWellnessNow jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iWellnessNow z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-iwellnessnow-from-the-gallery"></a>Přidání iWellnessNow z Galerie
Konfigurace integrace iWellnessNow do služby Azure AD, budete muset přidat iWellnessNow z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iWellnessNow z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **iWellnessNow**vyberte **iWellnessNow** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![iWellnessNow v seznamu výsledků](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí iWellnessNow podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v iWellnessNow je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iWellnessNow potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iWellnessNow, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele iWellnessNow](#create-an-iwellnessnow-test-user)**  – Pokud chcete mít protějšek Britta Simon v iWellnessNow, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci iWellnessNow.

**Ke konfiguraci Azure AD jednotné přihlašování s iWellnessNow, proveďte následující kroky:**

1. Na webu Azure Portal na **iWellnessNow** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_samlbase.png)

3. Na **iWellnessNow domény a adresy URL** části, pokud máte **soubor metadat poskytovatele služeb** a chcete nakonfigurovat aplikace v **zprostředkovatele identity** iniciované režimu, proveďte Následující kroky:

    ![nahrávání iWellnessNow domény a adresy URL jednotného přihlašování](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_upload.png)

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![uploadconfig iWellnessNow domény a adresy URL jednotného přihlašování](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_uploadconfig.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.
    
    c. Po úspěšném dokončení nahrávání **soubor metadat poskytovatele služeb** **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v  **iWellnessNow domény a adresy URL** části textového pole, jak je znázorněno níže:

    ![iWellnessNow domény a adresy URL jednotné přihlašování – informace](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url3.png)

4. Pokud nemáte **soubor metadat poskytovatele služeb** a chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![iWellnessNow domény a adresy URL jednotné přihlašování – informace](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `http://<CustomerName>.iwellnessnow.com`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<CustomerName>.iwellnessnow.com/ssologin`

5. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![iWellnessNow domény a adresy URL jednotné přihlašování – informace](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<CustomerName>.iwellnessnow.com/`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory klienta iWellnessNow](mailto:info@iwellnessnow.com) k získání těchto hodnot.

5. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/iwellnessnow-tutorial/tutorial_general_400.png)
    
7. Ke konfiguraci jednotného přihlašování na **iWellnessNow** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory iWellnessNow](mailto:info@iwellnessnow.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/iwellnessnow-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/iwellnessnow-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/iwellnessnow-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/iwellnessnow-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-iwellnessnow-test-user"></a>Vytvořit testovacího uživatele iWellnessNow

V této části vytvoříte uživatele v iWellnessNow jako Britta Simon. Práce s [tým podpory iWellnessNow](mailto:info@iwellnessnow.com) přidat uživatele na platformě iWellnessNow. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iWellnessNow.

![Přiřazení role uživatele][200] 

**Přiřadit iWellnessNow Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **iWellnessNow**.

    ![Odkaz iWellnessNow v seznamu aplikací](./media/iwellnessnow-tutorial/tutorial_iwellnessnow_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici iWellnessNow na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci iWellnessNow.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iwellnessnow-tutorial/tutorial_general_01.png
[2]: ./media/iwellnessnow-tutorial/tutorial_general_02.png
[3]: ./media/iwellnessnow-tutorial/tutorial_general_03.png
[4]: ./media/iwellnessnow-tutorial/tutorial_general_04.png

[100]: ./media/iwellnessnow-tutorial/tutorial_general_100.png

[200]: ./media/iwellnessnow-tutorial/tutorial_general_200.png
[201]: ./media/iwellnessnow-tutorial/tutorial_general_201.png
[202]: ./media/iwellnessnow-tutorial/tutorial_general_202.png
[203]: ./media/iwellnessnow-tutorial/tutorial_general_203.png

