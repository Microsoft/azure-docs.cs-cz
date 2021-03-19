---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s jednotným přihlašováním AWS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a AWS jednotného přihlašování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: e890ff1cb64961c7747b8865b68504ff0a266a3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s jednotným přihlašováním AWS

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování AWS pomocí Azure Active Directory (Azure AD). Když integrujete jednotné přihlašování AWS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování AWS
* Umožněte, aby se vaši uživatelé automaticky přihlásili k AWS jednotnému přihlašování pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* AWS odběr jednotného přihlašování s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* AWS jednotné přihlašování podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

* AWS jednotné přihlašování podporuje [**automatizované zřizování uživatelů**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Přidání jednotného přihlašování AWS z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování AWS do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat AWS jednotné přihlašování z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **AWS jednotné přihlašování** .
1. Na panelu výsledků vyberte **AWS jednotné přihlašování** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD pro jednotné přihlašování AWS

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí jednotného přihlašování AWS pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v rámci jednotného přihlašování AWS vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování AWS, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. Nakonfigurujte jednotné přihlašování **[AWS jednotného přihlašování](#configure-aws-single-sign-on-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele s jednotným přihlašováním AWS](#create-aws-single-sign-on-test-user)** , abyste měli protějšek B. Simon v AWS jednotném přihlašování, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **jednotného přihlašování AWS** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb**, v základní části **Konfigurace SAML** proveďte následující kroky:

    a. Klikněte na **nahrát soubor metadat**.

    ![image1](common/upload-metadata.png)

    b. Kliknutím na **logo složky** vyberte soubor metadat a klikněte na **nahrát**.

    ![image2](common/browse-upload-metadata.png)

    c. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi získají automaticky v základní části Konfigurace SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Pokud hodnoty **adresy URL** pro **identifikátor** a odpověď nezískávají automaticky polulated, zadejte je ručně podle vašich požadavků.

1. Pokud nemáte **soubor metadat poskytovatele služeb**, proveďte následující kroky v části **základní konfigurace SAML** , pokud chcete nakonfigurovat aplikaci v režimu **IDPed** , zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory AWS jednotného přihlašování pro klienty](mailto:aws-sso-partners@amazon.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. AWS aplikace s jednotným přihlašováním očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Pokud je v rámci AWS SSO povolený ABAC, můžou se další atributy předat jako značky relací přímo do účtů AWS.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavení jednotného přihlašování AWS** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k jednotnému přihlašování AWS.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **AWS jednotné přihlašování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-aws-single-sign-on-sso"></a>Konfigurace jednotného přihlašování AWS jednotného přihlašování

1. Otevřete **konzolu AWS SSO** . 
2. V levém navigačním podokně vyberte **Nastavení**.
3. Na stránce **Nastavení** Najděte **zdroj identity** a klikněte na **změnit**.
4. Na stránce změnit adresář vyberte **externí zprostředkovatel identity**.
5. V části **metadata poskytovatele služeb** Najděte **metadata SAML pro AWS SSO** a vyberte **Stáhnout soubor metadat** a Stáhněte si soubor metadat a uložte ho do svého počítače.
6. V části **metadata poskytovatele identity** klikněte na **Procházet** a nahrajte soubor metadat, který jste stáhli z Azure Portal.
7. Klikněte na tlačítko **Další: zkontrolovat**.
8. Do textového pole zadejte **Potvrdit** a potvrďte změnu adresáře.
9. Klikněte na tlačítko **Dokončit**.

### <a name="create-aws-single-sign-on-test-user"></a>Vytvořit testovacího uživatele jednotného přihlašování AWS

1. Otevřete **konzolu AWS SSO**.

2. V levém navigačním podokně vyberte **Uživatelé**.

3. Na stránce Uživatelé klikněte na možnost **Přidat uživatele**.

4. Na stránce Přidat uživatele postupujte podle následujících kroků:

    a. Do pole **uživatelské jméno** zadejte B. Simon.

    b. Do pole **e-mailová adresa** zadejte `username@companydomain.extension` . Například, `B.Simon@contoso.com`.

    c. V poli **Potvrdit e-mailovou adresu** znovu zadejte e-mailovou adresu z předchozího kroku.

    d. Do pole jméno zadejte `Jane` .

    e. Do pole Příjmení zadejte `Doe` .

    f. Do pole Zobrazovaný název zadejte `Jane Doe` .

    například Klikněte na tlačítko **Další: skupiny**.

    > [!NOTE]
    > Ujistěte se, že uživatelské jméno zadané v AWS SSO odpovídá přihlašovacímu jménu uživatele Azure AD. To vám pomůže vyhnout se jakýmkoli problémům s ověřováním.

5. Zvolte **Přidat uživatele**.
6. V dalším kroku přiřadíte uživatele k účtu AWS. Provedete to tak, že v levém navigačním podokně konzoly AWS SSO vyberete **účty AWS**.
7. Na stránce účty AWS vyberte kartu organizace AWS, zaškrtněte políčko vedle účtu AWS, který chcete uživateli přiřadit. Pak zvolte **přiřadit uživatele**.
8. Na stránce přiřadit uživatele vyhledejte a zaškrtněte políčko vedle uživatele B. Simon. Pak zvolte **Další: sady oprávnění**.
9. V části Vybrat sady oprávnění zaškrtněte políčko vedle sady oprávnění, kterou chcete přiřadit uživateli B. Simon. Pokud nemáte existující sadu oprávnění, vyberte **vytvořit novou sadu oprávnění**.

    > [!NOTE]
    > Sady oprávnění definují úroveň přístupu uživatelů a skupin k účtu AWS. Další informace o sadách oprávnění najdete na stránce **sady oprávnění** jednotného přihlašování (AWS).
10. Klikněte na tlačítko **Dokončit**.

> [!NOTE]
> AWS jednotné přihlašování podporuje taky Automatické zřizování uživatelů. Další podrobnosti najdete [tady](./aws-single-sign-on-provisioning-tutorial.md) , jak nakonfigurovat automatické zřizování uživatelů.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na AWS adresu URL jednotného přihlašování, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení pomocí jednotného přihlašování (AWS) přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlásit k AWS jednotnému přihlašování, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici jednotného přihlašování AWS v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k AWS jednotnému přihlašování, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování jednotného přihlašování AWS můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).