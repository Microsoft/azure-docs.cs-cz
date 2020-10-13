---
title: 'Kurz: Azure Active Directory integrace se službou Amazon Business | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Amazon Business.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: 659cca6979a8d8be7d12c49fe01a9d0a5d7ac58e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91713754"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Kurz: integrace Amazon Business s Azure Active Directory

V tomto kurzu se naučíte integrovat organizaci Amazon Business pomocí Azure Active Directory (Azure AD). Když integrujete [organizaci Amazon Business](https://www.amazon.com/b2b/info/amazon-business?layout=landing) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k organizaci Amazon Business.
* Umožněte uživatelům, aby se přihlásili k organizaci Amazon Business s jejich účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat zdarma [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Předplatné Amazon Business Single Signing (SSO) s podporou jednotného přihlašování. Pokud chcete vytvořit obchodní účet Amazon, navštivte stránku [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) .

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v existujícím obchodním účtu Amazon.

* Amazon Business podporuje **aktualizace SP a IDP** iniciované jednotného přihlašování.
* Amazon Business podporuje zřizování uživatelů **jenom v čase**

## <a name="adding-amazon-business-from-the-gallery"></a>Přidává se společnost Amazon Business z galerie.

Pokud chcete nakonfigurovat integraci Amazon Business do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat organizaci Amazon Business z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Amazon Business** .
1. Z panelu výsledků vyberte **Amazon Business** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Amazon Business pomocí testovacího uživatele s názvem **B. Simon**.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Amazon Business, proveďte následující kroky sestavení:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
2. **[Nakonfigurujte Amazon Business SSO](#configure-amazon-business-sso)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
5. **[Vytvořte uživatele Amazon Business Test User](#create-amazon-business-test-user)** – abyste měli protějšek B. Simon v Amazon Business, která je propojená s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **Amazon Business** Application Integration, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat v režimu iniciované **IDP** , proveďte následující kroky:

    1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí jednoho z následujících vzorů:
    
       | URL | Oblast |
       |-|-|
       | `https://www.amazon.com`| Severní Amerika |
       | `https://www.amazon.co.jp`| Východní Asie |
       | `https://www.amazon.de`| Evropa |

    1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:
    
       | URL | Oblast |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Severní Amerika |
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Východní Asie |
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`| Evropa |

       > [!NOTE]
       > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. `<idpid>`Hodnotu získáte z oddílu Amazon Business SSO Configuration, který je vysvětlen dále v tomto kurzu. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Pokud chcete nakonfigurovat aplikaci v režimu **SP** iniciované, budete muset do **přihlašovací adresy URL** v části **nastavit další adresy** URL přidat úplnou adresu URL poskytnutou v konfiguraci Amazon Business.

1. Následující snímek obrazovky ukazuje seznam výchozích atributů. Upravte atributy kliknutím na ikonu **Upravit** v části **atributy uživatele & deklarace identity** .

    ![Snímek obrazovky ukazuje atributy uživatele & deklarací s výchozími hodnotami, jako je například daný uživatel. jméno a EmailAddress User. mail.](media/amazon-business-tutorial/map-attribute3.png)

1. Upravte atributy a zkopírujte hodnotu **oboru názvů** těchto atributů do poznámkového bloku.

    ![Snímek obrazovky zobrazuje atributy uživatele & deklarace identity se sloupci pro název a hodnotu deklarace identity.](media/amazon-business-tutorial/map-attribute4.png)

1. Kromě toho aplikace Amazon Business Application očekává, že se v odpovědi SAML zpátky vrátí několik atributů. V části **atributy uživatele & deklarace** v dialogovém okně **deklarace skupiny** proveďte následující kroky:

    a. Klikněte na **pero** vedle **skupin vrácených v deklaraci identity**.

    ![Snímek obrazovky zobrazuje atributy uživatele & deklarace identity pomocí ikony pro skupiny vrácené ve vybrané deklaraci identity.](./media/amazon-business-tutorial/config04.png)

    ![Snímek obrazovky zobrazuje deklarace skupin s hodnotami, jak je popsáno v tomto postupu.](./media/amazon-business-tutorial/config05.png)

    b. V seznamu přepínačů vyberte **všechny skupiny** .

    c. Jako **zdrojový atribut**vyberte **ID skupiny** .

    d. Zaškrtněte políčko **přizpůsobit název deklarace skupiny** a zadejte název skupiny podle požadavků vaší organizace.

    e. Klikněte na **Uložit**.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **XML metadata** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení organizace Amazon Business** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Konfigurace Amazon Business SSO

1. V jiném okně webového prohlížeče se přihlaste k webu Amazon Business Company jako správce.

1. Klikněte na **Profil uživatele** a vyberte **Nastavení firmy**.

    ![Uživatelský profil](media/amazon-business-tutorial/user-profile.png)

1. V průvodci **integrací systému** vyberte **jednu Sign-On (SSO)**.

    ![Jednotné přihlašování (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. V průvodci **nastavením jednotného přihlašování** vyberte poskytovatele podle požadavků vaší organizace a klikněte na **Další**.

    ![Snímek obrazovky ukazuje nastavení S S S, s Microsoft Azureem D a dalším vybraným.](media/amazon-business-tutorial/default-group1.png)
    
    > [!NOTE]
    > I když je uvedená možnost Microsoft ADFS, nebude fungovat s přihlašováním pomocí Azure AD.

1. V Průvodci vytvořením **nového uživatelského účtu** vyberte **výchozí skupinu** a pak v závislosti na roli uživatele ve vaší organizaci vyberte **výchozí nákupní roli** a klikněte na **Další**.

    ![Snímek obrazovky zobrazuje výchozí nastavení nového uživatelského účtu s Microsoftem S S S. O, režádanka a dalším vybraným.](media/amazon-business-tutorial/dafault-group2.png)

1. V průvodci **nahrajte soubor metadat** klikněte na **Procházet** a nahrajte soubor **XML s metadaty** , který jste stáhli z Azure Portal, a klikněte na **nahrát**.

    ![Snímek obrazovky ukazuje nahrání souboru metadat, který vám umožní přejít na soubor x m l a nahrát ho.](media/amazon-business-tutorial/connection-data1.png)

1. Po nahrání staženého souboru metadat se pole v části **data připojení** naplní automaticky. Poté klikněte na tlačítko **Další**.

    ![Snímek obrazovky zobrazuje data o připojení, kde můžete zadat identifikátor Azure A D, přihlásit se U R L a podpisový certifikát SAML.](media/amazon-business-tutorial/connection-data2.png)

1. V průvodci **odesláním příkazu atributu** klikněte na **Přeskočit**.

    ![Snímek obrazovky ukazuje odeslání příkazu atributu, který umožňuje procházení k příkazu atributu, ale v tomto případě vyberte Přeskočit.](media/amazon-business-tutorial/map-attribute1.png)

1. V průvodci **mapováním atributů** přidejte pole požadavků kliknutím na možnost **+ Přidat pole** . Přidejte hodnoty atributu, včetně oboru názvů, který jste zkopírovali z části **atributy uživatele & deklarace identity** v Azure Portal do pole  **SAML attributeName** a klikněte na **Další**.

    ![Snímek obrazovky ukazuje mapování atributů, kde můžete upravovat své názvy atributů aplikace Amazon data.](media/amazon-business-tutorial/map-attribute2.png)

1. V průvodci pro **připojení Amazon data** klikněte na **Další**.

    ![Snímek obrazovky zobrazuje data připojení Amazon, kde můžete pokračovat kliknutím na tlačítko Další.](media/amazon-business-tutorial/amazon-connect.png)

1. Zkontrolujte **stav** nakonfigurovaných kroků a klikněte na **Spustit testování**.

    ![Snímek obrazovky se zobrazí podrobnosti o připojení s možností spuštění testování.](media/amazon-business-tutorial/sso-connection1.png)

1. V průvodci **Test připojení SSO** klikněte na **test**.

    ![Snímek obrazovky ukazuje připojení testu s S s tlačítkem test.](media/amazon-business-tutorial/sso-connection2.png)

1. V průvodci **spouštěnou adresu URL IDP** před kliknutím na **aktivovat**Zkopírujte hodnotu, která je přiřazená **idpid** a vložte do parametru **Idpid** v **adrese URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    ![Snímek obrazovky zobrazuje I D P iniciované U R L, kde můžete získat U R l nezbytnou pro testování a pak vybrat aktivovat.](media/amazon-business-tutorial/sso-connection3.png)

1. Na stránce jste **připraveni přepnout na aktivní jednotné přihlašování?** zaškrtněte políčko **mám plně testované jednotné přihlašování a** připravuje se na aktivní a klikněte na **Přepnout na aktivní**.

    ![Snímek obrazovky ukazuje, že jste připraveni přepnout na aktivní S S-O potvrzení, kde můžete vybrat přepínač přepnout na aktivní.](media/amazon-business-tutorial/sso-connection4.png)

1. Nakonec v části **Podrobnosti připojení SSO** se **stav** zobrazuje jako **aktivní**.

    ![Snímek obrazovky se zobrazí podrobnosti o připojení s stavem aktivní.](media/amazon-business-tutorial/sso-connection5.png)
    
    > [!NOTE]
    > Pokud chcete nakonfigurovat aplikaci v režimu **SP** iniciované, proveďte následující krok a vložte adresu URL pro přihlášení z obrazovky výše v textovém poli **Adresa URL pro přihlášení** v části **nastavit další adresy URL** v Azure Portal. Použijte tento formát:
    >
    > `https://www.amazon.<TLD>/bb/feature/sso/action/start?domain_hint=<uniqueid>`
    
### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

> [!NOTE]
> Správci potřebují v případě potřeby vytvářet testovací uživatele ve svém tenantovi. Následující kroky ukazují, jak vytvořit testovacího uživatele.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Vytvoření skupiny zabezpečení Azure AD v Azure Portal

1. Klikněte na **Azure Active Directory > všechny skupiny**.

    ![Snímek obrazovky se zobrazí nabídka Azure Portal s vybraným Azure Active Directory a všemi skupinami vybranými v podokně skupiny.](./media/amazon-business-tutorial/all-groups-tab.png)

1. Klikněte na **Nová skupina**:

    ![Snímek obrazovky se zobrazí tlačítko Nová skupina.](./media/amazon-business-tutorial/new-group-tab.png)

1. Vyplňte pole **typ skupiny**, **název skupiny**, **Popis skupiny**, **typ členství**. Klikněte na šipku a vyberte členy, vyhledejte nebo klikněte na člen, který chcete přidat do skupiny. Kliknutím na **Vybrat** přidejte vybrané členy a pak klikněte na **vytvořit**.

    ![Snímek obrazovky se zobrazí v podokně skupiny s možnostmi, včetně výběru členů a pozvání externích uživatelů.](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k organizaci Amazon Business.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Amazon Business**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Snímek obrazovky se zobrazeným tlačítkem Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

    >[!NOTE]
    > Pokud nepřiřadíte uživatele v Azure AD, zobrazí se následující chyba.

    ![Snímek obrazovky zobrazuje chybovou zprávu, že se nemůžete přihlásit.](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Přiřaďte skupinu zabezpečení Azure AD v Azure Portal

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Amazon Business**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Amazon Business**.

    ![Odkaz Amazon Business v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na **Přidat uživatele**.

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. Vyhledejte skupinu zabezpečení, kterou chcete použít, a potom ji kliknutím na skupinu přidejte do oddílu Vybrat členy. Klikněte na **Vybrat**a pak na **přiřadit**.

    ![Hledat skupinu zabezpečení](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Ověřte oznámení v řádku nabídek, abyste byli informováni o úspěšném přiřazení skupiny do podnikové aplikace v Azure Portal.

### <a name="create-amazon-business-test-user"></a>Vytvořit uživatele Amazon Business test

V této části se v Amazon Business vytvoří uživatel nazvaný B. Simon. Amazon Business podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Amazon Business neexistuje, vytvoří se po ověření nový.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Amazon Business na přístupovém panelu byste se měli automaticky přihlášeni k organizaci Amazon Business, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
