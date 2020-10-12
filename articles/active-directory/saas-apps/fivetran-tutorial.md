---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Fivetran | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Fivetran.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 9d6951456593c57f9def80990e582a5ff54cc5d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91312558"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Fivetran

V tomto kurzu se dozvíte, jak integrovat Fivetran s Azure Active Directory (Azure AD). Když integrujete Fivetran s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Fivetran.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Fivetran svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Účet Fivetran.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Fivetran podporuje jednotné přihlašování **IDP** .
* Fivetran podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-fivetran-from-the-gallery"></a>Přidání Fivetran z Galerie

Pokud chcete nakonfigurovat integraci Fivetran do služby Azure AD, musíte přidat Fivetran z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Fivetran** .
1. Na panelu výsledků vyberte **Fivetran** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Fivetran

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Fivetran pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Fivetran.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Fivetran postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte FIVETRAN SSO](#configure-fivetran-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Fivetran Test User](#create-fivetran-test-user)** -to, abyste měli protějšek B. Simon v Fivetran, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Fivetran** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základní sekci konfigurace SAML** je aplikace předem nakonfigurovaná a potřebné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .


1. Fivetran aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace Fivetran několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | -------------- | --------- |
    | FirstName | User. křestní jméno |
    | LastName | User. příjmení |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Fivetran** zkopírujte **adresu URL pro přihlášení** a hodnoty **identifikátoru služby Azure AD** .

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Fivetran.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Fivetran**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci nebyla nastavena žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-fivetran-sso"></a>Konfigurace jednotného přihlašování Fivetran

V této části nakonfigurujete jednotné přihlašování na straně **Fivetran** .

1. V jiném okně webového prohlížeče se přihlaste ke svému účtu Fivetran jako vlastník účtu.
1. Vyberte šipku v levém horním rohu okna a v rozevíracím seznamu vyberte **Spravovat účet** .

   ![Snímek obrazovky, který zobrazuje vybranou možnost nabídky Spravovat účet](media/fivetran-tutorial/fivetran-1.png)

1. Přejít do části **Konfigurace SAML** stránky **Nastavení** .

   ![Snímek obrazovky, který zobrazuje podokno konfigurace SAML s zvýrazněnými možnostmi konfigurace](media/fivetran-tutorial/fivetran-2.png)

   1. Pro možnost **Povolit ověřování SAML**vyberte **zapnuto**.
   1. Do pole **přihlašovací adresa URL**vložte hodnotu **přihlašovací adresa URL**, kterou jste zkopírovali z Azure Portal.
   1. Ve **vystaviteli**vložte hodnotu **identifikátoru Azure AD**, který jste zkopírovali z Azure Portal.
   1. Otevřete stažený soubor certifikátu v textovém editoru, zkopírujte certifikát do schránky a vložte ho do textového pole **veřejný certifikát** .
   1. Vyberte **Uložit konfiguraci**.

### <a name="create-fivetran-test-user"></a>Vytvořit testovacího uživatele Fivetran

V této části se v Fivetran vytvoří uživatel s názvem B. Simon. Fivetran podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Fivetran neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

1. Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Fivetran, pro které jste nastavili jednotné přihlašování. 

2. Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici Fivetran, měli byste se automaticky přihlásit k Fivetran, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Fivetran můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

