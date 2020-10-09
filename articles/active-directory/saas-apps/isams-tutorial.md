---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím iSAMs | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iSAM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: 5486752c8a1e36eba047ffd4d82b10cddfc771a1
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850011"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-isams"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím iSAMs

V tomto kurzu se dozvíte, jak integrovat iSAMs s Azure Active Directory (Azure AD). Když ve službě Azure AD integrujete iSAM, můžete:

* Řízení ve službě Azure AD, která má přístup k iSAMs.
* Umožněte uživatelům, aby se automaticky přihlásili k iSAM pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* odběry iSAM s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* iSAM podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Jakmile nakonfigurujete iSAM, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-isams-from-the-gallery"></a>Přidávání iSAM z Galerie

Pokud chcete nakonfigurovat integraci iSAMs do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat iSAMs z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **ISAMs** .
1. Vyberte z panelu výsledků možnost **ISAM** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-isams"></a>Konfigurace a testování jednotného přihlašování Azure AD pro iSAMs

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí iSAMs pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v iSAM.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí iSAMs, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování ISAM](#configure-isams-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvoření testovacího uživatele ISAMs](#create-isams-test-user)** – Pokud chcete mít protějšek B. Simon v ISAMs, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ISAM** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.isams.cloud/main/sso/saml2`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.isams.cloud/main/sso/saml2/acs`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.isams.cloud/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klientů ISAM](mailto:support@isams.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k iSAMs.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **ISAM**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-isams-sso"></a>Konfigurace jednotného přihlašování iSAM

1. Přihlaste se k iSAMs jako správce.

1. Přejděte do ovládacích panelů a otevřete modul **ověřování** .
1. V nabídce na pravé straně vyberte **Zprostředkovatelé identity** .

    ![Snímek obrazovky se zobrazí konfigurace služby Active Directory s vybranými zprostředkovateli identity.](./media/isams-tutorial/click-identity-provider.png)

1. Vybrat **Přidat poskytovatele**

    ![Snímek obrazovky se zobrazí zprostředkovatelé identity s vybranými přidat poskytovatele.](./media/isams-tutorial/add-identity-provider.png)


1. Na následující stránce proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v průvodci zprostředkovateli identity, kde můžete postupovat podle pokynů.](./media/isams-tutorial/configure-isams.png)

    a. Do textového pole **název** zadejte platný název jako `Saml2 Azure` . Toto je název, který se zobrazí na přihlašovací stránce.

    b. Do pole Adresa URL metadat zadejte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal.
    
    c. Stiskněte **importovat**.
    
    d. V seznamu **aplikace** v části **povolené klientské aplikace** vyberte všechny aplikace typu ISAM, u kterých chcete, aby se Váš poskytovatel zobrazoval na přihlašovací stránce pro.

    e. Klikněte na **uložit & zavřít**.

### <a name="create-isams-test-user"></a>Vytvořit testovacího uživatele iSAMs

1. Přihlaste se k iSAMs jako správce.

2.  Přejít na **ovládací panel Hlavní**  ->  **zabezpečení & oprávnění**uživatele  ->  **uživatelské účty**uživatelské  ->  **Možnosti & úlohy**  ->  **Upravit vlastnosti uživatele**

    ![Snímek obrazovky se zobrazí stránka uživatelských účtů s vybraným možnost upravit vlastnosti uživatele.](./media/isams-tutorial/modify-user-properties.png)


3. Ve výsledném překryvném okně vyberte kartu **Podrobnosti účtu** a změňte **autorizaci** na tohoto nově vytvořeného poskytovatele identity.

    ![Snímek obrazovky zobrazuje podrobnosti účtu s hodnotou pro autorizaci.](./media/isams-tutorial/account-details.png)

4. Klikněte na **uložit & zavřít**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici iSAMs na přístupovém panelu, měli byste být automaticky přihlášeni k iSAM, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte iSAM pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
