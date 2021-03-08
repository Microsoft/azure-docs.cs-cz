---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Boomi | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Boomi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: jeedes
ms.openlocfilehash: 15db3e5182c39f756d4e9cd63378c3b86ab1eb32
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449519"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-boomi"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Boomi

V tomto kurzu se dozvíte, jak integrovat Boomi s Azure Active Directory (Azure AD). Když integrujete Boomi s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Boomi.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Boomi svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Boomi odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Boomi podporuje jednotné přihlašování (SSO) iniciované **IDP** .

## <a name="add-boomi-from-the-gallery"></a>Přidání Boomi z Galerie

Pokud chcete nakonfigurovat integraci Boomi do služby Azure AD, musíte přidat Boomi z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Boomi** .
1. Na panelu výsledků vyberte **Boomi** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-boomi"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Boomi

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Boomi pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Boomi.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Boomi postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte BOOMI SSO](#configure-boomi-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Boomi Test User](#create-boomi-test-user)** -to, abyste měli protějšek B. Simon v Boomi, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Boomi** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud v **základním oddílu konfigurace SAML** máte **soubor metadat poskytovatele služeb** a chcete ho nakonfigurovat v režimu iniciované **IDP** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** pro **identifikátor** a odpověď získají automaticky v základní části Konfigurace SAML.

    d. Zadejte **přihlašovací adresu URL**, třeba `https://platform.boomi.com/AtomSphere.html#build;accountId={your-accountId}` .

    > [!Note]
    > **Soubor metadat poskytovatele služeb** získáte z oddílu **Konfigurace Boomi jednotného přihlašování** , který je vysvětlen dále v tomto kurzu. Pokud se hodnoty **adresy URL** pro **identifikátor** a odpověď nezískají automaticky, vyplníte hodnoty ručně podle vašich požadavků.

1. Boomi aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky ukazuje atributy uživatele & deklarací s výchozími hodnotami, jako je například daný uživatel. jméno a EmailAddress User. mail.](common/default-attributes.png)

1. Kromě toho očekává aplikace Boomi několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Název |  Zdrojový atribut|
    | ---------------|  --------- |
    | FEDERATION_ID | uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Boomi** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Boomi.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Boomi**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-boomi-sso"></a>Konfigurace jednotného přihlašování Boomi

1. V jiném okně webového prohlížeče se přihlaste k webu Boomi společnosti jako správce.

1. Přejděte na **název společnosti** a přejděte na **Nastavení**.

1. Klikněte na kartu **Možnosti jednotného přihlašování** a proveďte následující kroky.

    ![Konfigurovat jednu Sign-On na straně aplikace](./media/boomi-tutorial/import.png)

    a. Zaškrtněte políčko **Povolit jednotné přihlašování SAML** .

    b. Kliknutím na **importovat** nahrajte stažený certifikát z Azure AD do **certifikátu poskytovatele identity**.

    c. V textovém poli **Adresa URL pro přihlášení zprostředkovatele identity** zadejte hodnotu **přihlašovací adresa URL** z okna konfigurace aplikace služby Azure AD.

    d. V poli **ID federace** vyberte **id federace v FEDERATION_ID** přepínací tlačítko elementu atributu.

    e. Zkopírujte **adresu URL metadat AtomSphere**, v prohlížeči podle vašeho výběru přejít na **adresu URL metadat** a uložte výstup do souboru. Nahrajte **adresu URL metadat** v části **základní konfigurace SAML** v Azure Portal.

    f. Klikněte na tlačítko **Uložit** .

### <a name="create-boomi-test-user"></a>Vytvořit testovacího uživatele Boomi

Aby se uživatelé Azure AD mohli přihlašovat k Boomi, musí se zřídit v Boomi. V případě Boomi je zřizování ručním úkolem.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k webu Boomi společnosti jako správce.

1. Po přihlášení přejděte na **Správa uživatelů** a přejděte na **Uživatelé**.

    ![Snímek obrazovky znázorňující stránku správy uživatelů s vybranými uživateli.](./media/boomi-tutorial/user.png "Uživatelé")

1. Klikněte na **+**  ikonu a otevře se dialogové okno **Přidat/udržovat role uživatele** .

    ![Snímek obrazovky se zobrazí zaškrtnuté políčko + ikona.](./media/boomi-tutorial/add.png "Uživatelé")

    ![Snímek obrazovky ukazuje, jak přidat/udržovat role uživatelů, kde konfigurujete uživatele.](./media/boomi-tutorial/roles.png "Uživatelé")

    a. Do textového pole **e-mailová adresa uživatele** zadejte e-maily jako uživatel B.Simon@contoso.com .

    b. Do textového pole **název** zadejte jméno uživatele jako B.

    c. Do textového pole **příjmení** zadejte jméno uživatele jako Simon.

    d. Zadejte **ID federace** uživatele. Každý uživatel musí mít ID federace, které jedinečně identifikuje uživatele v rámci účtu.

    e. Přiřaďte uživateli **standardní roli uživatele** . Nepřiřazujte roli správce, protože by jim poskytoval normální přístup do atmosféry i přístup s jednotným přihlašováním.

    f. Klikněte na **OK**.

    > [!NOTE]
    > Uživatel neobdrží uvítací oznamovací e-mail obsahující heslo, které se dá použít k přihlášení k účtu AtomSphere, protože jeho heslo je spravované prostřednictvím poskytovatele identity. K zřizování uživatelských účtů AAD můžete použít jiné nástroje pro vytváření uživatelských účtů Boomi nebo rozhraní API poskytovaná Boomi.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k Boomi, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Boomi v okně moje aplikace, měli byste se automaticky přihlásit k Boomi, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Boomi můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
