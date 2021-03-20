---
title: 'Kurz: Azure Active Directory integrace s inplayem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a aktérem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 3cb6ee3162c70d2d07c4868ae90ecc54bd489966
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622487"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Kurz: integrace inplay s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat inplay s Azure Active Directory (Azure AD). Když integrujete aktéra s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke službě DisplayName.
* Umožněte uživatelům, aby se automaticky přihlásili k nástroji pro inplayi pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. Inplay podporuje jednotné přihlašování (SSO) iniciované v **SP** .

## <a name="adding-displayr-from-the-gallery"></a>Přidání aktéra z Galerie

Pokud chcete nakonfigurovat integraci inplayu do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat z Galerie aktéra.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **DisplayName** .
1. Vyberte položku **aktér** z panelu výsledků a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí nástroje DisplayName pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v inplay.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí nástroje inplay, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte inplay](#configure-displayr)** , aby na straně aplikace nakonfiguroval nastavení jednotného přihlašování.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** , aby mohl Britta Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořit testovacího uživatele aktéra](#create-displayr-test-user)** , aby měl protějšek Britta Simon v aktérovi, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce **inplay** Application Integration najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující krok:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<YOURDOMAIN>.displayr.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:`<YOURDOMAIN>.displayr.com`
    
    c. Do textového pole **Adresa URL odpovědi** zadejte `https://app.displayr.com/Login/ProcessSamlResponse` .
    
    d. Klikněte na **Uložit**.

    >[!NOTE]
    >Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot se obraťte na [tým podpory pro klienty](mailto:support@displayr.com) . Můžete se také podívat na vzory uvedené v části základní konfigurace SAML v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

   ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. Aplikace aktéra očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

   ![Snímek obrazovky, který zobrazuje oddíl "atributy uživatele" se zvýrazněnou ikonou "Upravit".](common/edit-attribute.png)

1. Kromě výše očekává aplikace inplay v odpovědi SAML několik atributů, které se mají vrátit zpátky. V dialogovém okně deklarace identity v části **atributy uživatele & deklarace** v dialogu **deklarace skupiny (Preview)** proveďte následující kroky:

   a. Klikněte na **přidat deklaraci skupiny**.

      ![Snímek obrazovky, který zobrazuje okno deklarace identity (Preview) s vybraným nastavením.](./media/displayr-tutorial/config05.png)

   b. V seznamu přepínačů vyberte **všechny skupiny** .

   c. Vyberte **zdrojový atribut** **ID skupiny**.

   d. Ověřte **přizpůsobení názvu deklarace identity skupiny**.

   e. Ověřte **, že se skupiny emitují jako deklarace identity role**.

   f. Klikněte na **Uložit**.

1. V části **Nastavení inplay** zkopírujte příslušné adresy URL na základě vašeho požadavku.

   ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurovat aktéra

1. Pokud chcete v nástroji pro automatizaci nakonfigurovat konfiguraci, je nutné nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **instalační program** , který vás přesměruje do aplikace inplay. Odtud zadejte přihlašovací údaje správce pro přihlášení k inplay. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Chcete-li nastavit, aby byl aktér ručně, otevřete nové okno webového prohlížeče a přihlaste se k webovému serveru služby inplay jako správce a proveďte následující kroky:

4. Klikněte na **Nastavení** a potom přejděte na **účet**.

    ![Snímek obrazovky zobrazující, že je vybraná ikona nastavení a účet.](./media/displayr-tutorial/config01.png)

5. V horní nabídce přepněte na **Nastavení** a posuňte se dolů na stránku, kde kliknete na **Konfigurovat jednotné přihlašování (SAML)**.

    ![Snímek obrazovky zobrazující vybranou kartu nastavení a vybranou akci konfigurovat jednotné přihlašování (S A M L).](./media/displayr-tutorial/config02.png)

6. Na stránce **jednotného přihlašování (SAML)** proveďte následující kroky:

    ![Konfigurace](./media/displayr-tutorial/config03.png)

    a. Zaškrtněte pole **Povolit jednotné přihlašování (SAML)** .

    b. Zkopírujte hodnotu skutečné **identifikátoru** z části **základní konfigurace SAML** služby Azure AD a vložte ji do textového pole **vystavitele** .

    c. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **odhlašovací adresa URL** vložte hodnotu URL pro **odhlášení**, kterou jste zkopírovali z Azure Portal.

    e. V programu Poznámkový blok otevřete certifikát (Base64), zkopírujte jeho obsah a vložte ho do textového pole **certifikát** .

    f. **Mapování skupin** jsou volitelná.

    například Klikněte na **Uložit**.  

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure udělením přístupu ke službě DisplayName.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte položku **aktér**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-displayr-test-user"></a>Vytvořit testovacího uživatele aktéra

Pokud chcete povolit uživatele Azure AD, přihlaste se k nástroji inplay, musí být zřízené do služby inplay. V inplay je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k nástroji inplay jako správce.

2. Klikněte na **Nastavení** a potom přejděte na **účet**.

    ![Snímek obrazovky zobrazující ikonu nastavení (ozubeného kola) s vybraným účtem](./media/displayr-tutorial/config01.png)

3. V horní nabídce přepněte na **Nastavení** a posuňte se dolů na stránku, dokud **uživatel** neklikne na **Nový uživatel**.

    ![Snímek obrazovky, který zobrazuje kartu nastavení s zvýrazněnými uživateli a vybraným tlačítkem nový uživatel.](./media/displayr-tutorial/config07.png)

4. Na stránce **Nový uživatel** proveďte následující kroky:

    ![Konfigurace pro inplay](./media/displayr-tutorial/config06.png)

    a. Do textového pole **název** zadejte jméno uživatele, jako je **Brittasimon**.

    b. Do textového pole **e-mailu** zadejte e-maily uživatele jako `Brittasimon@contoso.com` .

    c. Vyberte příslušné **členství ve skupině**.

    d. Klikněte na **Uložit**.

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu kliknete na dlaždici vylektoru, měli byste být automaticky přihlášeni k inlektoru, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)
