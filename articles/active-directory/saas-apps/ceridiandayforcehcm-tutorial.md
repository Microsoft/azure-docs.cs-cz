---
title: 'Kurz: Azure Active Directory integrace s Ceridian Dayforce HCM | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ceridian Dayforce HCM.
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
ms.openlocfilehash: b2241ff6841a5b3f536419336dc4f4fd888663d9
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673065"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Kurz: Azure Active Directory integrace s Ceridian Dayforce HCM

V tomto kurzu se dozvíte, jak integrovat Ceridian Dayforce HCM s Azure Active Directory (Azure AD).
Integrace Ceridian Dayforce HCM se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Ceridian Dayforce HCM.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Ceridian Dayforce HCM (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Ceridian Dayforce HCM budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné Ceridian Dayforce HCM s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Ceridian Dayforce HCM podporuje jednotné přihlašování (SSO) iniciované **SP**

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Přidání Ceridian Dayforce HCM z Galerie

Pokud chcete nakonfigurovat integraci Ceridian Dayforce HCM do Azure AD, musíte přidat Ceridian Dayforce HCM z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Ceridian Dayforce HCM z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Ceridian DAYFORCE HCM**, vyberte **Ceridian Dayforce HCM** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Ceridian Dayforce HCM v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Ceridian Dayforce HCM založeného na testovacím uživateli s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Ceridian Dayforce HCM.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Ceridian Dayforce HCM, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Ceridian DAYFORCE HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Ceridian DAYFORCE HCM Test User](#create-ceridian-dayforce-hcm-test-user)** -to, abyste měli protějšek Britta Simon v Ceridian Dayforce HCM, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Ceridianu Dayforce HCM, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Ceridian Dayforce HCM** Application Integration vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Ceridian Dayforce HCM informace o jednotném přihlašování v doméně a adresách URL](common/sp-identifier-reply.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL používanou vašimi uživateli, abyste se přihlásili do vaší aplikace CERIDIAN Dayforce HCM.

    | Prostředí | URL |
    | :-- | :-- |
    | Pro produkční prostředí | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Pro test | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | Prostředí | URL |
    | :-- | :-- |
    | Pro produkční prostředí | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Pro test | `https://fs-test.dayforcehcm.com/sp` |

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL, kterou služba Azure AD používá k odeslání odpovědi.

    | Prostředí | URL |
    | :-- | :-- |
    | Pro produkční prostředí | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Pro test | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL, identifikátor a adresu URL odpovědi. Pro získání těchto hodnot se obraťte na [tým podpory Ceridian DAYFORCE HCM](https://www.ceridian.com/support) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Aplikace Ceridian Dayforce HCM očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jeden Sign-On se** stránkou SAML kliknutím na tlačítko **Upravit** otevřete dialogové okno **atributy uživatele** .

    ![Snímek obrazovky zobrazuje atributy uživatele s vybranou ikonou pro úpravy.](common/edit-attribute.png)

6. V části **deklarace identity uživatelů** v dialogu **atributy uživatele** NAKONFIGURUJTE atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Název | Zdrojový atribut|
    | ---------| --------- |
    | name  | User. extensionAttribute2 |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![Snímek obrazovky zobrazuje deklarace identity uživatelů s možností přidání nové deklarace identity.](common/new-save-attribute.png)

    ![Snímek obrazovky se zobrazí dialogové okno Spravovat deklarace identity uživatelů, kde můžete zadat hodnoty, které jsou popsány.](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut** vyberte zdroj.

    e. V seznamu **zdrojový atribut** vyberte atribut uživatele, který chcete použít pro vaši implementaci. Například pokud chcete použít ČísloZaměstnance jako jedinečný identifikátor uživatele a uloženou hodnotu atributu v ExtensionAttribute2, vyberte User. ExtensionAttribute2.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

7. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašeho požadavku a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

8. V části **Nastavení HCM Dayforce pro Ceridian** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Konfigurace Ceridian Dayforce HCM Single Sign-On

Chcete-li nakonfigurovat jednotné přihlašování na **Ceridian DAYFORCE HCM** , je třeba odeslat stažená **metadata XML** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory Ceridian Dayforce](https://www.ceridian.com/support)HCM. Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Ceridian Dayforce HCM.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **Ceridian Dayforce HCM**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **Ceridian DAYFORCE HCM**.

    ![Odkaz Ceridian Dayforce HCM v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Vytvořit Ceridian Dayforce HCM Test User

V této části vytvoříte uživatele s názvem Britta Simon v Ceridian Dayforce HCM. Pracujte s [Ceridian DAYFORCE HCM týmu podpory](https://www.ceridian.com/support) a přidejte uživatele do platformy Ceridian Dayforce HCM. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici Ceridian Dayforce HCM na přístupovém panelu, měli byste se automaticky přihlásit k DAYFORCE HCM Ceridian, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)