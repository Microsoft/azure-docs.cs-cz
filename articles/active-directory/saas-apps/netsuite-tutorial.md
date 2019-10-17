---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s NetSuite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438496"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s NetSuite

V tomto kurzu se dozvíte, jak integrovat NetSuite s Azure Active Directory (Azure AD). Když integrujete NetSuite s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k NetSuite.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k NetSuite svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* NetSuite odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* NetSuite podporuje jednotné přihlašování **IDP** .

* NetSuite podporuje zřizování uživatelů **jenom v čase** .

* NetSuite podporuje [automatizované zřizování uživatelů](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-netsuite-from-the-gallery"></a>Přidání NetSuite z Galerie

Pokud chcete nakonfigurovat integraci NetSuite do služby Azure AD, musíte přidat NetSuite z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **NetSuite** .
1. Na panelu výsledků vyberte **NetSuite** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro NetSuite

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí NetSuite pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v NetSuite.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí NetSuite, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte NETSUITE SSO](#configure-netsuite-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte NetSuite Test User](#create-netsuite-test-user)** -to, abyste měli protějšek B. Simon v NetSuite, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **NetSuite** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat hodnotu, obraťte se na [tým podpory klienta NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. NetSuite aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů. Kliknutím na tlačítko **Upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

1. Kromě výše očekává aplikace NetSuite několik dalších atributů, které se vrátí zpátky v odpovědi SAML. V části deklarace identity uživatelů v dialogovém okně atributy uživatele proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulce:

    | Name (Název) | Zdrojový atribut | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    1. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    1. Ponechte **obor názvů** prázdný.

    1. Jako **atribut**vyberte zdroj.

    1. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    1. Klikněte na **OK** .

    1. Klikněte na **Uložit**.

    >[!NOTE]
    >Hodnota atributu Account není reálné číslo. Tuto hodnotu budete aktualizovat, což je vysvětleno později v tomto kurzu.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **XML metadata federace** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **Nastavení NetSuite** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k NetSuite.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **NetSuite**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-netsuite-sso"></a>Konfigurace jednotného přihlašování NetSuite

1. V prohlížeči otevřete novou kartu a přihlaste se k webu NetSuite společnosti jako správce.

2. Na panelu nástrojů v horní části stránky klikněte na **Nastavení**, přejděte na **Společnost** a klikněte na **Povolit funkce**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na panelu nástrojů uprostřed stránky klikněte na **SuiteCloud**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-suitecloud.png)

4. V části **spravovat ověřování** vyberte **jednotné přihlašování SAML** , abyste v NETSUITE mohli povolit možnost jednotného přihlašování SAML.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na panelu nástrojů v horní části stránky klikněte na tlačítko **nastavit**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

6. V seznamu **úlohy instalace** klikněte na **integrace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-integration.png)

7. V části **spravovat ověřování** klikněte na **jednotné přihlašování SAML**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml.png)

8. Na stránce **Nastavení SAML** v části **Konfigurace NetSuite** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Vyberte **metodu primárního ověřování**.

    b. Pro pole s popiskem **metadata poskytovatele identity SAMLV2**vyberte **nahrát soubor metadat IDP**. Pak klikněte na tlačítko **Procházet** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    c. Klikněte na **Odeslat**.

9. V NetSuite klikněte na **Nastavení** , přejděte na **Společnost** a v horní navigační nabídce klikněte na **informace o společnosti** .

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-account-id.png)

    b. Na stránce **informace o společnosti** v pravém SLOUPCI zkopírujte **ID účtu**.

    c. Vložte **ID účtu** , které jste zkopírovali z účtu NetSuite, do pole **hodnota atributu** ve službě Azure AD. 

10. Předtím, než mohou uživatelé provádět jednotné přihlašování do NetSuite, musí být nejprve přiřazena příslušná oprávnění v NetSuite. Pokud chcete přiřadit tato oprávnění, postupujte podle následujících pokynů.

    a. V horní navigační nabídce klikněte na **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    b. V levém navigačním panelu vyberte **Uživatelé/role**a pak klikněte na **Spravovat role**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klikněte na **Nová role**.

    d. Zadejte **název** nové role.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klikněte na **Uložit**.

    f. V nabídce v horní části klikněte na **oprávnění**. Pak klikněte na **Nastavení**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-sso.png)

    g. Vyberte **jednotné přihlašování SAML**a pak klikněte na **Přidat**.

    h. Klikněte na **Uložit**.

    i. V horní navigační nabídce klikněte na **Nastavení**a pak na **Správce instalace**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-setup.png)

    j. V levém navigačním panelu vyberte **Uživatelé/role**a pak klikněte na **Spravovat uživatele**.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Vyberte testovacího uživatele. Pak klikněte na **Upravit** a pak přejděte na kartu **přístup** .

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-edit-user.png)

    l. V dialogu role přiřaďte příslušnou roli, kterou jste vytvořili.

    ![Konfigurace jednotného přihlašování](./media/NetSuite-tutorial/ns-add-role.png)

    4m. Klikněte na **Uložit**.

### <a name="create-netsuite-test-user"></a>Vytvořit testovacího uživatele NetSuite

V této části se v NetSuite vytvoří uživatel s názvem Britta Simon. NetSuite podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v NetSuite neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici NetSuite, měli byste se automaticky přihlásit k NetSuite, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si NetSuite s Azure AD](https://aad.portal.azure.com/)

