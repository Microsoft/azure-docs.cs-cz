---
title: 'Kurz: Integrace Azure Active Directory s TOPdesk - Public | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TOPdesk - Public.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: f3190cb7f837464dd211acb127c4ab6f86e7a9f4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153296"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Kurz: Integrace Azure Active Directory s TOPdesk – veřejné

V tomto kurzu se dozvíte, jak integrovat TOPdesk - veřejnosti s Azure Active Directory (Azure AD).

Integrace TOPdesk - veřejnosti s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k TOPdesk - Public.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k TOPdesk – Public (Single Sign-On) s jejich účty Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TOPdesk – veřejné, potřebujete následující položky:

- Předplatné Azure AD
- TOPdesk – veřejné jednotného přihlašování povolená předplatného

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání TOPdesk - Public z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-topdesk---public-from-the-gallery"></a>Přidání TOPdesk - Public z Galerie
Konfigurace integrace TOPdesk - Public do služby Azure AD, potřebujete přidat TOPdesk - Public z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TOPdesk - Public z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **TOPdesk - Public**vyberte **TOPdesk - Public** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![TOPdesk - Public v seznamu výsledků](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části můžete nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk – Public podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co protějšek uživatel v TOPdesk – Public je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TOPdesk – Public potřeba navázat.

V TOPdesk – veřejné, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TOPdesk - Public, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
1. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
1. **[Vytvoření TOPdesk – veřejné testovacího uživatele](#create-a-topdesk---public-test-user)**  – Pokud chcete mít protějšek Britta Simon TOPdesk - Public, který je propojený s Azure AD reprezentace uživatele.
1. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
1. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování ve vašich TOPdesk – veřejné aplikace.

**Ke konfiguraci Azure AD jednotné přihlašování s TOPdesk – veřejné, proveďte následující kroky:**

1. Na webu Azure Portal na **TOPdesk - Public** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.
 
    ![Jednotné přihlašování – dialogové okno](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. Na **TOPdesk – veřejné domény a adresy URL** části, proveďte následující kroky:

    ![TOPdesk – veřejné domény a adresy URL jednotného přihlašování – informace](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.topdesk.net`
    
    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné identifikátor, adresa URL odpovědi a přihlašovací adresa URL. Adresa URL odpovědi je explaned později v kurzu. Kontakt [TOPdesk - tým podpory veřejným klientem](https://help.topdesk.com/saas/enterprise/user/) k získání těchto hodnot.  

1. Na **podpisový certifikát SAML** klikněte na tlačítko **soubor XML s metadaty** a uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. Na **TOPdesk – veřejné konfigurace** klikněte na tlačítko **konfigurace TOPdesk - Public** otevřete **nakonfigurovat přihlašování** okna. Kopírovat **URL odhlašování SAML Entity ID a SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka oddílu.**

    ![TOPdesk – veřejné konfigurace](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. Přihlaste se k vaší **TOPdesk - Public** společnosti serveru jako správce.

1. V **TOPdesk** nabídky, klikněte na tlačítko **nastavení**.
   
    ![Nastavení](./media/topdesk-public-tutorial/ic790598.png "nastavení")

1. Klikněte na tlačítko **nastavení přihlášení**.
   
    ![Nastavení přihlášení](./media/topdesk-public-tutorial/ic790599.png "nastavení přihlášení")

1. Rozbalte **nastavení přihlášení** nabídky a pak klikněte na tlačítko **Obecné**.
   
    ![Obecné](./media/topdesk-public-tutorial/ic790600.png "obecné")

1. V **veřejné** část **SAML přihlášení** konfigurace části, proveďte následující kroky:
   
    ![Technické nastavení](./media/topdesk-public-tutorial/ic790601.png "technické nastavení")
   
    a. Klikněte na tlačítko **Stáhnout** ke stažení souboru metadat veřejné a pak ho uložte místně ve vašem počítači.
   
    b. Otevřete soubor stažený metadat a najděte **AssertionConsumerService** uzlu.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopírovat **AssertionConsumerService** hodnota a vložte tuto hodnotu v **adresy URL odpovědi** textového pole v **TOPdesk – veřejné domény a adresy URL** oddílu.      
   
1. Chcete-li vytvořit soubor certifikátu, proveďte následující kroky:
    
    ![Certifikát](./media/topdesk-public-tutorial/ic790606.png "certifikátu")
    
    a. Otevřete soubor stažený metadat z webu Azure portal.
    
    b. Rozbalte **RoleDescriptor** uzel, který má **xsi: type** z **dodáni: ApplicationServiceType**.
    
    c. Zkopírujte hodnotu **certifikátu x 509** uzlu.
    
    d. Uložit zkopírovaný **certifikátu x 509** hodnotu místně na vašem počítači v souboru.

1. V **veřejné** klikněte na tlačítko **přidat**.
    
    ![Přihlášení SAML](./media/topdesk-public-tutorial/ic790625.png "SAML přihlášení")

1. Na **pomocníka s nastavením konfigurace SAML** dialogového okna stránky, proveďte následující kroky:
    
    ![Pomocníka s nastavením konfigurace SAML](./media/topdesk-public-tutorial/ic790608.png "Pomocníka s nastavením konfigurace SAML")
    
    a. Chcete-li nahrát soubor metadat stažené z webu Azure portal v části **federačních metadat**, klikněte na tlačítko **Procházet**.

    b. K nahrání souboru certifikátu v části **certifikátu (RSA)**, klikněte na tlačítko **Procházet**.

    c. Nahrát soubor loga jste získali v části z na tým podpory TOPdesk **ikona loga**, klikněte na tlačítko **Procházet**.

    d. V **atribut uživatelského jména** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. V **zobrazovaný název** textového pole zadejte název pro vaši konfiguraci.

    f. Klikněte na **Uložit**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [Dokumentace ke službě Azure AD, embedded]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-topdesk---public-test-user"></a>Vytvoření TOPdesk – veřejné testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do TOPdesk - veřejný, musí být poskytnuty do TOPdesk - Public.  
V případě TOPdesk – veřejné, zřizování se ruční úlohy.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurace zřizování uživatelů, proveďte následující kroky:
1. Přihlaste se k vaší **TOPdesk - Public** společnosti serveru jako správce.

1. V nabídce v horní části klikněte na tlačítko **TOPdesk \> nový \> podpůrné soubory \> osoba**.
   
    ![Osoba](./media/topdesk-public-tutorial/ic790628.png "osoby")

1. V dialogovém okně Nový uživatel proveďte následující kroky:
   
    ![Nové osobě](./media/topdesk-public-tutorial/ic790629.png "nové osoby")
   
    a. Klikněte na kartu Obecné.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je Simon
 
    c. Vyberte **lokality** pro účet.
 
    d. Klikněte na **Uložit**.

> [!NOTE]
> Můžete použít jakékoli jiné TOPdesk – zadáním hodnoty nástrojů pro vytváření veřejné uživatelského účtu nebo rozhraní API poskytovaných TOPdesk - Public zřízení uživatelských účtů služby Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k TOPdesk - Public.

![Přiřazení role uživatele][200] 

**Přiřadit Britta Simon TOPdesk – veřejné, proveďte následující kroky:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **TOPdesk - Public**.

    ![TOPdesk - veřejný odkaz v seznamu aplikací](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

1. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

1. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

1. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete TOPdesk – Public dlaždici na přístupovém panelu, vám by měl získat automaticky přihlášení k vaší TOPdesk – veřejné aplikace.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

