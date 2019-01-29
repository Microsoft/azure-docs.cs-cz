---
title: 'Kurz: Integrace Azure Active Directory s FloQast | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a FloQast.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 013cb57d-567c-44d0-a119-e6ba6e607153
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: f46714d2d4860abd1857e6ae16f98848678336aa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55184766"
---
# <a name="tutorial-azure-active-directory-integration-with-floqast"></a>Kurz: Integrace Azure Active Directory s FloQast

V tomto kurzu se dozvíte, jak integrovat FloQast s Azure Active Directory (Azure AD).

FloQast integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k FloQast.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k FloQast (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s FloQast, potřebujete následující položky:

- Předplatné Azure AD
- FloQast jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání FloQast z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-floqast-from-the-gallery"></a>Přidání FloQast z Galerie
Konfigurace integrace FloQast do služby Azure AD, budete muset přidat FloQast z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat FloQast z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **FloQast**vyberte **FloQast** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![FloQast v seznamu výsledků](./media/floqast-tutorial/tutorial_floqast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí FloQast podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v FloQast je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v FloQast potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s FloQast, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele FloQast](#create-a-floqast-test-user)**  – Pokud chcete mít protějšek Britta Simon FloQast, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci FloQast.

**Ke konfiguraci Azure AD jednotné přihlašování s FloQast, proveďte následující kroky:**

1. Na webu Azure Portal na **FloQast** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/floqast-tutorial/tutorial_floqast_samlbase.png)

1. Na **FloQast domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![FloQast domény a adresy URL jednotného přihlašování – informace](./media/floqast-tutorial/tutorial_floqast_url.png)

     V **identifikátor** textového pole zadejte adresu URL: `https://go.floqast.com/`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![FloQast domény a adresy URL jednotného přihlašování – informace](./media/floqast-tutorial/tutorial_floqast_url1.png)

     V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://go.floqast.com/login/sso`
     
1. FloQast aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurovat Single Sign-On atribut](./media/floqast-tutorial/tutorial_floqast_attribute.png)
    
1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |    
    | FirstName           | user.givenname |
    | LastName        | user.surname |
    | Email       | user.mail    |

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurovat Single Sign-On přidat](./media/floqast-tutorial/tutorial_attribute_04.png)

    ![Nakonfigurovat jednotné přihlašování Addattb](./media/floqast-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Nechte **Namespace** prázdné.
    
    e. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači a proveďte následující kroky:

    ![Odkaz ke stažení certifikátu](./media/floqast-tutorial/tutorial_floqast_certificate.png)

    a. Zkontrolujte **zobrazit pokročilé nastavení podepisování certifikátu**.

    ![Kontrolní výraz certifikátu](./media/floqast-tutorial/tutorial_floqast_certificateassertion.png)

    b. Vyberte **podepisování možnost** jako **přihlašování SAML odpověď a kontrolní výraz**.

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/floqast-tutorial/tutorial_general_400.png)
    
1. Ke konfiguraci jednotného přihlašování na **FloQast** straně, je nutné odeslat na stažený **soubor XML s metadaty** k [tým podpory FloQast](mailto:support@floqast.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/floqast-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/floqast-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/floqast-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/floqast-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-floqast-test-user"></a>Vytvoření zkušebního uživatele FloQast

V této části vytvoříte uživatele v FloQast jako Britta Simon. Práce s [tým podpory FloQast](mailto:support@floqast.com) přidat uživatele na platformě FloQast. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k FloQast použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit FloQast Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **FloQast**.

    ![Odkaz FloQast v seznamu aplikací](./media/floqast-tutorial/tutorial_floqast_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici FloQast na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci FloQast.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/floqast-tutorial/tutorial_general_01.png
[2]: ./media/floqast-tutorial/tutorial_general_02.png
[3]: ./media/floqast-tutorial/tutorial_general_03.png
[4]: ./media/floqast-tutorial/tutorial_general_04.png

[100]: ./media/floqast-tutorial/tutorial_general_100.png

[200]: ./media/floqast-tutorial/tutorial_general_200.png
[201]: ./media/floqast-tutorial/tutorial_general_201.png
[202]: ./media/floqast-tutorial/tutorial_general_202.png
[203]: ./media/floqast-tutorial/tutorial_general_203.png

