---
title: 'Kurz: Integrace Azure Active Directory se službou iQualify LMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: d1161480bfd7a4cfeeb81f02234586a515fdffed
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446132"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Kurz: Integrace Azure Active Directory se službou iQualify LMS

V tomto kurzu se dozvíte, jak integrovat iQualify LMS s Azure Active Directory (Azure AD).

IQualify LMS integraci se službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k iQualify LMS.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k iQualify LMS (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iQualify LMS, potřebujete následující položky:

- S předplatným služby Azure AD
- IQualify LMS jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání iQualify LMS z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-iqualify-lms-from-the-gallery"></a>Přidání iQualify LMS z Galerie
Konfigurace integrace iQualify LMS do služby Azure AD, budete muset přidat iQualify LMS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iQualify LMS z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **iQualify LMS**vyberte **iQualify LMS** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![iQualify LMS v seznamu výsledků](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s iQualify LMS založená na uživateli test "Britta Simon."

Azure AD pro jednotné přihlašování pro práci, potřebuje vědět, co protějšek uživatel v iQualify LMS je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iQualify LMS musí být vytvořeno.

V iQualify LMS přiřadit hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iQualify LMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvořit testovacího uživatele LMS iQualify](#create-an-iqualify-lms-test-user)**  – Pokud chcete mít protějšek Britta Simon v iQualify LMS, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci LMS iQualify.

**Ke konfiguraci Azure AD jednotné přihlašování s iQualify LMS, proveďte následující kroky:**

1. Na webu Azure Portal na **iQualify LMS** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

1. Na **iQualify LMS domény a adresy URL** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciované zprostředkovatele identity:

    ![informace o iQualify LMS domény a adresy URL jednotného přihlašování](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|--|
    | Produkční prostředí: `https://<yourorg>.iqualify.com/`|
    | Testovací prostředí: `https://<yourorg>.iqualify.io`|
    
    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|--|
    | Produkční prostředí: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testovací prostředí: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

1. Zkontrolujte **zobrazit pokročilé nastavení URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![informace o iQualify LMS domény a adresy URL jednotného přihlašování](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|--|
    | Produkční prostředí: `https://<yourorg>.iqualify.com/login` |
    | Testovací prostředí: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Kontakt [tým podpory LMS klienta iQualify](https://www.iqualify.com) k získání těchto hodnot. 

1. Aplikace LMS iQualify očekává, že kontrolní výrazy zabezpečení kontrolního výrazu SAML (Markup Language) který se má zobrazit v určitém formátu. Konfigurace deklarace identity a správy hodnoty atributů **atributy uživatele** části iQualify integrace stránky aplikace, jak je znázorněno na následujícím snímku obrazovky:
    
    ![Konfigurace jednotného přihlašování](./media/iqualify-tutorial/atb.png)

1. V **atributy uživatele** části na **jednotného přihlašování** dialogového okna pro každý řádek je znázorněno v následující tabulce proveďte následující kroky:
    
    | Název atributu | Hodnota atributu |
    | --- | --- |    
    | e-mail | user.userprincipalname |
    | křestní_jméno | user.givenname |
    | Příjmení | user.surname |
    | person_id | "atribut" | 

    a. Klikněte na tlačítko **přidat atribut** otevřít **přidat atribut** dialogového okna.

    ![Konfigurace jednotného přihlašování](./media/iqualify-tutorial/atb2.png)

    ![Konfigurace jednotného přihlašování](./media/iqualify-tutorial/atb3.png)
    
    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.
    
    c. Z **hodnotu** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.
    
    d. Klikněte na tlačítko **Ok**

    e. Opakujte kroky "a" až "d" pro další řádky tabulky. 

    > [!Note]
    > Opakováním kroků "a" až "d" pro **person_id** atribut je **volitelné**

1. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát (Base 64)** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/iqualify-tutorial/tutorial_general_400.png)
    
1. Na **iQualify LMS konfigurace** klikněte na tlačítko **konfigurace iQualify LMS** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování adresy URL a SAML jednotné přihlašování služby** z **Stručná referenční příručka oddílu.**

    ![iQualify LMS konfigurace](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

1.  Otevřete nové okno prohlížeče a přihlaste se k prostředí iQualify jako správce.

1. Jakmile jste přihlášení, klikněte na něj v pravém horním rohu a potom klikněte na **"Nastavení účtu".**

    ![Nastavení účtu](./media/iqualify-tutorial/setting1.png) 
1. V oblasti nastavení účtu klikněte na tlačítko na pásu karet na levé straně a klikněte na **"Integrace."**
    
    ![INTEGRACE](./media/iqualify-tutorial/setting2.png)

1. V části integrace, klikněte na **SAML** ikonu.

    ![Ikona SAML](./media/iqualify-tutorial/setting3.png)

1. V **nastavení ověřování SAML** dialogové okno pole, proveďte následující kroky:

    ![Nastavení ověřování SAML](./media/iqualify-tutorial/setting4.png)

    a. V **adresy služby jednotného přihlašování SAML** pole, vložte **SAML jedné adresy URL služby Sign‑On** hodnotu zkopírovat v okně Konfigurace aplikací služby Azure AD.
    
    b. V **SAML ODHLAŠOVACÍ adresa URL** pole, vložte **Sign‑Out URL** hodnotu zkopírovat v okně Konfigurace aplikací služby Azure AD.
    
    c. V poznámkovém bloku otevřete soubor stažený certifikát, zkopírujte jeho obsah a vložte jej do **veřejný certifikát** pole.
    
    d. V **popisek TLAČÍTKA pro přihlášení** zadejte název tlačítka zobrazený na přihlašovací stránku.
    
    e. Klikněte na **ULOŽIT**.

    f. Klikněte na tlačítko **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/iqualify-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/iqualify-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/iqualify-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/iqualify-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Vytvoření iQualify LMS testovacího uživatele

V této části se vytvoří uživateli Britta Simon v iQualify. iQualify LMS podporuje just‑in‑time zřizování uživatelů, která je ve výchozím nastavení povolené.

Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi iQualify, vytvoří se při pokusu o přístup k iQualify LMS nový.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iQualify LMS.

![Přiřazení role uživatele][200] 

**Přiřadit iQualify LMS Britta Simon, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **iQualify LMS**.

    ![Odkaz LMS iQualify v seznamu aplikací](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na iQualify, které LMS dlaždici na přístupovém panelu, měli byste obdržet přihlašovací stránku vaší aplikace LMS iQualify. 

   ![přihlašovací stránka](./media/iqualify-tutorial/login.png) 

Klikněte na tlačítko **přihlášení Azure AD** tlačítko by měl získat automaticky přihlášení k aplikaci LMS iQualify.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png

