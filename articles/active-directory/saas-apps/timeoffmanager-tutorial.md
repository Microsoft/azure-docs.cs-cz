---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s TimeOffManager | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06aa2ddf3e7168147ec091ef6fb9826025f23364
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561809"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s TimeOffManager

V tomto kurzu se dozvíte, jak integrovat TimeOffManager s Azure Active Directory (Azure AD). Když integrujete TimeOffManager s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k TimeOffManager.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k TimeOffManager svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* TimeOffManager odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.


* TimeOffManager podporuje jednotné přihlašování **IDP** .

* TimeOffManager podporuje zřizování uživatelů **jenom v čase** .

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.


## <a name="adding-timeoffmanager-from-the-gallery"></a>Přidání TimeOffManager z Galerie

Pokud chcete nakonfigurovat integraci TimeOffManager do služby Azure AD, musíte přidat TimeOffManager z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **TimeOffManager** .
1. Na panelu výsledků vyberte **TimeOffManager** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Konfigurace a testování jednotného přihlašování Azure AD pro TimeOffManager

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí TimeOffManager pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v TimeOffManager.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí TimeOffManager, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte TIMEOFFMANAGER SSO](#configure-timeoffmanager-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte TimeOffManager Test User](#create-timeoffmanager-test-user)** -to, abyste měli protějšek B. Simon v TimeOffManager, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **TimeOffManager** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Tato hodnota není reálné číslo. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Tuto hodnotu můžete získat na **stránce nastavení jednotného přihlašování** , která je vysvětlena dále v kurzu nebo se obraťte na [tým podpory TimeOffManager](https://www.purelyhr.com/contact-us). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. TimeOffManager aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace TimeOffManager několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name (Název) | Zdrojový atribut|
    | --- | --- |
    | FirstName |User. křestní jméno |
    | Polím |User. příjmení |
    | E-mail |User.mail |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení TimeOffManager** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k TimeOffManager.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **TimeOffManager**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-timeoffmanager-sso"></a>Konfigurace jednotného přihlašování TimeOffManager

1. V jiném okně webového prohlížeče se přihlaste k webu TimeOffManager společnosti jako správce.

2. V nabídce **účet \> možnosti účtu \> nastavení jednotného přihlašování**.
   
    ![Nastavení jednotného přihlašování](./media/timeoffmanager-tutorial/ic795917.png "Nastavení jednotného přihlašování")

3. V části **nastavení jednotného přihlašování** proveďte následující kroky:
   
    ![Nastavení jednotného přihlašování](./media/timeoffmanager-tutorial/ic795918.png "Nastavení jednotného přihlašování")
   
    a. V programu Poznámkový blok otevřete svůj certifikát s kódováním Base-64, zkopírujte jeho obsah do schránky a vložte celý certifikát do textového pole **certifikátu X. 509** .
   
    b. Do textového pole **vystavitele IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.
   
    c. Do textového pole **Adresa URL koncového bodu IDP** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
   
    d. Jako **Vynutilí SAML**vyberte **ne**.
   
    e. Jako **automaticky vytvořit uživatele**vyberte **Ano**.
   
    f. Do textového pole **Adresa URL pro odhlášení** vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.
   
    g. klikněte na **Uložit změny**.

4. Na stránce **nastavení jednotného přihlašování** Zkopírujte hodnotu **adresy URL služby pro příjemce kontrolního výrazu** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal. 

      ![Nastavení jednotného přihlašování](./media/timeoffmanager-tutorial/ic795915.png "Nastavení jednotného přihlašování")

### <a name="create-timeoffmanager-test-user"></a>Vytvořit testovacího uživatele TimeOffManager

V této části se v TimeOffManager vytvoří uživatel s názvem Britta Simon. TimeOffManager podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v TimeOffManager neexistuje, vytvoří se po ověření nový.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jiné nástroje pro vytváření uživatelských účtů TimeOffManager nebo rozhraní API poskytovaná TimeOffManager.

## <a name="test-sso"></a>Test SSO 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici TimeOffManager, měli byste se automaticky přihlásit k TimeOffManager, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje informací:

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si TimeOffManager s Azure AD](https://aad.portal.azure.com/)

