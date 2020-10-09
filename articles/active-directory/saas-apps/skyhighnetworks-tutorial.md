---
title: 'Kurz: Azure Active Directory integrace s cloudovou konfigurací jednotného přihlašování služby Azure AD v MVISION | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MVISION cloudovou konfigurací jednotného přihlašování Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d529123b52e0e90702839f6822a586cf53dffa52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546708"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Kurz: integrace MVISION cloudového jednotného přihlašování do cloudu Azure AD pomocí Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat konfiguraci MVISION cloudu Azure AD SSO pomocí Azure Active Directory (Azure AD). Když integrujete MVISION cloudovou konfiguraci jednotného přihlašování Azure AD pomocí služby Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k MVISION cloudové konfiguraci jednotného přihlašování služby Azure AD.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k MVISION konfiguraci jednotného přihlašování služby Azure AD pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* MVISION cloudové předplatné Azure AD jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.


## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konfigurace jednotného přihlašování v cloudu Azure AD MVISION podporuje **aktualizace SP a IDP** .
* Jakmile nakonfigurujete Dropbox, můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Přidání konfigurace MVISION cloudového jednotného přihlašování Azure AD z Galerie

Pokud chcete nakonfigurovat integraci MVISION cloudu služby Azure AD SSO do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat MVISION cloudovou konfiguraci služby Azure AD SSO z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **MVISION cloudová konfigurace služby Azure AD SSO** .
1. Na panelu výsledků vyberte **MVISION cloudová konfigurace jednotného přihlašování Azure AD** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s MVISION cloudovou konfigurací služby Azure AD SSO pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v konfiguraci služby MVISION cloudu Azure AD SSO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s MVISION cloudovou konfigurací jednotného přihlašování služby Azure AD, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte jednotné přihlašování MVISION cloudové služby Azure AD pro konfiguraci](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** jednotného přihlašování – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
    1. **[Vytvořte MVISION cloudového testu konfigurace jednotného přihlašování Azure AD](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** , abyste měli protějšek Britta Simon v MVISION cloudové konfiguraci služby Azure AD, která je propojená s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **služby Datadog** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)


4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![MVISION Cloud služby Azure AD SSO konfigurace jednotného přihlašování a adres URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory konfigurace jednotného přihlašování služby Azure AD pro MVISION Cloud](mailto:support@skyhighnetworks.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení cloudového oddílu pro konfiguraci služby Azure AD jednotného přihlašování pro MVISION** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména****brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k MVISION konfiguraci jednotného přihlašování ke cloudu Azure AD.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **MVISION cloudová konfigurace služby Azure AD SSO**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **MVISION cloudová konfigurace služby Azure AD jednotného přihlašování**.

    ![Odkaz na konfiguraci služby Azure AD SSO MVISION cloudu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Konfigurace jednotného přihlašování k MVISION cloudového jednotného přihlašování služby Azure AD

Ke konfiguraci jednotného přihlašování na straně **MVISION cloudové konfigurace jednotného přihlašování služby Azure AD** je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [MVISION cloudu podpory konfigurace jednotného přihlašování Azure AD](mailto:support@skyhighnetworks.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Vytvořit MVISION cloudového testu konfigurace jednotného přihlašování Azure AD

V této části vytvoříte uživatele s názvem B. Simon v konfiguraci služby jednotného přihlašování v MVISION cloudu Azure AD. Pokud chcete přidat uživatele na MVISION cloudovou platformu pro jednotné přihlašování Azure AD, pracujte s [MVISION cloudem podpory konfigurace jednotného přihlašování služby Azure AD](mailto:support@skyhighnetworks.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici konfigurace služby MVISION cloud Azure AD SSO, měli byste se automaticky přihlásit ke cloudové konfiguraci služby Azure AD SSO MVISION, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si MVISION cloudové konfigurace jednotného přihlašování Azure AD pomocí Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)