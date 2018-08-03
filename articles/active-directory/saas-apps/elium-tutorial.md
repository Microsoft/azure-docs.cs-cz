---
title: 'Kurz: Integrace Azure Active Directory se službou Elium | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: dfa90474632b2cf18055e0ba95994f120cb293ef
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447780"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Kurz: Integrace Azure Active Directory se službou Elium

V tomto kurzu se dozvíte, jak integrovat Elium s Azure Active Directory (Azure AD).

Elium integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Elium.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Elium (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Elium, potřebujete následující položky:

- S předplatným služby Azure AD
- Elium jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Elium z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-elium-from-the-gallery"></a>Přidání Elium z Galerie
Konfigurace integrace Elium do služby Azure AD, budete muset přidat Elium z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Elium z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Elium**vyberte **Elium** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Elium v seznamu výsledků](./media/elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Elium podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Elium je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Elium potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Elium, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele Elium](#create-an-elium-test-user)**  – Pokud chcete mít protějšek Britta Simon Elium, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Elium.

**Ke konfiguraci Azure AD jednotné přihlašování s Elium, proveďte následující kroky:**

1. Na webu Azure Portal na **Elium** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/elium-tutorial/tutorial_elium_samlbase.png)

1. Na **Elium domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu:

    ![Elium domény a adresy URL jednotného přihlašování – informace](./media/elium-tutorial/tutorial_elium_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Elium domény a adresy URL jednotného přihlašování – informace](./media/elium-tutorial/tutorial_elium_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Zobrazí se tyto hodnoty z **metadata souboru SP** ke stažení na `https://<platform-domain>.elium.com/login/saml2/metadata`, což je vysvětleno dále v tomto kurzu.

1. Aplikace Elium očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/tutorial_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogovém okně Konfigurovat atribut tokenu SAML, jak je znázorněno na předchozím obrázku a proveďte následující kroky:
           
    | Název atributu | Hodnota atributu |   
    | ---------------| ----------------|
    | e-mail   |User.Mail |
    | křestní_jméno| user.givenname |
    | Příjmení| user.surname|
    | funkce| user.jobtitle|
    | Společnosti| User.CompanyName|
    
    > [!NOTE]
    > Jedná se o výchozí deklarací identity. **Deklarace identity e-mailu je povinné jenom**. Pro JIT zřizování také e-mailu jenom deklarace identity je povinné. Další vlastní deklarace identity se může lišit od platformy jednoho zákazníka na jiné platformě zákazníka.

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/tutorial_attribute_04.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/tutorial_attribute_05.png)

    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    d. Obor názvů, ponechte prázdné.
    
    e. Klikněte na tlačítko **OK**. 

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/elium-tutorial/tutorial_elium_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/elium-tutorial/tutorial_general_400.png)
    
1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Elium jako správce.

1. Klikněte na **profilu uživatele** v pravém horním rohu a pak vyberte **správu**.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user1.png)

1. Vyberte **zabezpečení** kartu.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user2.png)

1. Přejděte dolů k položce **jednotné přihlašování (SSO)** části a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user3.png)

    a. Zkopírujte hodnotu **ověřte, že ověřování typu SAML2 funguje pro váš účet** a vložte ji **přihlašovací adresa URL** textového pole na **Elium domény a adresy URL** oddíl ve službě Azure portál.

    > [!NOTE]
    > Po dokončení konfigurace jednotného přihlašování, můžete vždy přístup k výchozí stránka vzdálené přihlášení na následující adrese URL: `https://<platform_domain>/login/regular/login` 

    b. Vyberte **povolit SAML2 federace** zaškrtávací políčko.

    c. Vyberte **JIT zřizování** zaškrtávací políčko.

    d. Otevřít **SP metadat** kliknutím na **Stáhnout** tlačítko.

    e. Hledat **entityID** v **SP metadat** soubor, zkopírujte **entityID** hodnotu a vložte ji **identifikátor** textového pole na  **Elium domény a adresy URL** části webu Azure Portal. 

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user4.png)

    f. Vyhledejte **AssertionConsumerService** v **SP metadat** soubor, zkopírujte **umístění** hodnotu a vložte ji **adresy URL odpovědi** textové pole na **Elium domény a adresy URL** části webu Azure Portal.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user5.png)

    g. Do poznámkového bloku otevřete soubor stažený metadat z webu Azure portal, zkopírujte obsah a vložte ho do **metadat zprostředkovatele identity** textového pole.

    h. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/elium-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/elium-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/elium-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/elium-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-elium-test-user"></a>Vytvořit testovacího uživatele Elium

Cílem této části je vytvořte uživatele Britta Simon v Elium. Elium podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k Elium, pokud ještě neexistuje.
>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Elium](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Elium použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Elium Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Elium**.

    ![Odkaz Elium v seznamu aplikací](./media/elium-tutorial/tutorial_elium_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Elium na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Elium.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/elium-tutorial/tutorial_general_01.png
[2]: ./media/elium-tutorial/tutorial_general_02.png
[3]: ./media/elium-tutorial/tutorial_general_03.png
[4]: ./media/elium-tutorial/tutorial_general_04.png

[100]: ./media/elium-tutorial/tutorial_general_100.png

[200]: ./media/elium-tutorial/tutorial_general_200.png
[201]: ./media/elium-tutorial/tutorial_general_201.png
[202]: ./media/elium-tutorial/tutorial_general_202.png
[203]: ./media/elium-tutorial/tutorial_general_203.png

