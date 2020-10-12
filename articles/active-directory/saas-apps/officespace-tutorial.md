---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování se softwarem OfficeSpace | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OfficeSpace softwarem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: e05a2a4ba12d5fc665090d5bf078da520000f43b
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s OfficeSpace softwarem

V tomto kurzu se dozvíte, jak integrovat software OfficeSpace s Azure Active Directory (Azure AD). Když integrujete software OfficeSpace s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k OfficeSpace softwaru.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k OfficeSpace softwaru pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné povoleného jednotného přihlašování OfficeSpace softwaru (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* OfficeSpace Software podporuje jednotné přihlašování spouštěné v **SP**


* OfficeSpace Software podporuje zřizování uživatelů **jenom v čase** .


## <a name="adding-officespace-software-from-the-gallery"></a>Přidání softwaru OfficeSpace z Galerie

Pokud chcete nakonfigurovat integraci OfficeSpace softwaru do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat OfficeSpace Software z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **OfficeSpace Software** .
1. Vyberte **OfficeSpace Software** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Konfigurace a testování jednotného přihlašování Azure AD pro OfficeSpace Software

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s OfficeSpace softwarem pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v OfficeSpace softwaru.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí OfficeSpace softwaru, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurovat jednotné přihlašování OfficeSpace softwaru](#configure-officespace-software-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvoření OfficeSpace softwaru pro testování softwaru](#create-officespace-software-test-user)** – pro uživatele, který má protějšek B. Simon v OfficeSpace softwaru, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **softwarových aplikací OfficeSpace** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. K získání těchto hodnot se obraťte na [tým podpory OfficeSpace softwaru klienta](mailto:support@officespacesoftware.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. OfficeSpace softwarová aplikace očekává kontrolní výrazy SAML v určitém formátu, které vyžadují přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**. OfficeSpace softwarová aplikace očekává, že **NameIdentifier** se namapují pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu **Upravit** a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Kromě výše uvedené OfficeSpace softwarová aplikace očekává, že se v odpovědi SAML vrátí zpátky několik atributů, které jsou uvedené dál. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Název | Zdrojový atribut|
    | ---------------| --------------- |
    | e-mail | uživatel. pošta |
    | name | User. DisplayName |
    | first_name | User. křestní jméno |
    | last_name | User. příjmení |

1. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

1. V části **podpisový certifikát SAML** zkopírujte **hodnotu kryptografického otisku** a uložte ji do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

1. V části **nastavit software OfficeSpace** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k softwaru OfficeSpace.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **OfficeSpace Software**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-officespace-software-sso"></a>Konfigurace jednotného přihlašování OfficeSpace softwaru

1. V jiném okně webového prohlížeče se přihlaste k tenantovi OfficeSpace softwaru jako správce.

2. Přejděte na **Nastavení** a klikněte na **konektory**.

    ![Snímek obrazovky, který zobrazuje rozevírací seznam nastavení s vybranými konektory](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Klikněte na **ověřování SAML**.

    ![Snímek obrazovky, který zobrazuje oddíl "ověřování" s vybranou akcí "S A M L Authentication".](./media/officespace-tutorial/tutorial_officespace_003.png)

4. V části **ověřování SAML** proveďte následující kroky:

    ![Konfigurovat jednu Sign-On na straně aplikace](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Do textového pole **Adresa URL poskytovatele odhlášení** vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL cíle IDP klienta** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Vložte hodnotu **kryptografického otisku** , kterou jste zkopírovali z Azure Portal, do textového pole **otisku certifikátu klienta IDP** . 

    d. Klikněte na **Uložit nastavení**.

### <a name="create-officespace-software-test-user"></a>Vytvořit uživatele testu softwaru OfficeSpace

V této části se v softwaru OfficeSpace vytvoří uživatel nazvaný B. Simon. OfficeSpace Software podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v OfficeSpace softwaru neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na [tým podpory OfficeSpace Software Support](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici OfficeSpace Software na přístupovém panelu, měli byste se automaticky přihlásit k softwaru OfficeSpace, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si OfficeSpace Software pomocí Azure AD](https://aad.portal.azure.com/)

