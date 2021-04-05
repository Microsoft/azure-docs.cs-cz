---
title: 'Kurz: Azure Active Directory integrace s cloudovým Portál pro správu pro Microsoft Azure | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a cloudovou Portál pro správu pro Microsoft Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: 950744e0b8b2f722d801c911d6fd2c9112f035ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522487"
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Kurz: Azure Active Directory integrace s cloudovým Portál pro správu pro Microsoft Azure

V tomto kurzu se naučíte, jak integrovat Cloud Portál pro správu pro Microsoft Azure s Azure Active Directory (Azure AD).
Integrací cloudových Portál pro správu pro Microsoft Azure se službou Azure AD získáte následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup ke cloudovým Portál pro správu Microsoft Azure.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili do cloudové Portál pro správu Microsoft Azure (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s cloudovým Portál pro správu pro Microsoft Azure potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Cloud Portál pro správu pro předplatné s povoleným Microsoft Azurem jednotného přihlašování

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cloud Portál pro správu pro Microsoft Azure podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Přidání cloudových Portál pro správu pro Microsoft Azure z Galerie

Pokud chcete nakonfigurovat integraci cloudových Portál pro správu pro Microsoft Azure do Azure AD, musíte přidat cloudové Portál pro správu pro Microsoft Azure z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Portál pro správu cloudu pro Microsoft Azure z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **cloud portál pro správu pro Microsoft Azure**, vyberte **cloudové portál pro správu pro Microsoft Azure** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Cloud Portál pro správu pro Microsoft Azure v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí cloudových Portál pro správu pro Microsoft Azure na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v cloudu Portál pro správu pro Microsoft Azure.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí cloudových Portál pro správu pro Microsoft Azure, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte Cloud portál pro správu pro Microsoft Azure jednotné přihlašování](#configure-cloud-management-portal-for-microsoft-azure-single-sign-on)** – ke konfiguraci jednoho Sign-On nastavení na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření cloudové portál pro správu pro Microsoft Azure testovacího uživatele](#create-cloud-management-portal-for-microsoft-azure-test-user)** – Pokud chcete mít protějšek Britta Simon v cloudovém Portál pro správu pro Microsoft Azure, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí cloudových Portál pro správu pro Microsoft Azure, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Cloud portál pro správu pro Microsoft Azure** integrace aplikací vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Cloudové Portál pro správu pro Microsoft Azure doménových a adres URL s jednotným přihlašováním](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://portal.newsignature.com/<instancename>
    https://portal.igcm.com/<instancename>
    ```

    b. Do pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://<subdomain>.igcm.com
    https://<subdomain>.newsignature.com
    ```

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    https://<subdomain>.igcm.com/<instancename>
    https://<subdomain>.newsignature.com
    https://<subdomain>.newsignature.com/<instancename>
    ```

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [Cloud Portál pro správu Microsoft Azure tým podpory klientů](mailto:jczernuszka@newsignature.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit cloudové portál pro správu pro Microsoft Azure** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-cloud-management-portal-for-microsoft-azure-single-sign-on"></a>Konfigurace cloudových Portál pro správu pro Microsoft Azure jednoho Sign-On

Ke konfiguraci jednotného přihlašování v **cloudové portál pro správu pro Microsoft Azure** straně je potřeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal do [cloudové portál pro správu pro tým podpory Microsoft Azure](mailto:jczernuszka@newsignature.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure udělením přístupu ke cloudovým Portál pro správu Microsoft Azure.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte **cloudové portál pro správu pro Microsoft Azure**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **cloudové portál pro správu pro Microsoft Azure**.

    ![Odkaz Portál pro správu cloudu pro Microsoft Azure v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-cloud-management-portal-for-microsoft-azure-test-user"></a>Vytvořit cloudovou Portál pro správu pro Microsoft Azure testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v Cloud Portál pro správu pro Microsoft Azure. Pokud chcete přidat uživatele do cloudového Portál pro správu pro Microsoft Azure platformu, pracujte s [cloudovým portál pro správu pro Microsoft Azure týmu podpory](mailto:jczernuszka@newsignature.com) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Cloud Portál pro správu for Microsoft Azure na přístupovém panelu byste měli být automaticky přihlášení ke cloudové Portál pro správu pro Microsoft Azure, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)