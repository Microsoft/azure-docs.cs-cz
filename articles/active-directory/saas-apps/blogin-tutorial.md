---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s BlogIn | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a BlogIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: jeedes
ms.openlocfilehash: a47a3ae27fd1a18b7e9acd7d8b25748f6274c3e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456989"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s BlogIn

V tomto kurzu se dozvíte, jak integrovat BlogIn s Azure Active Directory (Azure AD). Když integrujete BlogIn s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k BlogIn.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k BlogIn svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* BlogIn odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* BlogIn podporuje jednotné přihlašování (SSO) **a IDP** .
* BlogIn podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování BlogIn můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-blogin-from-the-gallery"></a>Přidání BlogIn z Galerie

Pokud chcete nakonfigurovat integraci BlogIn do služby Azure AD, musíte přidat BlogIn z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **BlogIn** .
1. Na panelu výsledků vyberte **BlogIn** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>Konfigurace a testování jednotného přihlašování Azure AD pro BlogIn

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí BlogIn pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v BlogIn.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí BlogIn, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte BLOGIN SSO](#configure-blogin-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte BlogIn Test User](#create-blogin-test-user)** -to, abyste měli protějšek B. Simon v BlogIn, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **BlogIn** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu, který je v **IDP** inicializovaném, zadejte v části **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.blogin.co/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.blogin.co/sso/saml/callback`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<SUBDOMAIN>.blogin.co/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Přesné hodnoty pro tato pole můžete získat na stránce **Nastavení** na BlogIn (karta **uživatelské ověřování** > **nakonfigurovat jednotné přihlašování a zřizování uživatelů**). Případně můžete kontaktovat [tým podpory klienta BlogIn](mailto:support@blogin.co) , aby tyto hodnoty získal. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. BlogIn aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho očekává aplikace BlogIn několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name | Zdrojový atribut |
    | ------ | --------- |
    | title |User. jobtitle |
    

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k BlogIn.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **BlogIn**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-blogin-sso"></a>Konfigurace jednotného přihlašování BlogIn

Pokud chcete nakonfigurovat jednotné přihlašování při přihlášení na straně **BlogIn** k vašemu účtu BlogIn, postupujte takto:

1. Přejít na **Nastavení**  >  **ověřování uživatelů**  >  **Konfigurace jednotného přihlašování & zřizování uživatelů**.
2. Na další obrazovce změňte stav jednoho Sign-On na **zapnuto** a jako tlačítko pro přihlášení SSO klikněte na vlastní název, který se zobrazí na přihlašovací obrazovce.

3. Pokud jste v posledním kroku předchozí části uložili **adresu URL federačních metadat aplikace** , vyberte **adresu URL metadat** metody konfigurace a vložte **adresu URL federačních METADAT aplikace** do pole Adresa URL metadat. Jinak změňte metodu konfigurace na **Ruční**, ručně naplňte **adresu URL jednotného přihlašování zprostředkovatele identity (přihlašovací adresu URL)** a **vystavitele zprostředkovatele identity (ID entity)** a nahrajte **certifikát (Base64)** , který jste získali ze služby Azure AD.

4. Vyberte výchozí roli uživatele pro nové uživatele připojující se k BlogIn pomocí jednotného přihlašování.

5. Vyberte **Uložit změny**.

Podrobnější vysvětlení nastavení jednotného přihlašování v BlogIn najdete v tématu [jak nastavit jednotné přihlašování pro Microsoft Azure AD v BlogIn](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/). Pokud máte nějaké dotazy nebo potřebujete pomoc, obraťte se na [tým podpory BlogIn](mailto:support@blogin.co) kdykoli.

### <a name="create-blogin-test-user"></a>Vytvořit testovacího uživatele BlogIn

V této části se v BlogIn vytvoří uživatel s názvem B. Simon. BlogIn podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v BlogIn neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici BlogIn, měli byste se automaticky přihlásit k BlogIn, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si BlogIn s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit BlogIn pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)