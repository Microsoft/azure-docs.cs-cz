---
title: 'Kurz: Azure Active Directory integrace s ověřováním identity cloudové platformy SAP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP pro ověřování identity Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 196de1cacb977305c778e16182b21c8dcfafe13e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161170"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Kurz: Azure Active Directory integrace s ověřováním identity cloudové platformy SAP

V tomto kurzu se dozvíte, jak integrovat ověřování identity cloudových platforem SAP pomocí Azure Active Directory (Azure AD).
Integrací ověřování identity cloudové platformy SAP pomocí Azure AD získáte následující výhody:

* Můžete řídit v Azure AD, který má přístup k ověřování identity cloudové platformy SAP.
* Uživatelům můžete povolit, aby se automaticky přihlásili k ověřování identity cloudové platformy SAP (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s ověřováním identity cloudové platformy SAP potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné SAP Cloud Platform ověřování identity s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Ověřování identity cloudové platformy SAP podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování

Než se podrobně k technickým podrobnostem, je důležité pochopit koncepty, na které se chystáte se podívat. Ověřování identity a Active Directory Federation Services (AD FS) pro cloudovou platformu SAP vám umožní implementovat jednotné přihlašování napříč aplikacemi nebo službami, které jsou chráněné službou Azure AD (jako IdP) s aplikacemi a službami SAP chráněnými pomocí cloudu SAP. Ověřování identity platformy.

V současné době ověřování identity cloudové platformy SAP funguje jako zprostředkovatel identity proxy pro aplikace SAP. Azure Active Directory v této instalaci slouží jako vedoucí poskytovatel identity. 

Tento vztah znázorňuje následující diagram:

![Vytvoření testovacího uživatele Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

V rámci této instalace je tenant ověřování identity cloudové platformy SAP nakonfigurovaný jako důvěryhodná aplikace v Azure Active Directory.

Všechny aplikace a služby SAP, které chcete chránit tímto způsobem, se následně nakonfigurují v konzole pro správu ověřování identity v cloudové platformě SAP.

Proto musí být autorizace pro udělení přístupu k aplikacím a službám SAP prováděna při ověřování identity cloudové platformy SAP (na rozdíl od Azure Active Directory).

Konfigurací ověřování identity cloudové platformy SAP jako aplikace prostřednictvím služby Azure Active Directory Marketplace nemusíte konfigurovat jednotlivé deklarace identity nebo kontrolní výrazy SAML.

> [!NOTE]
> Obě strany právě testovaly jenom jednotné přihlašování k webu. Toky, které jsou nezbytné pro komunikaci z App-to-API nebo API-to-API, by měly fungovat, ale ještě nebyly testovány. Budou testovány během následných aktivit.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Přidání ověřování identity cloudové platformy SAP z Galerie

Pokud chcete nakonfigurovat integraci ověřování identity cloudové platformy SAP do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ověřování identity cloudové platformy SAP z galerie.

**Pokud chcete přidat ověřování identity cloudové platformy SAP z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **SAP Cloud Platform ověřování identity**, na panelu výsledků vyberte **SAP Cloud Platform ověřování identity** a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Ověřování identity cloudové platformy SAP v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí [název aplikace] na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí být navázán vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v [název aplikace].

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí [název aplikace], musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování ověřování identity cloudové platformy SAP](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele ověřování identity pro cloudovou platformu SAP](#create-sap-cloud-platform-identity-authentication-test-user)** – Pokud chcete mít protějšek Britta Simon v ověřování identity cloudové platformy SAP, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí [název aplikace], proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ověřování identity cloudové platformy SAP** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat v režimu **IDP** intiated, proveďte následující kroky:

    ![Informace o jednotném přihlašování k doméně a adresám URL ověřování identity cloudové platformy SAP](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory pro ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) . Pokud nerozumíte hodnotě identifikátoru, přečtěte si dokumentaci k ověřování identity cloudové platformy SAP týkající se [Konfigurace klienta SAML 2,0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Informace o jednotném přihlašování k doméně a adresám URL ověřování identity cloudové platformy SAP](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Použijte prosím svoji konkrétní přihlašovací adresu URL obchodní aplikace. Pokud máte nějaké pochybnosti, obraťte se na [tým podpory klienta podpory pro ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) .

6. Aplikace pro ověřování identity v cloudové platformě SAP očekává kontrolní výrazy SAML v určitém formátu. Pro tuto aplikaci nakonfigurujte následující deklarace identity. Hodnoty těchto atributů můžete spravovat z oddílu **atributy uživatele** na stránce integrace aplikací. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na tlačítko **Upravit** a otevřete dialog **uživatelské atributy** .

    ![image](common/edit-attribute.png)

7. Pokud vaše aplikace SAP očekává atribut jako **FirstName**, přidejte atribut **FirstName** v oddílu **deklarace identity uživatelů** v dialogu **atributy uživatele** , nakonfigurujte atribut tokenu SAML, jak je znázorněno na obrázku výše, a proveďte následující kroky:

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu FirstName.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** vyberte hodnotu atributu User. křestní jméno.

    f. Klikněte na **OK** .

    g. Klikněte na **Uložit**.

8. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

9. V části **nastavení ověřování identity cloudové platformy SAP** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Konfigurace jednotného přihlašování pro ověřování identity v cloudové platformě SAP

1. Pokud chcete pro vaši aplikaci nakonfigurovat jednotné přihlašování, přečtěte si v konzole pro správu ověřování identity cloudové platformy SAP. Adresa URL má následující vzor: `https://<tenant-id>.accounts.ondemand.com/admin`. Pak si přečtěte dokumentaci týkající se ověřování identity cloudové platformy SAP při [integraci s Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. V Azure Portal klikněte na tlačítko **Uložit** .

3. Pokračujte následujícími možnostmi pouze v případě, že chcete přidat a povolit jednotné přihlašování pro jinou aplikaci SAP. Opakujte kroky uvedené v části **Přidání ověřování identity cloudové platformy SAP z Galerie**.

4. V Azure Portal na stránce integrace aplikace **ověřování identity cloudové platformy SAP** vyberte možnost **propojené přihlašování**.

    ![Konfigurace propojeného přihlašování](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Konfiguraci uložte.

> [!NOTE]
> Nová aplikace využívá konfiguraci jednotného přihlašování předchozí aplikace SAP. Ujistěte se, že používáte stejné podnikové zprostředkovatele identity v konzole pro správu ověřování identity v cloudové platformě SAP.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon\@yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k ověřování identity cloudové platformy SAP.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **SAP Cloud Platform ověřování identity**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **SAP Cloud Platform ověřování identity ověřování**.

    ![Odkaz na ověřování identity cloudové platformy SAP v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Vytvořit testovacího uživatele ověřování identity pro cloudovou platformu SAP

Nemusíte vytvářet uživatele v rámci ověřování identity cloudové platformy SAP. Uživatelé, kteří jsou v úložišti uživatelů Azure AD, můžou používat funkce jednotného přihlašování.

Ověřování identity cloudové platformy SAP podporuje možnost federace identit. Tato možnost umožňuje aplikaci ověřit, jestli existují uživatelé ověření podnikovým zprostředkovatelem identity v úložišti uživatelů ověřování identity cloudové platformy SAP.

Možnost federace identit je ve výchozím nastavení zakázána. Pokud je povolená federace identit, může k aplikaci přistupovat jenom uživatelé, kteří jsou importované v rámci ověřování identity cloudové platformy SAP.

Další informace o tom, jak povolit nebo zakázat federaci identity pomocí ověřování identity cloudové platformy SAP, najdete v části povolení federace identit s ověřováním identity cloudové platformy SAP v tématu [Konfigurace federace identit s úložištěm uživatele. Ověřování identity cloudové platformy SAP](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici ověřování identity cloudové platformy SAP na přístupovém panelu byste měli být automaticky přihlášeni k ověřování identity cloudové platformy SAP, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
