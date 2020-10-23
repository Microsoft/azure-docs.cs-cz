---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s využitím Coda | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Coda.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.openlocfilehash: c78744538991e337b226e49a053807ef8174d507
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Coda

V tomto kurzu se dozvíte, jak integrovat CODA s Azure Active Directory (Azure AD). Když integruje CODA s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Coda.
* Umožněte uživatelům, aby se automaticky přihlásili k Coda pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s podporou jednotného přihlašování (SSO) pro CODA (Enterprise) s GDrive integrací je zakázané. Obraťte se na [tým podpory Coda](mailto:support@coda.io) a zakažte integraci GDrive pro vaši organizaci, pokud je aktuálně povolená.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Systém Coda podporuje jednotné přihlašování **IDP**

* Systém Coda podporuje zřizování uživatelů **jenom v čase**

* Po nakonfigurování Coda můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace přesahují podmíněný přístup. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>Přidání Coda z Galerie

Pokud chcete nakonfigurovat integraci sady Coda do služby Azure AD, musíte přidat Coda z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Coda** .
1. Na panelu výsledků vyberte **Coda** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Coda

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Coda pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Coda.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Coda, dokončete následující stavební bloky:

1. **[Zahajte konfiguraci jednotného přihlašování Coda](#begin-configuration-of-coda-sso)** – Chcete-li zahájit konfiguraci jednotného přihlašování v systému Coda.
1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
   * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
   * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování Coda](#configure-coda-sso)** – pro dokončení konfigurace nastavení jednotného přihlašování v systému Coda.
   * **[Vytvořte testovacího uživatele Coda](#create-coda-test-user)** , abyste měli protějšek B. Simon v systému Coda, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="begin-configuration-of-coda-sso"></a>Zahájit konfiguraci Coda jednotného přihlašování

Pokud chcete začít, postupujte podle těchto kroků v Coda.

1. V prostředí Coda otevřete panel **Nastavení organizace** .

   ![Otevřít nastavení organizace](media/coda-tutorial/org-settings.png)

1. Ujistěte se, že má vaše organizace zapnutou integraci GDrive. Pokud je tato možnost aktuálně povolená, obraťte se na [tým podpory pro Coda](mailto:support@coda.io) , který vám usnadní migraci GDrive.

   ![GDrive zakázané](media/coda-tutorial/gdrive-off.png)

1. V části **ověřování pomocí jednotného přihlašování (SAML)** vyberte možnost **Konfigurovat protokol SAML** .

   ![Nastavení SAML](media/coda-tutorial/saml-settings-link.png)

1. Poznamenejte si hodnoty pro **ID entity** a **adresu URL odpovědi SAML**, které budete potřebovat v následujících krocích.

   ![ID entity a adresa URL odpovědi SAML pro použití v Azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Coda** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte hodnoty pro následující pole:

   a. Do textového pole **identifikátor** zadejte "ID entity". Měl by postupovat podle vzoru: `https://coda.io/samlId/<CUSTOMID>`

   b. Do textového pole **Adresa URL odpovědi** zadejte "URL odpověď SAML". Měl by postupovat podle vzoru: `https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Vaše hodnoty se budou lišit od výše uvedeného. vaše hodnoty najdete v konzole Coda "konfigurace SAML". Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Coda** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Coda.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte položku **Coda**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-coda-sso"></a>Konfigurace jednotného přihlašování Coda

Chcete-li dokončit instalaci, zadejte hodnoty z Azure Active Directory v panelu pro nastavení Coda pro modul **SAML** .

1. V prostředí Coda otevřete panel **Nastavení organizace** .
1. V části **ověřování pomocí jednotného přihlašování (SAML)** vyberte možnost **Konfigurovat protokol SAML** .
1. Nastavte **poskytovatele SAML** na **Azure Active Directory**.
1. V části **Adresa URL pro přihlášení zprostředkovatele identity**vložte **přihlašovací adresu URL** z konzoly Azure.
1. V části **Vystavitel zprostředkovatele identity**vložte **identifikátor Azure AD** z konzoly Azure.
1. V části **veřejný certifikát poskytovatele identity**vyberte možnost **nahrát certifikát** a vyberte soubor certifikátu, který jste stáhli dříve.
1. Vyberte **Uložit**.

Tím se dokončí práce, která je nezbytná pro nastavení připojení SAML SSO.

### <a name="create-coda-test-user"></a>Vytvořit testovacího uživatele Coda

V této části se ve Coda vytvoří uživatel s názvem Britta Simon. Systém Coda podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povoleno. V této části není žádná položka akce. Pokud uživatel ve Coda ještě neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici CODA na přístupovém panelu byste se měli automaticky přihlášeni k Coda, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Coda pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Coda pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)