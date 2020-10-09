---
title: 'Kurz: Azure Active Directory integrace s výukou ke sledování povědomí o zabezpečení KnowBe4 | Microsoft Docs'
description: Naučte se konfigurovat jednotné přihlašování mezi Azure Active Directory a KnowBe4 a školením týkajícím se zabezpečení.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: c6e70268dcc252964a0b046488c399f474660ca5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88540048"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Kurz: Azure Active Directory integrace s výukou ke sledování povědomí o zabezpečení KnowBe4

V tomto kurzu se naučíte integrovat školení týkající se KnowBe4 zabezpečení pomocí Azure Active Directory (Azure AD).
Integrace KnowBe4ch školení k zabezpečení pomocí Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k KnowBe4 školení zabezpečení.
* Uživatelům můžete povolit, aby se automaticky přihlásili k KnowBe4 školením na zabezpečení (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD pomocí školení KnowBe4 Security potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* KnowBe4 zabezpečení – školení pro jednotné přihlašování s povoleným odběrem

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Školení pro sledování povědomí v KnowBe4 podporuje jednotné přihlašování s využitím služeb **SP**

* Školení pro sledování povědomí v KnowBe4 podporuje při zřizování uživatelů **jenom včas**

## <a name="adding-knowbe4-security-awareness-training-from-the-gallery"></a>Přidání školení ke sledování KnowBe4 z Galerie

Pokud chcete nakonfigurovat integraci KnowBe4ch školení k zabezpečení do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat školení KnowBe4 pro zabezpečení z galerie.

**Pokud chcete do galerie přidat školení KnowBe4 Security, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **KnowBe4 sledování povědomí o zabezpečení**, vyberte **KnowBe4 sledování povědomí o zabezpečení** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![KnowBe4 sledování povědomí o zabezpečení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí školení KnowBe4 na zabezpečení na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo možné jednotné přihlašování pracovat, je třeba zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci školení o povědomí o zabezpečení KnowBe4.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí školení pro sledování povědomí v KnowBe4, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujete jednotné přihlašování na KnowBe4 zabezpečení](#configure-knowbe4-security-awareness-training-single-sign-on)** , abyste mohli konfigurovat nastavení jednoho Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytváření KnowBe4ch testů na sledování zabezpečení pro uživatele](#create-knowbe4-security-awareness-training-test-user)** – u Britta Simon ve výukovém programu, který je propojen s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí školení KnowBe4 na zabezpečení, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce pro školení týkající se **Sledování zabezpečení KnowBe4** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![KnowBe4 zabezpečení – informace o jednotném přihlašování v doméně a v adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > Hodnota adresy URL pro přihlášení není v reálném čase. Aktualizujte tuto hodnotu skutečnou adresou URL pro přihlášení. Pokud chcete získat tuto hodnotu, kontaktujte [tým podpory pro školení k KnowBe4 zabezpečení](mailto:support@KnowBe4.com) . Můžete se také podívat na vzor uvedený v části **základní konfigurace SAML** v Azure Portal.

    b. Do textového pole **identifikátor (ID entity)** zadejte hodnotu řetězce: `KnowBe4`

    > [!NOTE]
    > Rozlišují se malá a velká písmena.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na **Stáhnout** a Stáhněte si **certifikát (RAW)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificateraw.png)

6. V části **Nastavení školicích kurzů zabezpečení KnowBe4** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-knowbe4-security-awareness-training-single-sign-on"></a>Konfigurace KnowBe4 a školení o sledování zabezpečení na jednom Sign-On

Pokud chcete nakonfigurovat jednotné přihlašování na **KnowBe4i školení na zabezpečení** , je potřeba odeslat stažený **certifikát (RAW)** a příslušné zkopírované adresy URL z Azure Portal k [KnowBe4 týmu podpory pro školení](mailto:support@KnowBe4.com)týkající se zabezpečení. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k odbornému školení KnowBe4 zabezpečení.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost školení týkající se **sledování KnowBe4 zabezpečení**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **KnowBe4 školení zabezpečení**.

    ![Odkaz na KnowBe4u pro školení o sledování zabezpečení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Vytvoření testovacího uživatele pro školení ke sledování KnowBe4

Cílem této části je vytvořit uživatele s názvem Britta Simon ve výukovém programu pro sledování povědomí KnowBe4 Security. Školení pro sledování povědomí KnowBe4 podporuje zřizování za běhu, které je ve výchozím nastavení povolené.

V této části není žádná položka akce. Nový uživatel se vytvoří během pokusu o přístup k školení KnowBe4 Security, pokud ještě neexistuje.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory školení pro KnowBe4 zabezpečení](mailto:support@KnowBe4.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici školení pro KnowBe4 na zabezpečení, měli byste se automaticky přihlásit ke školení KnowBe4 na zabezpečení, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
