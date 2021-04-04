---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s v případě krize – mobilní aplikace | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a v případě řešení krize – mobilní.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: cee037b7f92dd55bb8fba264e164087a1cf8983b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92460270"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-in-case-of-crisis---mobile"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s pro případ krize – mobilní zařízení

V tomto kurzu se dozvíte, jak integrovat v případě krize – Mobile s Azure Active Directory (Azure AD). Když provádíte integraci s Azure AD v případě řešení krize – Mobile, můžete:

* Řízení ve službě Azure AD, která má přístup k aplikaci v případě krize – mobilní.
* Umožněte, aby se vaši uživatelé automaticky přihlásili v případě krize – mobilní s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* V případě krize – odběr povoleného jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* V případě krize – Mobile podporuje **IDP** , které iniciovaly jednotné přihlašování

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-in-case-of-crisis---mobile-from-the-gallery"></a>Přidání v případě krize – mobilní aplikace z Galerie

Pokud chcete nakonfigurovat integraci pro případ řešení krize-Mobile do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat v případě řešení krize-Mobile z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **případ krize – mobilní** .
1. Na panelu výsledků vyberte možnost **případ krize – mobilní** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-in-case-of-crisis---mobile"></a>Konfigurace a testování jednotného přihlašování Azure AD pro případ krize – mobilní zařízení

Konfigurace a testování jednotného přihlašování Azure AD s využitím testovacího uživatele s názvem **B. Simon** v případě řešení krize – mobilní. Aby jednotné přihlašování fungovalo, musíte v případě řešení krize-Mobile vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD v případě řešení krize – Mobile, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte v případě krizového – mobilního přihlášení SSO](#configure-in-case-of-crisis---mobile-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte v případě krizového testovacího uživatele](#create-in-case-of-crisis---mobile-test-user)** – Pokud chcete mít protějšek B. Simon v případě řešení krize-Mobile, které je propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **v případě krize – integrace mobilních** aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. Přejděte do části **Spravovat** na levé straně stránky, klikněte na **kartu vlastnosti** a zkopírujte **adresu URL přístupu uživatele** a uložte ji do svého počítače.

    ![Vlastnosti jednotného přihlašování](./media/in-case-of-crisis-mobile-tutorial/properties.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k řešení krize-Mobile.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **v případě řešení krize – mobilní**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-in-case-of-crisis---mobile-sso"></a>Konfigurace v případě krize – mobilní aplikace jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování **v případě krize – mobilní** aplikace, je potřeba odeslat stažený **certifikát (RAW)** a zkopírovat **adresu URL přístupu uživatele** z Azure Portal na [v případě týmu podpory pro krize](https://www.rockdovesolutions.com/features/enterprise-ready). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-in-case-of-crisis---mobile-test-user"></a>Vytvořit v případě krize – mobilní testovací uživatel

V této části vytvoříte uživatele s názvem Britta Simon v případě řešení krize-Mobile. V [případě řešení krize – Mobile support](https://www.rockdovesolutions.com/features/enterprise-ready) můžete v případě krize – mobilní platformy přidat uživatele. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici pro řešení krizí a mobilní aplikace, měli byste se automaticky přihlášeni v případě řešení krize – mobilní, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte případ krize – mobilní telefon s Azure AD](https://aad.portal.azure.com/)