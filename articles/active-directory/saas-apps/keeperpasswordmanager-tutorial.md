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
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96178017"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Kurz: Azure Active Directory integrace s nástrojem Správce hesel & digitálního trezoru

V tomto kurzu se naučíte, jak pomocí Azure Active Directory (Azure AD) integrovat správce hesel & digitálního trezoru.
Tato integrace poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k nástroji Správce hesel Keep & digitálního trezoru.
* Uživatelům můžete umožnit, aby se automaticky přihlásili k programu Správce hesel a & digitálního trezoru (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění: Azure Portal.


## <a name="prerequisites"></a>Předpoklady

K nakonfigurování integrace služby Azure AD pomocí & digitálního trezoru Správce úložiště, potřebujete tyto kroky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční [zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Správce hesel a & předplatné digitálního trezoru, povolené pro jednotné přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce hesel služby & Digital trezor podporuje jednotné přihlašování iniciované SP.

* Správce hesel a & digitálního trezoru podporuje zřizování uživatelů za běhu.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Přidání správce hesel & digitálního trezoru z Galerie

Pokud chcete nakonfigurovat integraci Správce úložiště & digitálního trezoru do Azure AD, přidejte aplikaci z Galerie do seznamu spravovaných aplikací SaaS (software jako služba).

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **& Digital trezor správce hesel** .
1. Z panelu výsledků vyberte možnost **správce hesel úložiště &** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro správce hesel Keeper & digitálního trezoru

Pomocí testovacího uživatele s názvem **B. Simon** nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu Správce hesel keeper & digitálního trezoru. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v nástroji Správce hesel úložiště & digitální trezor.

Postup konfigurace a testování jednotného přihlašování služby Azure AD pomocí Správce hesel & digitálního trezoru nástroje Keeper:

1. [NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso) , aby vaši uživatelé mohli používat tuto funkci.

    * [Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user) pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
    * [Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user) , aby mohl Britta Simon používat jednotné přihlašování Azure AD.

1. [Nakonfigurujte Správce udržování hesel & jednotného přihlašování digitálního trezoru](#configure-keeper-password-manager--digital-vault-sso) pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * [Vytvořte správce uživatelských hesel & testovací uživatel s digitálním trezorem](#create-a-keeper-password-manager--digital-vault-test-user) , aby měl protějšek Britta Simon v programu Správce hesel úložiště & digitální trezor propojený se zastoupením uživatele Azure AD.
1. [Otestujte jednotné přihlašování](#test-sso) a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce & pro integraci aplikací pro **digitální trezor** najděte část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Snímek obrazovky s nastavením jednoduchého Sign-On s SAML se zvýrazněnou ikonou tužky](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do pole **Adresa URL pro přihlášení** zadejte adresu URL, která používá následující vzor:
    * Pro jednotné přihlašování k cloudu: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Pro místní jednotné přihlašování: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Do pole **identifikátor (ID entity)** zadejte adresu URL, která používá následující vzor:
    * Pro jednotné přihlašování k cloudu: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Pro místní jednotné přihlašování: `https://<KEEPER_FQDN>/sso-connect`

    c. V poli **Adresa URL odpovědi** zadejte adresu URL, která používá následující vzor:
    * Pro jednotné přihlašování k cloudu: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Pro místní jednotné přihlašování: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory pro správce úložiště keep &](https://keepersecurity.com/contact.html). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Aplikace Správce hesel služby & digitálního trezoru očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![Snímek obrazovky atributů uživatele & deklarací identity.](common/default-attributes.png)

1. Kromě toho správce hesel Keep & aplikace digitálního trezoru očekává, že se v odpovědi SAML vrátí několik dalších atributů. Tyto jsou uvedeny v následující tabulce. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle svých požadavků.

    | Název | Zdrojový atribut|
    | ------------| --------- |
    | První | User. křestní jméno |
    | Poslední | User. příjmení |
    | E-mail | uživatel. pošta |

5. V části **nastavit jednu Sign-On s SAML** v části **podpisový certifikát SAML** vyberte **Stáhnout**. Tím se stáhnou **XML federačních metadat** z možností podle vašich požadavků a uloží se do vašeho počítače.

    ![Snímek pro podpisový certifikát SAML se zvýrazněnou možností stažení](common/metadataxml.png)

6. V části **nastavit správce hesel & digitálního trezoru** zkopírujte příslušné adresy URL podle vašeho požadavku.

    ![Snímek obrazovky s nastavením & digitálního trezoru pro správu hesel v programu a zvýrazněných adres URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal volána `B.Simon` .

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Jako **název** zadejte `B.Simon` .  
   1. Do pole **uživatelské jméno** zadejte `username@companydomain.extension` . Například, `B.Simon@contoso.com`.
   1. Vyberte možnost **Zobrazit heslo** a potom zapište zobrazenou hodnotu.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k nástroji Správce hesel Keep & digitálního trezoru.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **správce hesel úložiště & digitální trezor**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. V **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V části **Uživatelé a skupiny** vyberte v seznamu uživatelů možnost **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat ze seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, vybere se **výchozí role přístupu** .
1. V **Přidat přiřazení** vyberte **přiřadit**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurace Správce hesel a & jednotného přihlašování digitálního trezoru

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, přečtěte si pokyny v [příručce pro podporu Keeping](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Vytvoření správce & správce hesel testovací uživatel digitálního trezoru

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k programu Správce hesel & digitálnímu trezoru, musíte je zřídit. Aplikace podporuje zřizování uživatelů za běhu a poté, co se uživatelé ověřování v aplikaci automaticky vytvoří. Pokud chcete nastavit uživatele ručně, obraťte se na [podporu Keep](https://keepersecurity.com/contact.html)on.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* V Azure Portal vyberte možnost **testovat tuto aplikaci**. Tím se přesměruje na přihlašovací adresu URL pro správce úložiště Keep-and & Digital trezor, kde můžete zahájit přihlašování. 

* Můžete přejít přímo na přihlašovací adresu URL pro aplikaci a zahájit přihlášení.

* Můžete použít panel Microsoft Access. Když na přístupovém panelu kliknete na dlaždici **& správce hesel** , na které se odkazuje na přihlašovací adresu pro aplikaci. Další informace o přístupovém panelu najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování & digitálního trezoru Správce úložiště a hesla můžete vykonat řízení relace. Tím se chrání exfiltrace a infiltrace citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. Další informace najdete v tématu [vymáhání řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).