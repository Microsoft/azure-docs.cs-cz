---
title: 'Kurz: Integrace Azure Active Directory s přenosem MOVEit – integrace služby Azure AD | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přenos MOVEit – integrace služby Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b50bf623046094509170b5b5efc091013499b51b
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169832"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Kurz: Integrace Azure Active Directory s přenosem MOVEit – integrace služby Azure AD

V tomto kurzu se dozvíte, jak integrovat MOVEit přenosy – integrace služby Azure AD se službou Azure Active Directory (Azure AD).

Integrace MOVEit přenosy – integrace služby Azure AD službou Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k MOVEit přenosy – integrace služby Azure AD.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k MOVEit přenosy – integrace Azure AD (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s přenosem MOVEit – integrace služby Azure AD, potřebujete následující položky:

- Předplatné Azure AD
- Přenosy MOVEit – Azure AD integraci jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání MOVEit přenosy – integrace služby Azure AD z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Přidání MOVEit přenosy – integrace služby Azure AD z Galerie
Konfigurace integrace MOVEit přenosy – integrace služby Azure AD do služby Azure AD, budete muset přidat MOVEit přenosy – integrace služby Azure AD z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat MOVEit přenosy – integrace služby Azure AD z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **MOVEit přenosy – integrace služby Azure AD**vyberte **MOVEit přenosy – integrace služby Azure AD** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Přenosy MOVEit – integrace služby Azure AD v seznamu výsledků](./media/moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s přenosem MOVEit – integrace služby Azure AD na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v MOVEit přenosy – integrace služby Azure AD je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v MOVEit přenosy – integrace služby Azure AD je potřeba navázat.

Při přenosu MOVEit – integrace služby Azure AD, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s přenosem MOVEit – integrace služby Azure AD, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření MOVEit přenosu – Azure AD integrace testovacího uživatele](#create-a-moveit-transfer---azure-ad-integration-test-user)**  – Pokud chcete mít protějšek Britta Simon MOVEit přenosy – integrace služby Azure AD, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v MOVEit přenosy – aplikace pro integraci služby Azure AD.

**K Azure AD nakonfigurovat jednotné přihlašování s přenosem MOVEit – integrace služby Azure AD, postupujte následovně:**

1. Na webu Azure Portal na **MOVEit přenosy – integrace služby Azure AD** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

1. Na **MOVEit přenosy – integrace služby Azure AD domény a adresy URL** části, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://contoso.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://contoso.com/<tenatid>`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`    
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Můžete použít tyto hodnoty později v **adresa URL služby zprostředkovatele metadat** části nebo se obraťte na [MOVEit přenosy – tým podpory integrace klienta služby Azure AD](https://community.ipswitch.com/s/support) k získání těchto hodnot.

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/moveittransfer-tutorial/tutorial_general_400.png)
    
1. Přihlaste se k tenantovi MOVEit přenos jako správce.

1. V levém navigačním podokně klikněte na tlačítko **nastavení**.

    ![Části v aplikaci nastavení na straně](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

1. Klikněte na tlačítko **jednotné přihlášení** odkaz, který je v části **zásady zabezpečení -> uživatele Auth**.

    ![Zabezpečení zásady na aplikaci na straně](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

1. Klikněte na adresu URL metadat. odkaz ke stažení dokumentů metadat.

    ![Adresa URL služby zprostředkovatele metadat](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * Ověřte **entityID** odpovídá **identifikátor** v **MOVEit přenosy – integrace služby Azure AD domény a adresy URL** oddílu.
    * Ověřte **AssertionConsumerService** adresa URL umístění odpovídá **adresy URL odpovědi** v **MOVEit přenosy – integrace služby Azure AD domény a adresy URL** části.
    
    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

1. Klikněte na tlačítko **přidat zprostředkovatele Identity** tlačítko pro přidání nového poskytovatele federované Identity.

    ![Přidat zprostředkovatele Identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

1. Klikněte na tlačítko **Procházet...**  Pokud chcete vybrat soubor metadat, který jste si stáhli z webu Azure portal, pak klikněte na tlačítko **přidat zprostředkovatele Identity** nahrát staženého souboru.

    ![Zprostředkovatele Identity SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

1. Vyberte "**Ano**" jako **povolené** v **Upravit federované nastavení zprostředkovatele Identity...**  stránky a klikněte na tlačítko **Uložit**.

    ![Nastavení poskytovatele federované Identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

1. V **upravit Federated Identity uživatele nastavení poskytovatele** stránce, udělejte toto:
    
    ![Upravit nastavení poskytovatele federované Identity](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Vyberte **SAML NameID** jako **přihlašovací jméno**.
    
    b. Vyberte **jiných** jako **jméno a příjmení** a **název atributu** vložte hodnotu do textového pole: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Vyberte **jiných** jako **e-mailu** a **název atributu** vložte hodnotu do textového pole: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Vyberte **Ano** jako **automatické vytvoření účtu na Sign-on**.
    
    e. Klikněte na tlačítko **Uložit** tlačítko.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/moveittransfer-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/moveittransfer-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/moveittransfer-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/moveittransfer-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Vytvoření MOVEit přenosu – Azure AD integrace testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v MOVEit přenosy – integrace služby Azure AD. Přenosy MOVEit – integrace služby Azure AD podporuje just-in-time zřizování, které jste povolili. Neexistuje žádná položka akce pro vás v této části. Nový uživatel se vytvoří během pokusu o přístup k MOVEit přenosy – integrace služby Azure AD, pokud ještě neexistuje.

>[!NOTE]
>Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [MOVEit přenosy – tým podpory integrace klienta služby Azure AD](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k MOVEit přenosy – integrace služby Azure AD.

![Přiřazení role uživatele][200] 

**Britta Simon přiřadit přenosy MOVEit – integrace služby Azure AD, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **MOVEit přenosy – integrace služby Azure AD**.

    ![Přenosy MOVEit – integrace služby Azure AD odkaz v seznamu aplikací](./media/moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

Cílem této části je testování konfigurace jednotného přihlašování k Azure AD pomocí přístupového panelu.

Po kliknutí na přenosy MOVEit – integrace služby Azure AD dlaždici na přístupovém panelu, které by měl získat automaticky přihlášení k MOVEit přenosy – aplikace pro integraci služby Azure AD. 

## <a name="additional-resources"></a>Další materiály

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/moveittransfer-tutorial/tutorial_general_203.png

