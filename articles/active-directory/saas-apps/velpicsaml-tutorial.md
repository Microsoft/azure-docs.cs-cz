---
title: 'Kurz: Integrace Azure Active Directory s Velpic SAML | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8328fc5582fa9ebe38a23e5916ac0c3127575529
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166090"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Kurz: Integrace Azure Active Directory s Velpic SAML

V tomto kurzu se dozvíte, jak integrovat Velpic SAML s Azure Active Directory (Azure AD).

Integrace Velpic SAML s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Velpic SAML
- Uživatele, aby automaticky získat přihlášení k Velpic SAML (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD
- Můžete spravovat své účty na jediném místě – na portálu pro správu Azure

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD pomocí Velpic SAML, potřebujete následující položky:

- Předplatné Azure AD
- Velpic SAML jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Produkčním prostředí byste neměli používat, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete získat měsíční zkušební [tady](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Velpic SAML z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-velpic-saml-from-the-gallery"></a>Přidání Velpic SAML z Galerie
Konfigurace integrace Velpic SAML do služby Azure AD, budete muset přidat Velpic SAML z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Velpic SAML z galerie, postupujte následovně:**

1. V  **[portálu pro správu Azure](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![Aplikace][2]
    
1. Klikněte na tlačítko **přidat** tlačítko v horní části dialogového okna.

    ![Aplikace][3]

1. Do vyhledávacího pole zadejte **Velpic SAML**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_search.png)

1. Na panelu výsledků vyberte **Velpic SAML**a potom klikněte na tlačítko **přidat** tlačítko pro přidání aplikace.

    ![Vytváří se testovací uživatele služby Azure AD](./media/velpicsaml-tutorial/tutorial_velpicsaml_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurace a testování Azure AD jednotného přihlašování
V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování pomocí Velpic SAML podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Velpic SAML je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Velpic SAML.

Tento odkaz vztah navázaný přiřazením hodnoty **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** v Velpic SAML.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Velpic SAML, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytváří se testovací uživatele služby Azure AD](#creating-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Velpic SAML](#creating-a-velpic-saml-test-user)**  – Pokud chcete mít protějšek Britta Simon Velpic SAML, který je propojený s Azure AD reprezentace jí.
1. **[Přiřazení testovacího uživatele Azure AD](#assigning-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Testování Single Sign-On](#testing-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure Management portal a konfigurace jednotného přihlašování v aplikaci Velpic SAML.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí Velpic SAML, proveďte následující kroky:**

1. V portálu pro správu Azure na **Velpic SAML** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, jako **režimu** vyberte **přihlašování na základě SAML** chcete povolit jednotné přihlašování.
 
    ![Konfigurace jednotného přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_samlbase.png)

1. Zadejte podrobnosti v **domény Velpic SAML a adres URL** části -

    ![Konfigurace jednotného přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte hodnotu jako: `https://<sub-domain>.velpicsaml.net`

    b. V **identifikátor** vložit do textového pole **'Jednotné přihlašování na adresu URL'** hodnota `https://auth.velpic.com/saml/v2/<entity-id>/login`
    
    > [!NOTE]
    > Mějte prosím na paměti, že adresa URL přihlašování, poskytneme vám tým Velpic SAML a hodnotu identifikátoru budou k dispozici při konfiguraci jednotného přihlašování k modulu plug-in na straně Velpic SAML. Budete muset ze stránky aplikace Velpic SAML zkopírujte tuto hodnotu a vložte ho tady.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor XML ve vašem počítači.

    ![Konfigurace jednotného přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/velpicsaml-tutorial/tutorial_general_400.png)

1. V části Konfigurace Velpic SAML klikněte na tlačítko Konfigurovat Velpic SAML konfigurovat přihlašování – okno. SAML Entity ID zkopírujte z části Stručná referenční příručka.

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Velpic SAML.

1. Klikněte na **spravovat** kartu a přejít na **integrace** části, které je potřeba kliknout na **moduly plug-in** pro vytvoření nového modulu plug-in pro přihlášení.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_1.png)

1. Klikněte na **"Přidání modulu plug-in"** tlačítko.
    
    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_2.png)

1. Klikněte na **SAML** dlaždice na stránce Přidat zásuvný modul.
    
    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_3.png)

1. Zadejte název nové zásuvný modul SAML a klikněte na tlačítko **'Přidat'** tlačítko.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_4.png)

1. Zadejte podrobnosti o následujícím způsobem:

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_5.png)

    a. V **název** textového pole zadejte název modulu plug-in SAML.

    b. V **URL vystavitele** vložit do textového pole **SAML Entity ID** jste zkopírovali z **nakonfigurovat přihlašování** okna na webu Azure portal.

    c. V **zprostředkovatele metadat konfigurace** nahrát soubor metadat XML, který jste si stáhli z webu Azure portal.

    d. Můžete také povolit SAML zřizování čas tím, že **"Automatické vytváření nových uživatelů"** zaškrtávací políčko. Pokud uživatel neexistuje mezi Velpic a tento příznak není povolen, přihlášení z Azure se nezdaří. Pokud je příznak povolené uživatele se automaticky zřídí do Velpic při přihlášení. 

    e. Kopírovat **jednotné přihlašování na adrese URL** z textu a vložte ji na webu Azure Portal.
    
    f. Klikněte na **Uložit**.

### <a name="creating-an-azure-ad-test-user"></a>Vytváří se testovací uživatele služby Azure AD
Cílem této části je vytvoření zkušebního uživatele na portálu Azure Management portal volá Britta Simon.

![Vytvoření uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **Azure Management portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Vytváří se testovací uživatele služby Azure AD](./media/velpicsaml-tutorial/create_aaduser_01.png) 

1. Přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé** zobrazíte seznam uživatelů.
    
    ![Vytváří se testovací uživatele služby Azure AD](./media/velpicsaml-tutorial/create_aaduser_02.png) 

1. V horní části okna klikněte na tlačítko **přidat** otevřít **uživatele** dialogového okna.
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/velpicsaml-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Vytváří se testovací uživatele služby Azure AD](./media/velpicsaml-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="creating-a-velpic-saml-test-user"></a>Vytvoření zkušebního uživatele Velpic SAML

Tento krok se obvykle nevyžaduje jako aplikace podporuje pouze v době zřizování uživatelů. Pokud není povolena automatické zřizování uživatelů je vytvoření ruční uživatele provést, jak je popsáno níže.

Přihlaste se jako správce společnosti lokalitu Velpic SAML a proveďte následující kroky:
    
1. Klikněte na kartu spravovat a přejděte do části pro uživatele a potom klikněte na tlačítko Přidat uživatele.

    ![Přidat uživatele](./media/velpicsaml-tutorial/velpic_7.png)

1. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky.

    ![uživatel](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. V **křestní jméno** textového pole Název typu první Britta Simon.

    b. V **příjmení** textového pole zadejte příjmení Britta Simon.

    c. V **uživatelské jméno** textového pole zadejte uživatelské jméno Britta Simon.

    d. V **e-mailu** textového pole zadejte e-mailovou adresu účtu Britta Simon.

    e. Je volitelný zbývající informace, vyplní jej v případě potřeby.
    
    f. Klikněte na **ULOŽIT**.  

### <a name="assigning-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k Velpic SAML.

![Přiřadit uživatele][200] 

**Britta Simon přiřadit Velpic SAML, proveďte následující kroky:**

1. Na portálu Azure Management portal, otevřete zobrazení aplikací a přejděte do zobrazení adresáře přejít na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Velpic SAML**.

    ![Konfigurace jednotného přihlašování](./media/velpicsaml-tutorial/tutorial_velpicsaml_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Přiřadit uživatele][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Přiřadit uživatele][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="testing-single-sign-on"></a>Testování jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

1. Když kliknete na dlaždici Velpic SAML na přístupovém panelu, měli byste obdržet přihlašovací stránku Velpic SAML aplikace. Měli byste vidět **"Přihlaste se pomocí služby Azure AD"** tlačítko na přihlašovací stránce.

    ![Modul plug-in](./media/velpicsaml-tutorial/velpic_6.png)

1. Klikněte na **"Přihlaste se pomocí služby Azure AD"** tlačítko pro přihlášení k Velpic pomocí svého účtu Azure AD.


## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/velpicsaml-tutorial/tutorial_general_01.png
[2]: ./media/velpicsaml-tutorial/tutorial_general_02.png
[3]: ./media/velpicsaml-tutorial/tutorial_general_03.png
[4]: ./media/velpicsaml-tutorial/tutorial_general_04.png

[100]: ./media/velpicsaml-tutorial/tutorial_general_100.png

[200]: ./media/velpicsaml-tutorial/tutorial_general_200.png
[201]: ./media/velpicsaml-tutorial/tutorial_general_201.png
[202]: ./media/velpicsaml-tutorial/tutorial_general_202.png
[203]: ./media/velpicsaml-tutorial/tutorial_general_203.png

