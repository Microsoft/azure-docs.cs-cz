---
title: 'Kurz: Azure Active Directory integrace s ověřováním identity cloudové platformy SAP | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SAP pro ověřování identity Cloud Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: dc0cd57eb32baaeac0850337bbead3a73dec9292
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897328"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s ověřováním identity Cloud platformou SAP

V tomto kurzu se dozvíte, jak integrovat ověřování identity cloudových platforem SAP pomocí Azure Active Directory (Azure AD). Když integruje ověřování identity cloudové platformy SAP s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ověřování identity cloudové platformy SAP
* Umožněte, aby se vaši uživatelé automaticky přihlásili k ověřování identity cloudové platformy SAP pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Odběr ověřování identity cloudové platformy SAP – jednotné přihlašování (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Ověřování identity cloudové platformy SAP podporuje **aktualizace SP** a **IDP** , které iniciovaly jednotné přihlašování

Než se podrobně k technickým podrobnostem, je důležité pochopit koncepty, na které se chystáte se podívat. Ověřování identity a Active Directory Federation Services (AD FS) pro cloudovou platformu SAP umožňuje implementovat jednotné přihlašování napříč aplikacemi nebo službami, které jsou chráněné službou Azure AD (jako IdP), s aplikacemi a službami SAP chráněnými ověřováním identity cloudové platformy SAP.

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

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **ověřování identity cloudové platformy SAP** .
1. Na panelu výsledků vyberte možnost **ověřování identity cloudové platformy SAP** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro ověřování identity cloudové platformy SAP

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ověřování identity cloudové platformy SAP pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ověřování identity v cloudové platformě SAP.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ověřování identity cloudové platformy SAP, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování pro ověřování identity cloudové platformy SAP](#configure-sap-cloud-platform-identity-authentication-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit testovacího uživatele ověřování identity pro cloudovou platformu SAP](#create-sap-cloud-platform-identity-authentication-test-user)** – Chcete-li mít protějšek B. Simon v ověřování identity cloudové platformy SAP, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **ověřování identity cloudové platformy SAP** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat v režimu iniciované **IDP** proveďte následující kroky:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta podpory pro ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) . Pokud nerozumíte hodnotě identifikátoru, přečtěte si dokumentaci k ověřování identity cloudové platformy SAP týkající se [Konfigurace klienta SAML 2,0](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované pomocí **SP**:

    ![Informace o jednotném přihlašování k doméně a adresám URL ověřování identity cloudové platformy SAP](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné přihlašovací adresy URL. Použijte prosím svoji konkrétní přihlašovací adresu URL obchodní aplikace. Pokud máte nějaké pochybnosti, obraťte se na [tým podpory klienta podpory pro ověřování identity cloudové platformy SAP](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) .

1. Aplikace pro ověřování identity v cloudové platformě SAP očekává kontrolní výrazy SAML v konkrétním formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace ověřování identity cloudové platformy SAP v odpovědi SAML několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ---------------| --------------- |
    | firstName | User. křestní jméno |

8. Na stránce **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a stáhněte **XML metadat** z daných možností podle vašeho požadavku a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

9. V části **nastavení ověřování identity cloudové platformy SAP** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k ověřování identity cloudové platformy SAP.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SAP Cloud Platform ověřování identity ověřování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.

1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurace jednotného přihlašování pro ověřování identity cloudové platformy SAP

1. Pokud chcete automatizovat konfiguraci v rámci ověřování identity cloudové platformy SAP, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit ověřování identity cloudové platformy SAP** . přesměruje vás na aplikaci ověřování identity cloudové platformy SAP. Odtud zadejte přihlašovací údaje správce, které se přihlásí k ověřování identity cloudové platformy SAP. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit ověřování identity pro cloudovou platformu SAP ručně, v jiném okně webového prohlížeče přejdete do konzoly pro správu ověřování identity cloudové platformy SAP. Adresa URL má následující vzor: `https://<tenant-id>.accounts.ondemand.com/admin` . Pak si přečtěte dokumentaci týkající se ověřování identity cloudové platformy SAP při [integraci s Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. V Azure Portal klikněte na tlačítko **Uložit** .

3. Pokračujte následujícími možnostmi pouze v případě, že chcete přidat a povolit jednotné přihlašování pro jinou aplikaci SAP. Opakujte kroky uvedené v části **Přidání ověřování identity cloudové platformy SAP z Galerie**.

4. V Azure Portal na stránce integrace aplikace **ověřování identity cloudové platformy SAP** vyberte možnost **propojené přihlašování**.

    ![Konfigurace propojených Sign-On](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. Konfiguraci uložte.

> [!NOTE]
> Nová aplikace využívá konfiguraci jednotného přihlašování předchozí aplikace SAP. Ujistěte se, že používáte stejné podnikové zprostředkovatele identity v konzole pro správu ověřování identity v cloudové platformě SAP.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Vytvořit testovacího uživatele ověřování identity pro cloudovou platformu SAP

Nemusíte vytvářet uživatele v rámci ověřování identity cloudové platformy SAP. Uživatelé, kteří jsou v úložišti uživatelů Azure AD, můžou používat funkce jednotného přihlašování.

Ověřování identity cloudové platformy SAP podporuje možnost federace identit. Tato možnost umožňuje aplikaci ověřit, jestli existují uživatelé ověření podnikovým zprostředkovatelem identity v úložišti uživatelů ověřování identity cloudové platformy SAP.

Možnost federace identit je ve výchozím nastavení zakázána. Pokud je povolená federace identit, může k aplikaci přistupovat jenom uživatelé, kteří jsou importované v rámci ověřování identity cloudové platformy SAP.

Další informace o tom, jak povolit nebo zakázat federaci identity pomocí ověřování identity v cloudové platformě SAP, najdete v části "povolení federace identit pomocí ověřování identity cloudové platformy SAP" v tématu [Konfigurace federace identit s uživatelským úložištěm ověřování identity pro cloudovou platformu SAP](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k ověřování identity cloudové platformy SAP, kde můžete spustit tok přihlášení.

* Přejít na adresu URL pro ověřování identity v cloudové platformě SAP a spustit tok přihlášení přímo z tohoto účtu.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k ověřování identity cloudové platformy SAP, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici ověřování identity cloudové platformy SAP v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k ověřování identity cloudové platformy SAP, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování ověřování identity pro cloudovou platformu SAP můžete vyhovět ovládacím prvkům relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)