---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ContractSafe typu Saml2 SSO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ContractSafe typu Saml2 SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: 2d236b9910e2eda8e574d020544d625a68aefb81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455303"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>Kurz: Integrace Azure Active Directory jednotného přihlašování (SSO) pomocí jednotného přihlašování ContractSafe typu Saml2

V tomto kurzu se dozvíte, jak integrovat ContractSafe typu Saml2 SSO pomocí Azure Active Directory (Azure AD). Když integrujete jednotné přihlašování ContractSafe typu Saml2 s Azure AD, můžete:

* Řízení, kdo má přístup k jednotnému přihlašování ContractSafe typu Saml2 ve službě Azure AD
* Umožněte uživatelům, aby se automaticky přihlásili k ContractSafe SSO pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Na začátek budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ContractSafe typu Saml2 SSO s povoleným SSO.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. ContractSafe typu Saml2 SSO podporuje jednotné přihlašování iniciované v **IDP**.

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>Přidat jednotné přihlašování ContractSafe typu Saml2 z Galerie

Pokud chcete nakonfigurovat integraci ContractSafe typu Saml2 SSO do Azure AD, musíte do svého seznamu spravovaných aplikací přidat ContractSafe typu Saml2 SSO z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ContractSafe typu Saml2 SSO** .
1. Na panelu výsledků vyberte **ContractSafe typu Saml2 SSO** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro ContractSafe typu Saml2 SSO

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí jednotného přihlašování (ContractSafe typu Saml2 SSO) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ContractSafe typu Saml2 SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování ContractSafe typu Saml2, dokončete následující stavební bloky:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.
   * [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro otestování jednotného přihlašování Azure AD pomocí účtu **B. Simon** .
   * [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) povolení **B. Simon** pro použití jednotného přihlašování služby Azure AD.

1. [NAKONFIGURUJTE jednotné přihlašování ContractSafe typu Saml2](#configure-contractsafe-saml2-sso) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
   * [Vytvořte uživatele ContractSafe typu Saml2 SSO Test User](#create-a-contractsafe-saml2-sso-test-user) , který bude mít protějšek **B. Simon** v ContractSafe typu Saml2 SSO, který je propojený s předprezentací Azure AD.
2. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ContractSafe typu Saml2 SSO** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu Upravit (pero) pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte do odpovídajících polí následující hodnoty:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího formátu: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího formátu: `https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory ContractSafe typu Saml2 SSO Client support](mailto:support@contractsafe.com) . Můžete se také podívat na formáty zobrazené v části **základní konfigurace SAML** v Azure Portal.

1. ContractSafe typu Saml2 SSO očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Běžné výchozí atributy](common/default-attributes.png)

1. Kromě výchozích atributů očekává aplikace ContractSafe typu Saml2 SSO několik dalších atributů, které se vrátí zpátky v odpovědi SAML. Tyto atributy jsou předem vyplněné, ale můžete je zkontrolovat podle svých požadavků. Následující seznam obsahuje další atributy.

    | Name | Zdrojový atribut|
    | ---------------| --------------- |
    | e-mailová adresa | User. userPrincipalName |
    | e-mail | User. onpremisesuserprincipalname |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML federačních metadat**. Vyberte **Stáhnout** a Stáhněte certifikát a pak ho uložte na svém počítači.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **set up ContractSafe typu Saml2 SSO** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem **B. Simon**.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**. Vyberte **Uživatelé** a pak vyberte **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte e-mailovou adresu ve `username@companydomain.extension` formátu. Příklad: `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Vyberte **Vytvořit**.

## <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte **B. Simon** používat jednotné přihlašování Azure tím, že udělíte přístup k CONTRACTSAFE typu Saml2 SSO.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **ContractSafe typu Saml2 SSO**.
1. Na stránce Přehled aplikace najděte část **Správa** a pak vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

   ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** . Pak vyberte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte tlačítko **přiřadit** .

## <a name="configure-contractsafe-saml2-sso"></a>Konfigurace jednotného přihlašování ContractSafe typu Saml2

Pokud chcete nakonfigurovat jednotné přihlašování na straně **jednotného přihlašování ContractSafe typu Saml2** , je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory pro ContractSafe typu Saml2 SSO](mailto:support@contractsafe.com). Tým je zodpovědný za správné nastavení připojení SAML na obou stranách.

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>Vytvoření testovacího uživatele ContractSafe typu Saml2 SSO

Vytvořte uživatele s názvem B. Simon v rámci ContractSafeu typu Saml2 SSO. Spolupracujte s [týmem podpory ContractSafe typu Saml2 SSO](mailto:support@contractsafe.com) a přidejte uživatele na platformě CONTRACTSAFE typu Saml2 SSO. Před použitím jednotného přihlašování se musí vytvořit a aktivovat uživatelé.

## <a name="test-sso"></a>Test SSO

Otestujte konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu. Když vyberete dlaždici SSO ContractSafe typu Saml2 SSO na přístupovém panelu, měli byste se automaticky přihlásit k rozhraní ContractSafe typu Saml2 SSO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte jednotné přihlašování ContractSafe typu Saml2 s Azure AD](https://aad.portal.azure.com/)