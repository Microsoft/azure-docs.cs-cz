---
title: 'Kurz: Azure Active Directory Integration s virtuálním prostředím ON24 s připojením SAML | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a připojením SAML virtuálního prostředí ON24.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 30fd3843b50ac6b075d33e961986b94ee2496fef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518513"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Kurz: Azure Active Directory integrace s virtuálním prostředím ON24 s připojením SAML

V tomto kurzu se naučíte integrovat připojení ON24 s virtuálním prostředím SAML pomocí Azure Active Directory (Azure AD).
Integrací připojení ON24 s virtuálním prostředím SAML se službou Azure AD získáte následující výhody:

* Můžete řídit ve službě Azure AD, která má přístup k ON24 připojení SAML k virtuálnímu prostředí.
* Můžete uživatelům povolit, aby se automaticky přihlásili k ON24 virtuálnímu prostředí (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s připojením SAML virtuálního prostředí ON24 potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné ON24 pro připojení SAML s povoleným jednotným přihlašováním ve virtuálním prostředí

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Připojení SAML pro virtuální prostředí ON24 podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Přidání připojení ON24 s virtuálním prostředím SAML z Galerie

Pokud chcete nakonfigurovat integraci připojení ON24 virtuálního prostředí SAML do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat připojení SAML virtuálního prostředí ON24 z galerie.

**Pokud chcete do galerie přidat připojení ON24 virtuálního prostředí SAML, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **ON24 připojení SAML virtuální prostředí**, vyberte **připojení SAML virtuální prostředí ON24** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Připojení SAML virtuálního prostředí ON24 v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD s připojením SAML ve virtuálním prostředí ON24 založeném na testovacím uživateli s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán odkaz na odkaz mezi uživatelem služby Azure AD a souvisejícím uživatelem v rámci připojení SAML ve virtuálním prostředí ON24.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí připojení SAML pro virtuální prostředí ON24, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurovat jednotné přihlašování pro připojení SAML virtuálního prostředí ON24](#configure-on24-virtual-environment-saml-connection-single-sign-on)** – pro konfiguraci nastavení jednoho Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele připojení SAML pro ON24 virtuální prostředí](#create-on24-virtual-environment-saml-connection-test-user)** – Chcete-li mít protějšek Britta Simon ve virtuálním prostředí, které je propojeno s reprezentací SAML uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD s připojením SAML virtuálního prostředí ON24, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace pro **připojení SAML virtuálního prostředí ON24** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Informace o jednotném přihlašování ON24 virtuálního prostředí SAML pro doménu připojení SAML a adresy URL](common/idp-relay.png)

    a. Do textového pole **identifikátor** zadejte adresu URL:

     **Adresa URL produkčního prostředí**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **Adresa URL prostředí pro kontrolu kvality**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL:

     **Adresa URL produkčního prostředí**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **Adresa URL prostředí pro kontrolu kvality**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Klikněte na **nastavit další adresy URL**. 

    d. Do textového pole **stav přenosu** zadejte adresu URL: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

5.  Pokud chcete nakonfigurovat aplikaci v režimu **SP** iniciované, proveďte následující krok:

    ![Snímek obrazovky, který zobrazuje část "nastavit další U R ls" se zvýrazněným textovým polem "přihlašovat u R L".](common/both-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://vshow.on24.com/vshow/<INSTANCENAME>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným stavem předávání a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta připojení SAML pro ON24 virtuálního prostředí](https://www.on24.com/contact-us/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **nastavení připojení SAML pro virtuální prostředí ON24** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-on24-virtual-environment-saml-connection-single-sign-on"></a>Nakonfigurovat ON24 pro připojení k virtuálnímu prostředí SAML Single Sign-On

Ke konfiguraci jednotného přihlašování na straně **připojení SAML ve virtuálním prostředí ON24** je potřeba odeslat stažený **soubor XML federačních metadat** a příslušné zkopírované adresy URL z Azure Portal do [týmu podpory připojení SAML virtuálního prostředí ON24](https://www.on24.com/about-us/support/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k připojení SAML k virtuálnímu prostředí ON24.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace** a pak vyberte možnost **připojení SAML pro virtuální prostředí ON24**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **připojení SAML ve virtuálním prostředí ON24**.

    ![Odkaz na připojení SAML virtuálního prostředí ON24 v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-on24-virtual-environment-saml-connection-test-user"></a>Vytvořit testovacího uživatele připojení SAML pro virtuální prostředí ON24

V této části vytvoříte uživatele s názvem Britta Simon ve virtuálním prostředí ON24 připojení SAML. Pokud chcete přidat uživatele v rámci platformy pro připojení SAML ve virtuálním prostředí ON24, pracujte s [týmem podpory připojení SAML s virtuálním prostředím ON24](https://www.on24.com/about-us/support/) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici připojení SAML virtuálního prostředí ON24 na přístupovém panelu byste se měli automaticky přihlášeni k připojení SAML ve virtuálním prostředí ON24, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)