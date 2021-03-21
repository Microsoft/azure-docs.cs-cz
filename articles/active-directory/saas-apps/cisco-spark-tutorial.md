---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Cisco Webex | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592519"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s Cisco Webex

V tomto kurzu se dozvíte, jak integrovat Cisco Webex s Azure Active Directory (Azure AD). Když integrujete Cisco Webex s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Cisco Webex.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Cisco Webex pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Cisco Webex jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Cisco Webex podporuje jednotné přihlašování iniciované v **SP** .
* Cisco Webex podporuje [**automatizované zřizování uživatelů**](./cisco-webex-provisioning-tutorial.md).

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie

Pokud chcete nakonfigurovat integraci Cisco Webex do služby Azure AD, musíte přidat Cisco Webex z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Cisco Webex** .
1. Na panelu výsledků vyberte **Cisco Webex** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Cisco Webex

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Cisco Webex pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Cisco Webex.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Cisco Webex, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
2. **[Nakonfigurujte Cisco Webex](#configure-cisco-webex)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte uživatele Cisco Webex Test User](#create-cisco-webex-test-user)** , který bude mít protějšek B. Simon v Cisco Webex, která je propojená s reprezentací uživatele v Azure AD.
3. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Cisco Webex** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** nahrajte stažený soubor **metadat poskytovatele služby** a nakonfigurujte aplikaci provedením následujících kroků:

    >[!Note]
    >Soubor metadat poskytovatele služeb získáte v části **Konfigurace Cisco Webex** , která je vysvětlena dále v tomto kurzu. 

    a. Klikněte na **nahrát soubor metadat**.

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    c. Po úspěšném dokončení nahrávání souboru metadat poskytovatele **služby se v** části **základní konfigurace SAML** automaticky naplní hodnoty **adresy URL pro odpovědi** .

    Do textového pole **přihlašovací adresa URL** vložte hodnotu **adresy URL odpovědi**, která se automaticky vyplní nahráním souboru s metadaty SP.

1. Aplikace Cisco Webex očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě toho aplikace Cisco Webex očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
  
    | Name |  Zdrojový atribut|
    | ---------------|--------- |
    | UID | User. userPrincipalName |

    > [!NOTE]
    >  Hodnota zdrojového atributu je ve výchozím nastavení namapována na userpricipalname. To lze změnit na User. mail nebo User. onpremiseuserprincipalname nebo na jinou hodnotu podle nastavení v WebEx.


1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/metadataxml.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Cisco Webex.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Cisco Webex**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cisco-webex"></a>Konfigurace Cisco Webex

1. Přihlaste se k Cisco Webex pomocí přihlašovacích údajů správce.

1. Vyberte **Nastavení organizace** a v části **ověřování** klikněte na **Upravit**.

    ![Snímek obrazovky zobrazuje nastavení ověřování, kde můžete vybrat možnost Upravit.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Vyberte možnost **integrace poskytovatele identity od jiného výrobce. (Rozšířené)** a klikněte na tlačítko **Další**.

    ![Snímek obrazovky ukazuje integraci poskytovatele identity od jiného výrobce.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Kliknutím na **Stáhnout soubor metadat** stáhnete **soubor metadat poskytovatele služby** a uložíte ho do svého počítače. klikněte na **Další**.

    ![Snímek obrazovky se zobrazí v souboru metadat poskytovatele služby.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Kliknutím na možnost **Prohlížeč souborů** vyhledejte a nahrajte soubor metadat služby Azure AD. Pak vyberte **vyžadovat certifikát podepsaný certifikační autoritou v metadatech (bezpečnější)** a klikněte na **Další**.

    ![Snímek obrazovky ukazuje stránku metadat import I d P.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Vyberte **Test připojení SSO** a když se otevře nová karta prohlížeče, proveďte přihlášení pomocí služby Azure AD.

1. Vraťte se na kartu prohlížeče **správy pro spolupráci Cisco Cloud Collaboration** . Pokud byl test úspěšný, vyberte **Tento test byl úspěšný. Povolte možnost jedna Sign-On** a klikněte na tlačítko **Další**.

1. Klikněte na **Uložit**.

> [!NOTE]
> Další informace o tom, jak nakonfigurovat Cisco Webex, najdete na [této](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) stránce.

### <a name="create-cisco-webex-test-user"></a>Vytvořit uživatele Cisco Webex Test User

V této části se v Cisco Webex vytvoří uživatel nazvaný B. Simon. Tato aplikace podporuje automatické zřizování uživatelů, které umožňuje automatické zřizování a rušení zřizování na základě vašich obchodních pravidel.  Microsoft doporučuje používat Automatické zřizování, kdykoli to bude možné. Podívejte se, jak povolit Automatické zřizování pro [Cisco Webex](./cisco-webex-provisioning-tutorial.md).

Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:

1. Přihlaste se k Cisco Webex pomocí přihlašovacích údajů správce.

2. Klikněte na **Uživatelé** a pak na **Spravovat uživatele**.
   
    ![Snímek obrazovky se zobrazí stránka uživatelé, kde můžete spravovat uživatele.](./media/cisco-spark-tutorial/user-1.png) 

3. V okně **Spravovat uživatele** vyberte **ručně přidat nebo upravit uživatele**.

    ![Snímek obrazovky se zobrazí stránka uživatelé, kde můžete spravovat uživatele a vybrat možnost ručně přidat nebo upravit uživatele.](./media/cisco-spark-tutorial/user-2.png)

4. Vyberte **jména a e-mailovou adresu**. Pak vyplňte textové pole následujícím způsobem:

    ![Snímek obrazovky se zobrazí v dialogovém okně Spravovat weby Users, kde můžete ručně přidávat nebo upravovat uživatele.](./media/cisco-spark-tutorial/user-3.png) 

    a. Do textového pole **název** zadejte jméno uživatele jako **B**.

    b. Do textového pole **příjmení** zadejte jméno příjmení uživatele, jako je **Simon**.

    c. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele b.simon@contoso.com .

5. Klikněte na znaménko plus a přidejte B. Simon. Pak klikněte na tlačítko **Další**.

6. V okně **Přidat služby pro uživatele** klikněte na **Přidat uživatele** a potom na **Dokončit**.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení Cisco Webex, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k Cisco Webex přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Cisco Webex v části Moje aplikace, přesměruje se na adresu URL pro přihlášení Cisco Webex. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Cisco Webex můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)