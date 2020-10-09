---
title: 'Kurz: Azure Active Directory integrace s iLMS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 03b8110db94a08f44035e75371fd7641fcd91626
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91826355"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Kurz: integrace iLMS s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat iLMS s Azure Active Directory (Azure AD). Když integrujete iLMS s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k iLMS.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k iLMS svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* iLMS odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí. iLMS podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-ilms-from-the-gallery"></a>Přidání iLMS z Galerie

Pokud chcete nakonfigurovat integraci iLMS do služby Azure AD, musíte přidat iLMS z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **iLMS** .
1. Na panelu výsledků vyberte **iLMS** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí iLMS pomocí testovacího uživatele s názvem **Britta Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v iLMS.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí iLMS, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte ILMS SSO](#configure-ilms-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření iLMS Test User](#create-ilms-test-user)** – pro Britta Simon v iLMS, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **ILMS** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte na stránce **základní konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** vložte hodnotu **identifikátoru** , kterou jste zkopírovali z části **poskytovatel služeb** v nastavení SAML na portálu pro správu iLMS.

    b. Do textového pole **Adresa URL odpovědi** vložte hodnotu **koncového bodu (URL)** , který kopírujete z části **poskytovatel služeb** nastavení SAML na portálu pro správu iLMS, který má následující vzor. `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **koncového bodu (URL)** , kterou kopírujete z oddílu **Service Provider** v nastavení SAML na portálu pro správu iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Pro povolení zřizování JIT očekává vaše aplikace iLMS kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    > [!NOTE]
    > Pro namapování těchto atributů musíte povolit možnost **vytvořit nerozpoznaný uživatelský účet** v iLMS. Pokud chcete získat představu o konfiguraci atributů, postupujte podle pokynů uvedených [tady](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) .

1. Kromě výše očekává aplikace iLMS několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Název | Zdrojový atribut|
    | --------|------------- |
    | dělení | User. Department |
    | oblast | User. State |
    | Oddělení | User. jobtitle |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    c. Ponechte **obor názvů** prázdný.

    d. Jako **atribut**vyberte zdroj.

    e. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    f. Klikněte na **OK** .

    například Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení iLMS** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-ilms-sso"></a>Konfigurace jednotného přihlašování iLMS

1. V jiném okně webového prohlížeče se přihlaste k **portálu pro správu iLMS** jako správce.

2. Klikněte na **SSO: SAML** na kartě **Nastavení** otevřete nastavení SAML a proveďte následující kroky:

    ![Snímek obrazovky zobrazuje kartu nastavení I L M, kde můžete vybrat S S: SAML.](./media/ilms-tutorial/1.png)

3. Rozbalte část **poskytovatel služeb** a zkopírujte hodnotu **identifikátor** a **koncový bod (URL)** .

    ![Snímek obrazovky zobrazuje nastavení SAML, kde můžete získat hodnoty.](./media/ilms-tutorial/2.png) 

4. V části **zprostředkovatel identity** klikněte na **importovat metadata**.

5. Vyberte soubor **federačních metadat** stažený z Azure Portal v části **podpisový certifikát SAML** .

    ![Snímek obrazovky ukazuje nastavení SAML, kde můžete vybrat soubor metadat.](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Pokud chcete povolit zřizování JIT pro vytváření účtů iLMS pro zrušení rozpoznávání uživatelů, postupujte podle následujících kroků:

    a. Ověřte **Vytvoření nerozpoznaného uživatelského účtu**.

    ![Snímek obrazovky ukazuje možnost vytvořit nerozpoznaný uživatelský účet.](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Namapujte atributy v Azure AD pomocí atributů v iLMS. Ve sloupci atribut zadejte název atributů nebo výchozí hodnotu.

    c. Přejít na kartu **obchodní pravidla** a proveďte následující kroky:

    ![Snímek obrazovky zobrazuje nastavení obchodních pravidel, kde můžete zadat informace v tomto kroku.](./media/ilms-tutorial/5.png)

    d. Pokud chcete vytvořit oblasti, divize a oddělení, které ještě neexistují v době jednotného přihlašování, Projděte si část **Vytvoření nerozpoznaných oblastí, divizí a oddělení** .

    e. Pomocí možnosti **aktualizovat profil uživatele během přihlašování** určete, jestli se má pro každé jednotné přihlašování aktualizovat profil uživatele.

    f. Pokud je zaškrtnutá možnost **aktualizovat prázdné hodnoty pro povinná pole v profilu uživatele** , způsobí to, že při přihlášení bude profil iLMS uživatele obsahovat prázdné hodnoty pro tato pole.

    například Zaregistrujte **e-mail s oznámením o chybách** a zadejte e-mail uživatele, u kterého chcete dostávat e-mailové oznámení o chybách.

7. Uložte nastavení kliknutím na tlačítko **Uložit** .

    ![Snímek obrazovky se zobrazením tlačítka Uložit.](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `Britta Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `BrittaSimon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Create** (Vytvořit).

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k iLMS.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **iLMS**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-ilms-test-user"></a>Vytvořit testovacího uživatele iLMS

Aplikace podporuje zřizování uživatelů pouze v čase a po ověření, že se uživatelé budou automaticky vytvářet v aplikaci. JIT bude fungovat, pokud jste klikli na zaškrtávací políčko **vytvořit nerozpoznaný uživatelský účet** během nastavení konfigurace SAML na portálu pro správu iLMS.

Pokud potřebujete ručně vytvořit uživatele, postupujte podle následujících kroků:

1. Přihlaste se k webu iLMS společnosti jako správce.

2. Kliknutím na **Registrovat uživatele** na kartě **Uživatelé** otevřete stránku **zaregistrovat uživatele** .

   ![Snímek obrazovky se zobrazí na kartě nastavení I L M, kde můžete vybrat zaregistrovat uživatele.](./media/ilms-tutorial/3.png)

3. Na stránce **zaregistrovat uživatele** proveďte následující kroky.

    ![Snímek obrazovky se zobrazí stránka registrace uživatele, kde zadáte zadané informace.](./media/ilms-tutorial/create_testuser_add.png)

    a. Do textového pole **název** zadejte jméno, například Britta.

    b. Do textového pole **příjmení** zadejte příjmení, jako je Simon.

    c. Do textového pole **ID e-mailu** zadejte e-mailovou adresu uživatele BrittaSimon@contoso.com .

    d. V rozevíracím seznamu **oblast** vyberte hodnotu pro oblast.

    e. V rozevíracím seznamu **dělení** vyberte hodnotu pro dělení.

    f. V rozevíracím seznamu **oddělení** vyberte hodnotu pro oddělení.

    například Klikněte na **Uložit**.

    > [!NOTE]
    > Registrační e-maily můžete odeslat uživateli zaškrtnutím políčka **Odeslat registrační poštu** .

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici iLMS, měli byste se automaticky přihlásit k iLMS, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
