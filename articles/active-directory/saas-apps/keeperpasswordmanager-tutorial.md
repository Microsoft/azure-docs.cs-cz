---
title: 'Kurz: Azure Active Directory Integration with správce hesel služby & Digital trezor | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a správcem hesel & digitálního trezoru.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: 88f84fba43959ee5e5b8d93446e4985a75697813
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685864"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Kurz: Azure Active Directory integrace s nástrojem Správce hesel & digitálního trezoru

V tomto kurzu se naučíte, jak pomocí Azure Active Directory (Azure AD) integrovat správce hesel & digitálního trezoru.
Integrací správce hesel & digitální trezor s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k nástroji Správce hesel Keep & digitálního trezoru.
* Uživatelům můžete umožnit, aby se automaticky přihlásili k programu Správce hesel & digitální trezor (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

K nakonfigurování integrace služby Azure AD pomocí & digitálního trezoru Správce úložiště, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Správce hesel a & předplatné s povoleným jednotným přihlašováním digitálního trezoru

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce hesel služby & Digital trezor podporuje jednotné přihlašování **SP** iniciované.

* Správce hesel a & digitálního trezoru podporuje **jenom čas** zřizování uživatelů

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Přidání správce hesel & digitálního trezoru z Galerie

Pokud chcete nakonfigurovat integraci Správce úložiště a & digitálního trezoru do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat & digitálního trezoru služby správce hesel.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **& Digital trezor správce hesel** .
1. Z panelu výsledků vyberte možnost **správce hesel úložiště &** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro správce hesel Keeper & digitálního trezoru

Pomocí testovacího uživatele s názvem **B. Simon** nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu Správce hesel keeper & digitálního trezoru. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji Správce hesel úložiště & digitální trezor.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí programu Správce hesel & digitálního trezoru, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.

    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.

1. **[Nakonfigurujte Správce hesel úložiště & jednotné přihlašování digitálního trezoru](#configure-keeper-password-manager--digital-vault-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    * **[Vytvořte Správce úložiště a uživatele &ho testu digitálního trezoru](#create-keeper-password-manager--digital-vault-test-user)** – abyste měli protějšek Britta Simon v nástroji Správce hesel pro úložiště & digitální trezor, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce & integrace aplikací pro **digitální trezor** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:
    * Pro **jednotné přihlašování k cloudu** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Pro **jednotné přihlašování Prem** : `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:
    * Pro **jednotné přihlašování k cloudu** : `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Pro **jednotné přihlašování Prem** : `https://<KEEPER_FQDN>/sso-connect`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:
    * Pro **jednotné přihlašování k cloudu** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Pro **jednotné přihlašování Prem** : `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí vlastního přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [správce udržování hesel & tým podpory pro klienta digitálního trezoru](https://keepersecurity.com/contact.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Správce hesel služby & digitálního trezoru očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše uvedeného platí, že aplikace Správce hesel pro & digitálního trezoru očekává, že se v odpovědi SAML vrátí další atributy, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ------------| --------- |
    | První | User. křestní jméno |
    | Poslední | User. příjmení |
    | E-mail | uživatel. pošta |

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení správce udržování hesel & digitálního trezoru** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k nástroji Správce hesel Keep & digitálního trezoru.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **správce hesel úložiště & digitální trezor**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurace Správce hesel a & jednotného přihlašování digitálního trezoru

Pokud chcete nakonfigurovat jednotné přihlašování na straně **& správce hesel pro správu digitálních trezorů** , postupujte podle pokynů uvedených v [Průvodci zajištěním podpory](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Vytvoření správce hesel a & testovací uživatel digitálního trezoru

Aby se uživatelé Azure AD mohli přihlašovat k & digitálního trezoru správců hesel a správců, musí se zřídit v programu Správce hesel Keep & Digital trezor. Aplikace podporuje zřizování uživatelů pouze v čase a po ověření budou automaticky vytvořena v aplikaci. Pokud chcete nastavit uživatele ručně, obraťte se na [podporu Keep](https://keepersecurity.com/contact.html)-Worker.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje do Správce hesel a & adresa URL pro přihlášení k digitálnímu trezoru, kde můžete spustit tok přihlášení. 

* Přejít na správce hesel Keeper & adresa URL pro přihlášení k digitálnímu trezoru přímo a zahájit tok přihlášení.

* Můžete použít panel Microsoft Access. Po kliknutí na dlaždici Správce úložiště, & na přístupovém panelu, se tato akce přesměruje na správce hesel, který & adresu URL pro přihlášení k digitálnímu trezoru. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování & digitálního trezoru pro správce hesel můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)