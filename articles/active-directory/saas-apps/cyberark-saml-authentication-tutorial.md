---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s ověřováním CyberArk SAML | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CyberArk ověřování SAML.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: 7f6ef50fbe8870ef2bf412795edea3a798798dcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88550040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cyberark-saml-authentication"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s ověřováním SAML v CyberArk

V tomto kurzu se dozvíte, jak integrovat ověřování CyberArk SAML pomocí Azure Active Directory (Azure AD). Když integrujete CyberArk ověřování SAML s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k CyberArk ověřování SAML.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k CyberArk ověřování SAML pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* CyberArk předplatného jednotného přihlašování (SSO) s povoleným ověřováním SAML.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* CyberArk ověřování SAML podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-cyberark-saml-authentication-from-the-gallery"></a>Přidání ověřování CyberArk SAML z Galerie

Pokud chcete nakonfigurovat integraci CyberArk ověřování SAML do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat CyberArk ověřování SAML z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **CyberArk ověřování SAML** .
1. Na panelu výsledků vyberte **CyberArk ověřování SAML** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cyberark-saml-authentication"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ověřování CyberArk SAML

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s CyberArk ověřováním SAML pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v rámci ověřování SAML v CyberArk vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s CyberArk ověřováním SAML, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurovat jednotné přihlašování **[CYBERARK SAML](#configure-cyberark-saml-authentication-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořit testovacího uživatele pro ověřování SAML v CyberArk](#create-cyberark-saml-authentication-test-user)** , který má protějšek B. Simon v CYBERARK ověřování SAML, které je propojené s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ověřování SAML CyberArk** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:  `https://<PVWA DNS or IP>/passwordvault/api/auth/saml/logon`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<PVWA DNS or IP>/PasswordVault/v10/logon/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a adresy URL Sign-On. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory CYBERARK SAML pro ověřování klienta](mailto:bizdevtech@cyberark.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení ověřování SAML pro CyberArk** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ověřování CyberArk SAML.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **CyberArk ověřování SAML**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cyberark-saml-authentication-sso"></a>Konfigurace jednotného přihlašování pro ověřování SAML CyberArk

Ke konfiguraci jednotného přihlašování na straně **ověřování CYBERARK SAML** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory ověřování SAML CyberArk](mailto:bizdevtech@cyberark.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-cyberark-saml-authentication-test-user"></a>Vytvořit testovacího uživatele pro ověřování SAML CyberArk

V této části vytvoříte uživatele s názvem B. Simon při ověřování CyberArk SAML. Pokud chcete přidat uživatele v CyberArk platformě ověřování SAML, pracujte s [CyberArk týmu podpory pro ověřování SAML](mailto:bizdevtech@cyberark.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ověřování CyberArk SAML na přístupovém panelu byste měli být automaticky přihlášeni k ověřování CyberArk SAML, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte CyberArk ověřování SAML pomocí Azure AD](https://aad.portal.azure.com/)