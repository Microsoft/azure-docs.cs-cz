---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Areálovou kavárnou | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a školními kavárnami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: jeedes
ms.openlocfilehash: ec6addfb14f89b7acbc3c6d4ed62120d9af4612a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456530"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-campus-caf"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s Areálovou kavárnou

V tomto kurzu se dozvíte, jak integrovat službu Campy do kavárny pomocí Azure Active Directory (Azure AD). Při integraci služby Campy do kavárny pomocí Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k areálu kavárny.
* Umožněte uživatelům, aby se automaticky přihlásili do areálu v kavárně pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s jednotným přihlašováním (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Školní kavárna podporuje jednotné přihlašování iniciované v **SP**
* Jakmile nakonfigurujete areál kavárny, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-campus-caf-from-the-gallery"></a>Přidání areálu z Galerie do kavárny

Pokud chcete nakonfigurovat integraci areálu kavárny do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat areál kavárny z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **areál kavárny** .
1. Z panelu výsledků vyberte **areál kavárny** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-campus-caf"></a>Konfigurace a testování jednotného přihlašování v Azure AD pro Campus kavárny

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí areálu kavárny pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem Azure AD a souvisejícím uživatelem v areálu kavárny.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí areálu kavárny, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné PŘIhlašování pro školní kavárny](#configure-campus-cafe-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvoření testovacího uživatele pro školní kavárny](#create-campus-cafe-test-user)** – Pokud chcete mít protějšek B. Simon v areálu kavárny, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací pro **Campus kavárny** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb** v **základní části Konfigurace SAML** , proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![zvolit soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnota **identifikátoru** automaticky naplní v základní části Konfigurace SAML.

    ![Informace o jednotném přihlašování v areálu kavárny v doméně a adresách URL](common/sp-identifier.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://{SSO}-web.scansoftware.com/cafeweb/loginsso`

    > [!Note]
    > Pokud hodnota **identifikátoru** nezíská auto polulated, vyplňte prosím hodnotu ručně podle vašeho požadavku. Hodnota přihlašovací adresy URL není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory kavárny pro klienty](mailto:support@campuscafesoftware.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení kavárny v areálu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k areálu kavárny.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **areál kavárny**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-campus-cafe-sso"></a>Konfigurace vysokoškolského Cafe jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování v **areálu pro kavárny** , je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal [týmu podpory pro Campy](mailto:support@campuscafesoftware.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-campus-cafe-test-user"></a>Vytvoření testovacího uživatele pro areál Cafe

V této části vytvoříte v areálu kavárny uživatele s názvem B. Simon. Pracujte s [týmem podpory v areálovém kavárně](mailto:support@campuscafesoftware.com) a přidejte uživatele na platformu Campy kavárny. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Campy na přístupovém panelu, měli byste se automaticky přihlásit k školní kavárně, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Campus kavárny pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)