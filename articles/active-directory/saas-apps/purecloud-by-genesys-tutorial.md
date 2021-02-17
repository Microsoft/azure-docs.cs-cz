---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s PureCloud podle Genesys | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a PureCloud pomocí Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 8e31c050218e2069354d4d18f812a50f445e596f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560599"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s PureCloud pomocí Genesys

V tomto kurzu se dozvíte, jak integrovat PureCloud pomocí Genesys s Azure Active Directory (Azure AD). Při integraci PureCloud pomocí Genesys se službou Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k PureCloud prostřednictvím Genesys.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k PureCloud prostřednictvím Genesys s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud ho nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* PureCloud pomocí předplatného jednotného přihlašování Genesys (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* PureCloud by Genesys podporovaly **SP a IDP**– bylo zahájeno jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-purecloud-by-genesys-from-the-gallery"></a>Přidání PureCloud podle Genesys z Galerie

Pokud chcete nakonfigurovat integraci PureCloud by Genesys do služby Azure AD, musíte přidat PureCloud od galerie Genesys z Galerie do svého seznamu spravovaných aplikací SaaS. Postupujte takto:

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo pomocí osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejít na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **PureCloud by Genesys** .
1. Z panelu výsledků vyberte **PureCloud by Genesys** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-purecloud-by-genesys"></a>Konfigurace a testování jednotného přihlašování Azure AD pro PureCloud pomocí Genesys

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí PureCloud pomocí Genesys pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v PureCloud pomocí Genesys.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí PureCloud pomocí Genesys, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. **[Nakonfigurujte PureCloud pomocí jednotného přihlašování Genesys](#configure-purecloud-by-genesys-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte PureCloud pomocí Genesys testovacího uživatele](#create-purecloud-by-genesys-test-user)** , aby měl protějšek B. Simon v PureCloud by Genesys, který je propojený s reprezentací uživatele Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pokud chcete povolit jednotné přihlašování služby Azure AD v Azure Portal, postupujte následovně:

1. V Azure Portal na stránce **PureCloud podle Genesys** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **vybrat jednu Sign-On metodu** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP**, zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do pole **identifikátor** zadejte adresy URL, které odpovídají vaší oblasti:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Do pole **Adresa URL odpovědi** zadejte adresy URL, které odpovídají vaší oblasti:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Vyberte **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do pole **přihlašovací adresa URL** zadejte adresy URL, které odpovídají vaší oblasti:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. PureCloud by aplikace Genesys očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů:

    ![image](common/default-attributes.png)

1. Kromě toho PureCloud by aplikace Genesys očekává, že se v odpovědi SAML vrátí několik dalších atributů, jak je znázorněno v následující tabulce. Tyto atributy jsou také předem vyplněné, ale můžete je podle potřeby zkontrolovat.

    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | E-mail | User. userPrincipalName |
    | OrganizationName | `Your organization name` |

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení PureCloud podle Genesys** zkopírujte příslušnou adresu URL (nebo adresy URL) na základě vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon v Azure Portal:

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte uživatelské jméno v následujícím formátu: username@companydomain.extension . Příklad: `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom si poznamenejte hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k PureCloud pomocí Genesys.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **PureCloud podle Genesys**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-purecloud-by-genesys-sso"></a>Konfigurace PureCloud pomocí jednotného přihlašování Genesys

1. V jiném okně webového prohlížeče se přihlaste k PureCloud pomocí Genesys jako správce.

1. V horní části vyberte **správce** a potom v části **integrace** klikněte na **jednotné přihlašování** .

    ![Snímek obrazovky se zobrazí okno Správce PureCloud, kde můžete vybrat jednotné přihlašování.](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Přepněte na kartu **ADFS/Azure AD (Premium)** a pak postupujte podle těchto kroků:

    ![Snímek obrazovky se zobrazí stránka integrace, kde můžete zadat hodnoty, které jsou popsány.](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Vyberte **Procházet** a nahrajte certifikát s kódováním base-64, který jste stáhli z Azure Portal do **certifikátu ADFS**.

    b. Do pole **identifikátor URI vystavitele ADFS** vložte hodnotu **identifikátoru Azure AD** , který jste zkopírovali z Azure Portal.

    c. Do pole **cílový identifikátor URI** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    d. Pro hodnotu **identifikátoru předávající strany** klikněte na Azure Portal a pak na stránce Application Integration **PureCloud podle Genesys** vyberte kartu **vlastnosti** a zkopírujte hodnotu **ID aplikace** . Vložte ho do pole **identifikátor předávající strany** .

    ![Snímek obrazovky se zobrazí v podokně vlastnosti, kde můžete najít hodnotu v aplikaci I D.](./media/purecloud-by-genesys-tutorial/configure-6.png)

    e. Vyberte **Uložit**.

### <a name="create-purecloud-by-genesys-test-user"></a>Vytvořit PureCloud podle Genesys testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k PureCloud prostřednictvím Genesys, musí se zřídit v PureCloud pomocí Genesys. V PureCloud podle Genesys je zřizování ručním úkolem.

**Pokud chcete zřídit uživatelský účet, postupujte podle těchto kroků:**

1. Přihlaste se k PureCloud pomocí Genesys jako správce.

1. V horní části vyberte **správce** a v části **lidé & oprávnění** přejít na **lidi** .

    ![Snímek obrazovky se zobrazí okno Správce PureCloud, kde můžete vybrat osoby.](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Na stránce **lidé** vyberte **Přidat osobu**.

    ![Snímek obrazovky zobrazující stránku lidé, kde můžete přidat osobu.](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. V dialogovém okně **Přidat lidi do organizace** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka, kde můžete zadat hodnoty, které jsou popsány.](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. Do pole **jméno a příjmení zadejte** jméno uživatele. Například: **B. Simon**.

    b. Do pole **e-mail** zadejte e-mail uživatele. Například: **b. simon \@ contoso.com**.

    c. Vyberte **Vytvořit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na PureCloud podle adresy URL pro přihlášení Genesys, kde můžete spustit tok přihlášení.  

* Přejít na PureCloud podle adresy URL pro přihlašování Genesys přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlásit k PureCloud pomocí Genesys, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici PureCloud by Genesys v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud jste nakonfigurovali v režimu IDP, měli byste se automaticky přihlásit k PureCloud pomocí Genesys, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování PureCloud nástrojem Genesys můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
