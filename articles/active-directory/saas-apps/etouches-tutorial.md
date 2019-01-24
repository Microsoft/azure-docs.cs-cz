---
title: 'Kurz: Integrace Azure Active Directory s etouches | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a etouches.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 76cccaa8-859c-4c16-9d1d-8a6496fc7520
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 7e4dcc91ecafab6fc0b7864658b0f7724ec04bf2
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54813912"
---
# <a name="tutorial-azure-active-directory-integration-with-etouches"></a>Kurz: Integrace Azure Active Directory s etouches

V tomto kurzu se dozvíte, jak integrovat etouches s Azure Active Directory (Azure AD).

Etouches integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k etouches
- Můžete povolit uživatelům, aby automaticky získat přihlášení k etouches (Single Sign-On) s jejich účty Azure AD
- Můžete spravovat své účty na jediném místě – na webu Azure portal

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s etouches, potřebujete následující položky:

- Předplatné Azure AD
- Etouches jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání etouches z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-etouches-from-the-gallery"></a>Přidání etouches z Galerie
Konfigurace integrace etouches do služby Azure AD, budete muset přidat etouches z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat etouches z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **etouches**vyberte **etouches** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![etouches v seznamu výsledků](./media/etouches-tutorial/tutorial_etouches_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování
V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí etouches podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v etouches je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v etouches potřeba navázat.

V etouches, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s etouches, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele etouches](#create-an-etouches-test-user)**  – Pokud chcete mít protějšek Britta Simon v etouches, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci etouches.

**Ke konfiguraci Azure AD jednotné přihlašování s etouches, proveďte následující kroky:**

1. Na webu Azure Portal na **etouches** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Konfigurace jednotného přihlašování][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/etouches-tutorial/tutorial_etouches_samlbase.png)

1. Na **etouches domény a adresy URL** části, proveďte následující kroky:

    ![etouches domény a adresy URL jednotného přihlašování – informace](./media/etouches-tutorial/tutorial_etouches_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.eiseverywhere.com/saml/accounts/?sso&accountid=<ACCOUNTID>`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.eiseverywhere.com/<instance name>`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte hodnotu s skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později v tomto kurzu.
    > 

1. etouches aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atribut uživatele** aplikace. Následující snímek obrazovky ukazuje příklad pro tuto. 

    ![Atribut uživatele](./media/etouches-tutorial/tutorial_etouches_attribute.png) 

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na obrázku a proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | ------------------- | -------------------- |
    | Email | user.mail |    
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Přidat atribut](./media/etouches-tutorial/tutorial_attribute_04.png)

    ![Přidat atribut dialogového okna](./media/etouches-tutorial/tutorial_attribute_05.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/etouches-tutorial/tutorial_etouches_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/etouches-tutorial/tutorial_general_400.png)

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, postupujte následovně etouches aplikace: 

    ![Konfigurace etouches](./media/etouches-tutorial/tutorial_etouches_06.png) 

    a. Přihlaste se k **etouches** aplikace pomocí oprávnění správce.
   
    b. Přejděte **SAML** konfigurace.

    c. V **obecné nastavení** části, otevřete stažený certifikát z webu Azure portal v programu Poznámkový blok, zkopírujte jeho obsah a vložte ho do textového pole metadat zprostředkovatele identity. 

    d. Klikněte na **Uložit & zůstat** tlačítko.
  
    e. Klikněte na **Metadata aktualizace** tlačítko v sekci metadat SAML. 

    f. Tím se otevře na stránce a provádět jednotné přihlašování. Jakmile fungovat jednotné přihlašování je můžete nastavit uživatelské jméno.

    g. Do pole uživatelské jméno, vyberte **emailaddress** jak je znázorněno na následujícím obrázku. 

    h. Kopírovat **SP entity ID** hodnotu a vložte ho do **identifikátor** textové pole, která je ve **etouches domény a adresy URL** části na webu Azure portal.

    i. Kopírovat **adresu URL jednotného přihlašování / ACS** hodnotu a vložte ho do **přihlašovací adresa URL** textové pole, která je ve **etouches domény a adresy URL** části na webu Azure portal.
   
> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD
Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. V **webu Azure portal**, v levém navigačním podokně klikněte na tlačítko **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](./media/etouches-tutorial/create_aaduser_01.png) 

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin** a klikněte na tlačítko **všichni uživatelé**.
    
    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/etouches-tutorial/create_aaduser_02.png) 

1. Chcete-li otevřít **uživatele** dialogového okna, klikněte na tlačítko **přidat** horní části dialogového okna.
 
    ![Tlačítko Přidat](./media/etouches-tutorial/create_aaduser_03.png) 

1. Na **uživatele** dialogového okna stránky, proveďte následující kroky:
 
    ![Dialogové okno uživatele](./media/etouches-tutorial/create_aaduser_04.png) 

    a. V **název** textové pole, typ **BrittaSimon**.

    b. V **uživatelské jméno** textové pole, typ **e-mailová adresa** z BrittaSimon.

    c. Vyberte **zobrazit heslo** a zapište si hodnotu **heslo**.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-etouches-test-user"></a>Vytvořit testovacího uživatele etouches

V této části vytvoříte uživatele v etouches jako Britta Simon. Práce s [tým podpory etouches klienta](https://www.etouches.com/event-software/support/customer-support/) přidat uživatele na platformě etouches.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k etouches.

![Přiřazení role uživatele][200] 

**Přiřadit etouches Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **etouches**.

    ![Odkaz etouches v seznamu aplikací](./media/etouches-tutorial/tutorial_etouches_app.png) 

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202] 

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování


Cílem této části je test vaší konfigurace Azure AD jednotné přihlašování pomocí přístupového panelu.

Po kliknutí na dlaždici etouches na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci etouches.

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/etouches-tutorial/tutorial_general_01.png
[2]: ./media/etouches-tutorial/tutorial_general_02.png
[3]: ./media/etouches-tutorial/tutorial_general_03.png
[4]: ./media/etouches-tutorial/tutorial_general_04.png

[100]: ./media/etouches-tutorial/tutorial_general_100.png

[200]: ./media/etouches-tutorial/tutorial_general_200.png
[201]: ./media/etouches-tutorial/tutorial_general_201.png
[202]: ./media/etouches-tutorial/tutorial_general_202.png
[203]: ./media/etouches-tutorial/tutorial_general_203.png

