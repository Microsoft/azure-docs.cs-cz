---
title: 'Kurz: Integrace Azure Active Directory se službou Cisco Webex | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 4dcf487afdad899853c97d3d2a1493a6123b3bab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440707"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Kurz: Integrace Azure Active Directory se službou Cisco Webex

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Cisco Webex.

Integrace Cisco Webex s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k Cisco Webex.
- Můžete povolit uživatelům, aby automaticky získat přihlášení k Cisco Webex pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cisco Webex, potřebujete následující položky:

- S předplatným služby Azure AD
- Předplatné Cisco Webex jednotné přihlašování na povoleno

> [!NOTE]
> Nedoporučujeme používat produkčním prostředí pro testování kroky v tomto kurzu.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle následujících doporučení:

- Nepoužívejte vaše produkční prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat bezplatnou zkušební verzi měsíčního](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání Cisco Webex z Galerie
1. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="add-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie
Konfigurace integrace Cisco Webex do služby Azure AD, budete muset přidat Cisco Webex z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cisco Webex z galerie, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

1. Přejděte na **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
1. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko nahoře v dialogovém okně.

    ![Tlačítko nové aplikace][3]

1. Do vyhledávacího pole zadejte **Cisco Webex**. 

1. Vyberte **Cisco Webex** na panelu výsledků. Vyberte **přidat** tlačítko pro přidání aplikace.

    ![Cisco Webex v seznamu výsledků](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex podle testovacího uživatele nazývá "Britta Simon."

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, kdo je uživatel protějšky v Cisco Webex pro uživatele ve službě Azure AD. Jinými slovy budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatelské v Cisco Webex.

V Cisco Webex, zadejte hodnotu **uživatelské jméno** stejnou hodnotu jako **uživatelské jméno** ve službě Azure AD. Nyní jste vytvořili propojení mezi dva uživatele. 

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex, proveďte následující stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
1. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
1. [Vytvoření zkušebního uživatele Cisco Webex](#create-a-cisco-webex-test-user) mít protějšek Britta Simon Cisco Webex, který je propojený s Azure AD reprezentace uživatele.
1. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
1. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci Cisco Webex.

**Ke konfiguraci Azure AD jednotné přihlašování s Cisco Webex, proveďte následující kroky:**

1. Na webu Azure Portal na **Cisco Webex** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

1. Pro povolení jednotného přihlašování, v **jednotného přihlašování** v dialogu **režimu** rozevíracího seznamu vyberte **přihlašování na základě SAML**.
 
    ![Jednotné přihlašování – dialogové okno](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

1. V **Cisco Webex domény a adresy URL** části, proveďte následující kroky:

    ![Cisco Webex domény a adresy URL jednotného přihlašování – informace](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. V **přihlašovací adresa URL** pole, zadejte adresu URL s následujícím vzorem: `https://<subdomain>.webex.com`

    b. V **identifikátor** pole, zadejte adresu URL `http://www.webex.com`.

    c. V **adresy URL odpovědi** pole, zadejte adresu URL s následujícím vzorem: `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty adresy URL skutečné odpovědi a adresa URL přihlašování. Kontakt [tým podpory klienta Cisco Webex](https://www.webex.co.in/support/support-overview.html) k získání těchto hodnot. 

1. Na **podpisový certifikát SAML** vyberte **soubor XML s metadaty**a poté uložte soubor metadat ve vašem počítači.

    ![Odkaz ke stažení certifikátu](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

1. Vyberte **Uložit**.

    ![Konfigurace jednotné přihlašování tlačítko Uložit](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
1. V **konfigurace Cisco Webex** vyberte **konfigurace Cisco Webex** otevřít **nakonfigurovat přihlašování** okna. Kopírovat **odhlašování URL**, **SAML Entity ID**, a **SAML jednotné přihlašování – adresa URL služby** z **Stručná referenční příručka** oddílu.

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Cisco Webex jako správce.

1. V nabídce v horní části vyberte **Správa webu**.

    ![Správa lokalit](./media/cisco-webex-tutorial/ic777621.png "Správa lokalit")

1. V **Správa webu** vyberte **Konfigurace jednotného přihlašování**.
   
    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/ic777622.png "Konfigurace jednotného přihlašování")

1. V **federované Konfigurace jednotného přihlašování k webu** části, proveďte následující kroky:
   
    ![Konfigurace jednotného přihlašování k federovanému](./media/cisco-webex-tutorial/ic777623.png "federované jednotné přihlašování konfigurace")  

    a. V **protokol Federation** seznamu vyberte **SAML 2.0**.

    b. Pro **profil jednotného přihlašování**vyberte **SP iniciované**.

    c. Otevřete stažený certifikát v programu Poznámkový blok a zkopírujte obsah.

    d. Vyberte **importovat Metadata SAML**a vložte zkopírovaný obsah certifikátu.

    e. V **vystavitele pro SAML (IdP ID)** vložte hodnotu **SAML Entity ID** , který jste zkopírovali z portálu Azure portal.

    f. V **zákazníka jednotného přihlašování služby přihlašovací adresa URL** vložte **SAML jednotné přihlašování – adresa URL služby**, který jste zkopírovali z portálu Azure portal.

    g. Z **formátem** seznamu vyberte **e-mailová adresa**.

    h. V **AuthnContextClassRef** zadejte **urn: oasis: názvy: tc: SAML:2.0:ac:classes:Password**.

    i. V **zákazníka jednotného přihlašování služby odhlašovací adresa URL** vložte **odhlašování URL**, který jste zkopírovali z portálu Azure portal.
   
    j. Vyberte **aktualizace**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com) při nastavení aplikace. Po přidání této aplikace z **služby Active Directory** > **podnikové aplikace** vyberte **Single Sign-On** kartu a potom přejdete na vložené dokumentace ke službě prostřednictvím **konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace na [dokumentace ke službě Azure AD embedded](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně, vyberte **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/cisco-webex-tutorial/create_aaduser_01.png)

1. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/cisco-webex-tutorial/create_aaduser_02.png)

1. Chcete-li otevřít **uživatele** dialogovém okně vyberte **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/cisco-webex-tutorial/create_aaduser_03.png)

1. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.
 
### <a name="create-a-cisco-webex-test-user"></a>Vytvoření zkušebního uživatele Cisco Webex

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Cisco Webex, musí být zřízený v Cisco Webex. V případě Cisco Webex zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, proveďte následující kroky:**

1. Přihlaste se k vaší **Cisco Webex** tenanta.

1. Přejděte na **spravovat uživatele** > **přidat uživatele**.
   
    ![Přidání uživatelů](./media/cisco-webex-tutorial/ic777625.png "přidat uživatele")

1. V **přidat uživatele** části, proveďte následující kroky:
   
    ![Přidat uživatele](./media/cisco-webex-tutorial/ic777626.png "přidat uživatele")   

    a. Pro **typ účtu**vyberte **hostitele**.

    b. V **křestní jméno** zadejte jméno uživatele (v tomto případě **Britta**).

    c. V **příjmení** zadejte příjmení uživatele (v tomto případě **Simon**).

    d. V **uživatelské jméno** zadejte e-mailu uživatele (v tomto případě **Brittasimon@contoso.com**).

    e. V **e-mailu** zadejte e-mailovou adresu uživatele (v tomto případě **Brittasimon@contoso.com**).

    f. V **heslo** zadejte heslo uživatele.

    g. V **potvrdit** heslo pole, znovu zadejte heslo uživatele.

    h. Vyberte **Přidat**.

>[!NOTE]
>Můžete použít jiné nástroje pro tvorbu Cisco Webex uživatelského účtu nebo rozhraní API, která jsou k dispozici v Cisco Webex ke zřízení uživatelských účtů služby Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolit uživatele Britta Simon používat jednotné přihlašování Azure udělením přístupu k Cisco Webex.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon Cisco Webex, proveďte následující kroky:**

1. Na webu Azure Portal otevřete zobrazení aplikace. Další, přejděte na zobrazení adresáře a potom na **podnikové aplikace**.  

1. Vyberte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

1. V seznamu aplikací vyberte **Cisco Webex**.

    ![Odkaz Cisco Webex v seznamu aplikací](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

1. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

1. Vyberte **přidat** tlačítko. Potom vyberte **uživatelů a skupin** v **přidat přiřazení** dialogové okno.

    ![Podokno Přidat přiřazení][203]

1. V **uživatelů a skupin** dialogu **Britta Simon** v **uživatelé** seznamu.

1. V **uživatelů a skupin** dialogové okno, klikněte na tlačítko **vyberte** tlačítko.

1. Vyberte **přiřadit** tlačítko **přidat přiřazení** dialogové okno.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když vyberete dlaždici Cisco Webex na přístupovém panelu, můžete automaticky získat přihlášení k aplikaci Cisco Webex.

Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

