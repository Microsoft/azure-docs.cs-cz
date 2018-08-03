---
title: 'Kurz: Integrace Azure Active Directory se službou přiblížení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přiblížení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 57ae31245a356a4cd5769fe71ef471922bf6faf9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440130"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Kurz: Integrace Azure Active Directory se službou přiblížení

V tomto kurzu se dozvíte, jak integrovat přiblížení s Azure Active Directory (Azure AD).

Přiblížení integrace s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke zvětšení.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k přiblížení (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přiblížení, potřebujete následující položky:

- S předplatným služby Azure AD
- Přiblížení jediného přihlášení povolený předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání přiblížení z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-zoom-from-the-gallery"></a>Přidání přiblížení z Galerie
Ke konfiguraci integrace přiblížení či oddálení do služby Azure AD, budete muset přidat přiblížení na váš seznam spravovaných aplikací SaaS z galerie.

**Přiblížení přidat z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **přiblížení**vyberte **přiblížení** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Lupa v seznamu výsledků](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí zvětšení na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v přiblížení je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v přiblížení.

V přiblížení, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s zvětšení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele přiblížení](#create-a-zoom-test-user)**  – Pokud chcete mít protějšek Britta Simon zvětšení, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci přiblížení.

**Ke konfiguraci Azure AD jednotné přihlašování s přiblížení, proveďte následující kroky:**

1. Na webu Azure Portal na **přiblížení** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

1. Na **přiblížení domény a adresy URL** části, proveďte následující kroky:

    ![Přiblížení domény a adresy URL jednotného přihlašování – informace](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.zoom.us`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `<companyname>.zoom.us`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory přiblížení klienta](https://support.zoom.us/hc) k získání těchto hodnot.

1. Přiblížení aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. 

    ![Konfigurace jednotného přihlašování](./media/zoom-tutorial/tutorial_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu | Hodnota Namespace |
    | ------------------- | -----------|--------- |    
    | E-mailová adresa | User.Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Jméno | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Příjmení | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Telefonní číslo | User.telephoneNumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Oddělení | User.Department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Konfigurace jednotného přihlašování](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. V **Namespace** textového pole zadejte hodnotu oboru názvů, který je zobrazený pro tento řádek.
    
    e. Klikněte na tlačítko **OK**. 
 
1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/zoom-tutorial/tutorial_zoom_certificate.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/zoom-tutorial/tutorial_general_400.png)

1. Na **přiblížení konfigurace** klikněte na tlačítko **konfigurace přiblížení** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace přiblížení](./media/zoom-tutorial/tutorial_zoom_configure.png)

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti přiblížení jako správce.

1. Klikněte na tlačítko **Single Sign-On** kartu.
   
    ![Karta přihlašování](./media/zoom-tutorial/IC784700.png "jednotného přihlašování")

1. Klikněte na tlačítko **řízení zabezpečení** kartu a potom přejděte **Single Sign-On** nastavení.

1. V části jednotného přihlašování proveďte následující kroky:
   
    ![Jednotné přihlašování – část](./media/zoom-tutorial/IC784701.png "jednotného přihlašování")
   
    a. V **přihlašovací adresa URL stránky** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.
   
    b. V **adresy URL odhlašovací stránky** textového pole vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal.
     
    c. V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    d. V **vystavitele** textového pole vložte hodnotu **SAML Entity ID** zkopírovanou z webu Azure portal. 

    e. Klikněte na **Uložit**.

    > [!NOTE] 
    > Další informace najdete v dokumentaci k přiblížení [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/zoom-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/zoom-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/zoom-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/zoom-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-zoom-test-user"></a>Vytvoření zkušebního uživatele přiblížení

Chcete-li povolit Azure AD uživatelům přihlášení přiblížit, musí být poskytnuty do přiblížení. V případě přiblížení zřizování se ruční úlohy.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se k vaší **přiblížení** společnosti serveru jako správce.
 
1. Klikněte na tlačítko **správu účtů** kartu a potom klikněte na tlačítko **Správa uživatelů**.

1. V části Správa uživatelů, klikněte na tlačítko **přidat uživatele**.
   
    ![Správa uživatelů](./media/zoom-tutorial/IC784703.png "Správa uživatelů")

1. Na **přidat uživatele** stránce, proveďte následující kroky:
   
    ![Přidání uživatelů](./media/zoom-tutorial/IC784704.png "přidat uživatele")
   
    a. Jako **typ uživatele**vyberte **základní**.

    b. V **e-mailů** textové pole, zadejte e-mailovou adresu platnou Azure AD účtu, který jste ke zřízení.

    c. Klikněte na tlačítko **Add** (Přidat).

> [!NOTE]
> Můžete použít jakékoli jiné přiblížení uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných přiblížení ke zřízení služby Azure Active Directory uživatelské účty.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu přiblížit.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon přiblížit, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **přiblížení**.

    ![Přiblížení odkaz v seznamu aplikací](./media/zoom-tutorial/tutorial_zoom_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici přiblížení na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci přiblížení.

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png

