---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování (SSO) k rychlému hraničnímu cloudu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a rychle hraničním cloudem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/09/2020
ms.author: jeedes
ms.openlocfilehash: 005e2154f4e33f23a2ec45d6e0b3f620be10c1c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453616"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fastly-edge-cloud"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s rychlým Edgem z cloudu

V tomto kurzu se dozvíte, jak integrovat rychlý hraniční Cloud s Azure Active Directory (Azure AD). Při integraci rychle hraničního cloudu s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k rychlému hraničnímu cloudu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k rychlému hraničnímu cloudu s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Rychlé vyvíjení jednotného přihlašování (SSO) s povoleným jednotným zápisem do cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Rychlé Edge do cloudu podporuje **IDP** , který zahájil jednotné přihlašování.

## <a name="adding-fastly-edge-cloud-from-the-gallery"></a>Přidání rychle hraničního cloudu z Galerie

Pokud chcete nakonfigurovat integraci rychle hraničního cloudu do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat rychlý hraniční Cloud z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Fast Edge Cloud** .
1. Z panelu výsledků vyberte **Fast Edge Cloud** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fastly-edge-cloud"></a>Konfigurace a testování jednotného přihlašování Azure AD pro rychlou Edge Cloud

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí rychlého Edgeního cloudu s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rychlém hraničním cloudu.

Pokud chcete konfigurovat a testovat jednotné přihlašování Azure AD pomocí rychle hraničního cloudu, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace rychlého Edge SSO pro Cloud](#configure-fastly-edge-cloud-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte rychle hraničního uživatele cloudového testu](#create-fastly-edge-cloud-test-user)** , abyste měli protějšek B. Simon v rychlém hraničním cloudu, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce rychlá aplikace **Edge cloudové** integrace najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:  `https://api.fastly.com/saml/<CUSTOM_IDENTIFIER>`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte hodnotu skutečným identifikátorem. Pokud chcete získat hodnotu, obraťte se na [tým podpory pro cloudové klienta pro rychlejšího Edge](mailto:support@fastly.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení rychlého edgeního cloudu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k rychlému hraničnímu cloudu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **rychle Edge Cloud**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-fastly-edge-cloud-sso"></a>Konfigurace rychlého Edge pro jednotné přihlašování do cloudu

Chcete-li konfigurovat jednotné přihlašování v **rychlém Edge na straně cloudu** , je třeba odeslat stažený **soubor XML s federačními metadaty** a příslušné zkopírované adresy URL z Azure Portal do [rychlejšího Edge týmu podpory pro Cloud](mailto:support@fastly.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-fastly-edge-cloud-test-user"></a>Vytvořit rychle hraničního uživatele pro testování v cloudu

V této části vytvoříte uživatele s názvem B. Simon v rychlém hraničním cloudu. Pracujte s [rychlým týmem pro cloudovou podporu](mailto:support@fastly.com) , abyste mohli přidávat uživatele v rychlé hraniční cloudové platformě. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici rychlá hrana v cloudu, měli byste se automaticky přihlásit k rychlému hraničnímu cloudu, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si rychlý Edge Cloud s Azure AD](https://aad.portal.azure.com/)