---
title: 'Kurz: Azure Active Directory integrace s společnost Sage Intacct | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a společnost Sage Intacct.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: jeedes
ms.openlocfilehash: e01bc36f0f30cec779652a603aaf7bdb496e5190
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850570"
---
# <a name="tutorial-integrate-sage-intacct-with-azure-active-directory"></a>Kurz: integrace společnost Sage Intacct s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat společnost Sage Intacct s Azure Active Directory (Azure AD). Když integrujete společnost Sage Intacct s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k společnost Sage Intacct.
* Umožněte uživatelům, aby se automaticky přihlásili k společnost Sage Intacct s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné společnost Sage Intacct s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Společnost Sage Intacct podporuje **IDP** iniciované jednotného přihlašování.
* Po nakonfigurování společnost Sage Intacct můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-sage-intacct-from-the-gallery"></a>Přidání společnost Sage Intacct z Galerie

Pokud chcete nakonfigurovat integraci společnost Sage Intacct do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat společnost Sage Intacct z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **společnost Sage Intacct** .
1. Z panelu výsledků vyberte **společnost Sage Intacct** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-sage-intacct"></a>Konfigurace a testování jednotného přihlašování Azure AD pro společnost Sage Intacct

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí společnost Sage Intacct s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v společnost Sage Intacct.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s společnost Sage Intacct, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
2. **[Nakonfigurujte jednotné přihlašování společnost Sage Intacct](#configure-sage-intacct-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořit společnost Sage Intacct Test User](#create-sage-intacct-test-user)** -to znamená, že má protějšek B. Simon v společnost Sage Intacct, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Intacct společnost Sage** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL:  `https://www.intacct.com/ia/acct/sso_response.phtml`

1. Aplikace společnost Sage Intacct očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na možnost **Upravit** ikonu otevřete dialogové okno atributy uživatele..

    ![image](common/edit-attribute.png)

1. Kromě toho aplikace společnost Sage Intacct očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V dialogovém okně **atributy uživatele & deklarací** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Název atributu  |  Zdrojový atribut|
    | ---------------| --------------- |
    | Název společnosti | **ID společnosti společnost Sage Intacct** |
    | name | Hodnota by měla být stejná jako **ID uživatele**společnost Sage Intacct, které zadáte v **části Vytvoření společnost Sage Intacct test uživatele**, která je vysvětlena dále v tomto kurzu. |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte nebo vyberte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Intacct společnost Sage** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k společnost Sage Intacct.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **společnost Sage Intacct**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sage-intacct-sso"></a>Konfigurace jednotného přihlašování společnost Sage Intacct

1. V jiném okně webového prohlížeče se přihlaste k webu společnost Sage Intacct společnosti jako správce.

1. Klikněte na kartu **Společnost** a pak klikněte na **informace o společnosti**.

    ![Společnost](./media/intacct-tutorial/ic790037.png "Společnost")

1. Klikněte na kartu **zabezpečení** a pak klikněte na **Upravit**.

    ![Zabezpečení](./media/intacct-tutorial/ic790038.png "Zabezpečení")

1. V části **jednotné přihlašování (SSO)** proveďte následující kroky:

    ![Jednotné přihlašování](./media/intacct-tutorial/ic790039.png "jednotné přihlašování")

    a. Vyberte **Povolit jednotné přihlašování**.

    b. Jako **typ zprostředkovatele identity**vyberte **SAML 2,0**.

    c. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    e. Otevřete v programu Poznámkový blok certifikát s kódováním **Base-64** , zkopírujte jeho obsah do schránky a vložte ho do pole **certifikát** .

    f. Klikněte na **Uložit**.

### <a name="create-sage-intacct-test-user"></a>Vytvořit testovacího uživatele společnost Sage Intacct

Pokud chcete nastavit uživatele Azure AD tak, aby se mohli přihlásit k společnost Sage Intacct, musí být zřízené v společnost Sage Intacct. Pro společnost Sage Intacct je zřizování ručním úkolem.

**Při zřizování uživatelských účtů proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **společnost Sage Intacct** .

1. Klikněte na kartu **Společnost** a potom klikněte na **Uživatelé**.

    ![Uživatelé](./media/intacct-tutorial/ic790041.png "Uživatelé")

1. Klikněte na kartu **Přidat** .

    ![Přidat](./media/intacct-tutorial/ic790042.png "Přidat")

1. V části **informace o uživateli** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části informace o uživateli, kde můžete zadat informace v tomto kroku.](./media/intacct-tutorial/ic790043.png "Informace o uživateli")

    a. Do části **informace o uživateli** zadejte **ID uživatele**, **příjmení** **, jméno**, **e-mailovou adresu**, **název**a **telefon** účtu Azure AD, který chcete zřídit.

    > [!NOTE]
    > Zajistěte, aby bylo stejné **ID uživatele** ve výše uvedeném snímku obrazovky a hodnota **atributu zdroje** , která je namapována s atributem **Name** v sekci **atributů uživatele** v Azure Portal.

    b. Vyberte **oprávnění správce** pro účet služby Azure AD, který chcete zřídit.

    c. Klikněte na **Uložit**. 
    
    d. Držitel účtu Azure AD obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje.

1. Klikněte na kartu **jednotné přihlašování** a ujistěte se, že **ID uživatele federovaného jednotného přihlašování** na obrázku pod ním a hodnotu **atributu zdroje** , která je namapovaná s hodnotou `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` v části **atributů uživatele** v Azure Portal, by měla být stejná.

    ![Snímek obrazovky se zobrazí v sekci informace o uživateli, kde můžete zadat federované S S S, který jsem d.](./media/intacct-tutorial/ic790044.png "Informace o uživateli")

> [!NOTE]
> K zajištění uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů společnost Sage Intacct nebo rozhraní API, která jsou k dispozici v společnost Sage Intacct.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici společnost Sage Intacct, měli byste být automaticky přihlášeni k společnost Sage Intacct, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
