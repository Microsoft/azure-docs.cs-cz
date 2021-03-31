---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s jedním Sign-On HRworks | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a HRworks jednotného přihlašování.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: d6b23213d9d986c62a227b3e182a22896d128222
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s jedním Sign-On HRworks

V tomto kurzu se dozvíte, jak integrovat HRworks jednu Sign-On s Azure Active Directory (Azure AD). Když integrujete HRworks jednu Sign-On s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k jednotnému přihlašování HRworks
* Umožněte, aby se vaši uživatelé automaticky přihlásili k HRworks jednomu Sign-On s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* HRworks jednotné přihlašování (SSO) s povoleným jedním Sign-On.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* HRworks Single Sign-On podporuje jednotné přihlašování spouštěné v **SP**

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Přidání HRworks jednoho Sign-On z Galerie

Pokud chcete nakonfigurovat integraci HRworks jednoho Sign-On do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat HRworks jednu Sign-On z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **HRworks jednotné přihlašování** .
1. Na panelu výsledků vyberte **HRworks jednotné přihlašování** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD pro HRworks Single Sign-On

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí HRworks s jedním Sign-On pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v rámci jednotného přihlašování HRworks vytvořit vztah propojení mezi uživatelem Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování HRworks, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte HRworks Sign-On jednotné přihlašování](#configure-hrworks-single-sign-on-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte HRworks jednoho Sign-On testovacího uživatele](#create-hrworks-single-sign-on-test-user)** – abyste měli protějšek B. Simon v HRworks jedné Sign-On, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **jednotného přihlašování HRworks** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou Sign-On. Pokud chcete získat hodnotu, obraťte se na [tým podpory HRworks jednoho Sign-On](mailto:nadja.sommerfeld@hrworks.de) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení jednotného přihlašování HRworks** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k jednotnému přihlašování HRworks.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **HRworks jednotné přihlašování**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-hrworks-single-sign-on-sso"></a>Konfigurace jednotného Sign-On jednotného přihlašování HRworks

1. Pokud chcete automatizovat konfiguraci v rámci jednotného přihlašování HRworks, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

1. Po přidání rozšíření do prohlížeče se kliknutím na **nastavit HRworks jednotné přihlašování** směrujete na aplikaci HRworks Single Sign-On. Odtud zadejte přihlašovací údaje správce pro přihlášení k HRworks jednotnému přihlašování. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-4.

    ![Konfigurace instalace](common/setup-sso.png)

1. Pokud chcete ručně nastavit HRworks jednu Sign-On, otevřete nové okno webového prohlížeče a přihlaste se k vaší HRworks podnikovému webu Sign-On jako správce a proveďte následující kroky:

1. Klikněte na   >  **základní informace** o  >  **zabezpečení**  >  **jednotného přihlašování** na levé straně řádku nabídek a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Zaškrtněte políčko **použít jednotné přihlašování** .

    b. Jako **metodu vstupu meta data** vyberte **metadata XML** .

    c. Jako **hodnotu pro NameId** vyberte **jednotlivý identifikátor NameId** .

    d. V programu Poznámkový blok otevřete XML metadat, které jste stáhli z Azure Portal, zkopírujte jeho obsah a vložte ho do textového pole **metadata** .

    e. Klikněte na **Uložit**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Vytvořit HRworks jednoho Sign-On testovacího uživatele

Pokud chcete povolit uživatele Azure AD, přihlaste se k HRworks jednotnému přihlašování, musí se zřídit do HRworks jednotného přihlašování. Při jednotném přihlašování HRworks je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k HRworks jednomu Sign-On jako správce.

1. V nabídce na  >    >    >  levé straně řádku nabídek klikněte na osoby s oprávněními správce **nová osoba** .

     ![Snímek obrazovky znázorňuje stránku práce v jazyce H R s vybranými osobami a novými osobami.](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. V automaticky otevíraném okně klikněte na **Další**.

    ![Snímek obrazovky zobrazuje seznam zemí, ze kterých si můžete pro osobu vybrat.](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. V místní nabídce **vytvořit novou osobu s právními podmínkami** vyplňte příslušné údaje jako **křestní jméno**, **příjmení** a klikněte na **vytvořit**.

    ![Snímek obrazovky s textovými poli, kde můžete zadat jména a příjmení pro osobu.](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Sign-On HRworks, měli byste se automaticky přihlásit k HRworks Sign-On, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte HRworks jednu Sign-On s Azure AD](https://aad.portal.azure.com/)