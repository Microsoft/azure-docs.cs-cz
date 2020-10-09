---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s přístupem k programu MS Azure SSO pro sadu Office s předpisy pro Ethidex™ | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a přístup k JEDNOTNÉmu přístupu v MS Azure pro™ Office pro dodržování předpisů Ethidex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: jeedes
ms.openlocfilehash: cee76d78e934931e6f2d0ff6787d755717ea8b0b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88549017"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s přístupem k programu MS Azure SSO pro sadu Office pro dodržování předpisů Ethidex™

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) integrovat přístup k jednotnému přihlašování přes službu MS Azure pro Ethidex dodržování předpisů v™ systému Office. Když integrujete službu MS Azure SSO pro™ Ethidex dodržování předpisů pomocí služby Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k přístupu přes jednotné přihlašování ke službě MS Azure pro Ethidex dodržování předpisů v Office™.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k účtu MS Azure SSO pro Ethidex dodržování™ předpisů pomocí účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přístup k jednotnému přihlašování přes službu MS Azure pro Ethidex dodržování předpisů v Office™ předplatné s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Přístup k jednotnému přihlašování do MS Azure pro Ethidex dodržování předpisů v Office™ podporuje jednotné přihlašování **IDP**

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>Přidání přístupu k jednotnému přihlašování k Ethidex™ Office pro dodržování předpisů pomocí služby Microsoft Azure z Galerie

Pokud chcete nakonfigurovat integraci přístupu přes jednotné přihlašování MS Azure pro Ethidex dodržování předpisů pro Office™ do služby Azure AD, musíte do svého seznamu spravovaných aplikací pro SaaS přidat přístup k Ethidex pro Office dodržování předpisů pomocí jednotného přihlašování (SSO)™ z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Ethidex služby Microsoft Azure SSO Access for dodržování™ předpisů** .
1. Na panelu výsledků vyberte možnost **přístup k jednotnému přihlašování MS Azure pro Ethidex dodržování™ předpisů** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Konfigurace a testování jednotného přihlašování Azure AD pro přístup k JEDNOTNÉmu přístupu ke službě MS Azure pro Ethidex dodržování předpisů v Office™

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí služby MS Azure SSO Access pro Ethidex dodržování předpisů v Office™ pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci přístupového programu k JEDNOTNÉmu přístupu v MS Azure pro Ethidex pro dodržování předpisů v Office™.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby MS Azure SSO Access pro Ethidex dodržování předpisů v Office™, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte přístup k jednotnému přihlašování přes službu MS Azure pro Ethidex dodržování předpisů Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte přístup k jednotnému PŘIhlašování pomocí služby MS Azure pro Ethidex dodržování předpisů pro testovacího uživatele Office](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** , abyste měli protějšek B. Simon ve službě MS Azure Access pro dodržování předpisů Ethidex™ Office, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **Microsoft Azure Access pro Ethidex dodržování předpisů v Office™** integraci aplikací najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `com.ethidex.prod.<CLIENTID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.ethidex.com/saml2/sp/acs/<CLIENTID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [přístup k programu Microsoft Azure SSO pro Ethidex dodržování předpisů pro sadu Office™ support](mailto:support@ethidex.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Přístup k jednotnému přihlašování do MS Azure pro Ethidex dodržování předpisů pro aplikaci Office™ aplikace očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. Přístup k jednotnému přihlašování do MS Azure pro Ethidex dodržování předpisů v aplikacích Office™ očekává, že **NameIdentifier** se namapují pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (RAW)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

1. V části **nastavení přístupu k jednotnému přístupu do MS Azure pro Ethidex dodržování předpisů v Office™** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k přístupu pomocí jednotného přihlašování (SSO) k MS Azure pro dodržování předpisů Ethidex™ Office.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **přístup k jednotnému přístupu pomocí služby MS Azure pro Ethidex dodržování předpisů v Office™**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>Konfigurace přístupu k jednotnému přihlašování přes službu MS Azure pro Ethidex dodržování předpisů Office SSO

Ke konfiguraci jednotného přihlašování na základě jednotného přihlašování v **MS Azure pro Ethidex dodržování předpisů v systému office™** je třeba odeslat stažený **certifikát (RAW)** a příslušné zkopírované adresy URL z Azure Portal do služby [MS Azure Ethidex pro™ dodržování předpisů pro](mailto:support@ethidex.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>Vytvoření přístupu ke službě Microsoft Azure SSO pro uživatele testovacího Ethidex dodržování předpisů

V této části vytvoříte uživatele s názvem B. Simon ve službě MS Azure přístup pomocí jednotného přihlašování pro Ethidex pro dodržování předpisů v Office™. Pracujte s [přístupem k jednotnému přístupu ke službě MS Azure pro Ethidex dodržování předpisů v sadě office™ support](mailto:support@ethidex.com) , aby bylo možné přidávat uživatele v rámci služby Ethidex (Microsoft Azure SSO Access) pro™ platformy Office. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Ethidexovat přístup k JEDNOTNÉmu přístupu přes službu MS Azure pro™, měli byste se automaticky přihlásili k přístupu k programu Ethidex pro dodržování předpisů pro™ Office, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si službu MS Azure pro Ethidex dodržování předpisů pro Office™ s Azure AD](https://aad.portal.azure.com/)

