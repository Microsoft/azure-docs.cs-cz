---
title: 'Kurz: Integrace Azure Active Directory se službou Boomi | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Boomi.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 40d034ff-7394-4713-923d-1f8f2ed8bf36
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: cf925e0e0e7b6b4c10b6b21d17214f91473a9026
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432945"
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>Kurz: Integrace Azure Active Directory se službou Boomi

V tomto kurzu se dozvíte, jak integrovat Boomi s Azure Active Directory (Azure AD).

Boomi integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Boomi.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Boomi (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Boomi, potřebujete následující položky:

- S předplatným služby Azure AD
- Boomi jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Boomi z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-boomi-from-the-gallery"></a>Přidání Boomi z Galerie
Konfigurace integrace Boomi do služby Azure AD, budete muset přidat Boomi z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Boomi z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Boomi**vyberte **Boomi** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Boomi v seznamu výsledků](./media/boomi-tutorial/tutorial_boomi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí Boomi podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v Boomi je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Boomi potřeba navázat.

V Boomi, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Boomi, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření zkušebního uživatele Boomi](#create-a-boomi-test-user)**  – Pokud chcete mít protějšek Britta Simon Boomi, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Boomi.

**Ke konfiguraci Azure AD jednotné přihlašování s Boomi, proveďte následující kroky:**

1. Na webu Azure Portal na **Boomi** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/boomi-tutorial/tutorial_boomi_samlbase.png)

1. Na **Boomi domény a adresy URL** části, proveďte následující kroky:

    ![Boomi domény a adresy URL jednotného přihlašování – informace](./media/boomi-tutorial/tutorial_boomi_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL: `https://platform.boomi.com/`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://platform.boomi.com/sso/<boomi-tenant>/saml`

    > [!NOTE] 
    > Hodnota adresy URL odpovědi není skutečný. Skutečná adresa URL odpovědi zaktualizujte příslušnou hodnotu. Kontakt [tým podpory Boomi](https://boomi.com/company/contact/) má být získána hodnota.
 
1. Boomi aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte prosím následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z "**atributy uživatele**" části na stránce aplikací pro integraci. Následující snímek obrazovky ukazuje příklad pro tuto.
    
    ![Konfigurace jednotného přihlašování](./media/boomi-tutorial/tutorial_attribute.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogové okno, pro každý řádek je znázorněno v následující tabulce, proveďte následující kroky:

    | Název atributu | Hodnota atributu |
    | -------------- | --------------- |
    | FEDERATION_ID | User.Mail |
    
    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.
    
    ![Konfigurace jednotného přihlašování](./media/boomi-tutorial/tutorial_officespace_04.png)
    
    ![Konfigurace jednotného přihlašování](./media/boomi-tutorial/tutorial_attribute_05.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **OK**.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **Certificate(Base64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/boomi-tutorial/tutorial_boomi_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/boomi-tutorial/tutorial_general_400.png)

1. Na **Boomi konfigurace** klikněte na tlačítko **nakonfigurovat Boomi** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![Konfigurace Boomi](./media/boomi-tutorial/tutorial_boomi_configure.png) 

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti Boomi. 

1. Přejděte do **název společnosti** a přejděte na **nastavit**.

1. Klikněte na tlačítko **možnosti jednotného přihlašování** kartě a proveďte následující kroky.

    ![Konfigurace jednotného přihlašování na straně aplikace](./media/boomi-tutorial/tutorial_boomi_11.png)

    a. Zkontrolujte **povolit jednotné přihlašování SAML** zaškrtávací políčko.

    b. Klikněte na tlačítko **Import** na kterou odešlete certifikát stažený z Azure AD, aby **certifikát poskytovatele Identity**.
    
    c. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **SAML jednotné přihlašování – adresa URL služby** z okna konfigurace aplikace Azure AD.

    d. Jako **Id umístění federačních**vyberte **federace je v elementu FEDERATION_ID atribut** přepínač. 

    e. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/boomi-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/boomi-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/boomi-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/boomi-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
  
### <a name="create-a-boomi-test-user"></a>Vytvoření zkušebního uživatele Boomi

Chcete-li povolit uživatele Azure AD se přihlaste k Boomi, musí být poskytnuty do Boomi. V případě Boomi zřizování se ruční úlohy.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>K poskytnutí uživatelského účtu, postupujte následovně:

1. Přihlaste se na web společnosti Boomi jako správce.

1. Po přihlášení, přejděte na **Správa uživatelů** a přejděte na **uživatelé**.

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_001.png "uživatelů")

1. Klikněte na tlačítko **+** ikonu a **rolí uživatele přidat/zachovat** otevře se dialogové okno.

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_002.png "uživatelů")

    ![Uživatelé](./media/boomi-tutorial/tutorial_boomi_003.png "uživatelů")

    a. V **e-mailovou adresu uživatele** , jako je textové pole, typ e-mailu uživatele BrittaSimon@contoso.com.
    
    b. V **křestní jméno** textového pole Název typu prvního uživatele, jako je Britta.

    c. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon.
    
    d. Zadejte uživatele **federace**. Každý uživatel musí mít ID federace, která jednoznačně identifikuje uživatele v rámci účtu.
    
    e. Přiřazení **standardní uživatel** role pro uživatele. Nepřiřazujte roli správce, protože, který by mu dát normální prostředí přístup, jakož i přístup pomocí jednotného přihlašování.
    
    f. Klikněte na **OK**.
    
    > [!NOTE]
    > Uživatel nebude dostávat oznámení uvítací e-mail obsahující heslo, které lze použít pro přihlášení k účtu AtomSphere, protože své heslo je spravována prostřednictvím zprostředkovatele identity. Můžete použít jakékoli jiné Boomi uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Boomi uživatelským účtům, zřídit AAD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Boomi použití Azure jednotného přihlašování.

![Přiřazení role uživatele][200] 

**Přiřadit Boomi Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Boomi**.

    ![Odkaz Boomi v seznamu aplikací](./media/boomi-tutorial/tutorial_boomi_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Boomi na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci Boomi.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/boomi-tutorial/tutorial_general_01.png
[2]: ./media/boomi-tutorial/tutorial_general_02.png
[3]: ./media/boomi-tutorial/tutorial_general_03.png
[4]: ./media/boomi-tutorial/tutorial_general_04.png

[100]: ./media/boomi-tutorial/tutorial_general_100.png

[200]: ./media/boomi-tutorial/tutorial_general_200.png
[201]: ./media/boomi-tutorial/tutorial_general_201.png
[202]: ./media/boomi-tutorial/tutorial_general_202.png
[203]: ./media/boomi-tutorial/tutorial_general_203.png

