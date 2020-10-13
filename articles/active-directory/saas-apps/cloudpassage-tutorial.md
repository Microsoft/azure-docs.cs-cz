---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s CloudPassage | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a CloudPassage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 8fdb59db7e11e3c8b946be940a2f98bd42810b88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758315"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s CloudPassage

V tomto kurzu se dozvíte, jak integrovat CloudPassage s Azure Active Directory (Azure AD). Když integrujete CloudPassage s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k CloudPassage.
* Umožněte uživatelům, aby se automaticky přihlásili k CloudPassage pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* CloudPassage odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* CloudPassage podporuje jednotné přihlašování iniciované v **SP**

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-cloudpassage-from-the-gallery"></a>Přidání CloudPassage z Galerie

Pokud chcete nakonfigurovat integraci CloudPassage do služby Azure AD, musíte přidat CloudPassage z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **CloudPassage** .
1. Na panelu výsledků vyberte **CloudPassage** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Konfigurace a testování jednotného přihlašování Azure AD pro CloudPassage

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí CloudPassage pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v CloudPassage.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí CloudPassage, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte CLOUDPASSAGE SSO](#configure-cloudpassage-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte CloudPassage Test User](#create-cloudpassage-test-user)** -to, abyste měli protějšek B. Simon v CloudPassage, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **CloudPassage** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

     a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://portal.cloudpassage.com/saml/consume/accountid` . Hodnotu pro tento atribut můžete získat kliknutím na **dokumentaci k nastavení** jednotného **přihlašování** v portálu CloudPassage.

    ![Snímek obrazovky se zobrazí na portálu CloudPassage s odkazem na dokumentaci k instalaci S S O instalačnímu programu s názvem.](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečnou adresou URL Sign-On a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta CloudPassage](https://www.cloudpassage.com/company/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. CloudPassage aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/edit-attribute.png)

1. Kromě toho očekává aplikace CloudPassage několik dalších atributů, které se vrátí zpátky v odpovědi SAML, které jsou uvedené níže. Tyto atributy se také předem naplní, ale můžete je zkontrolovat podle vašich požadavků.

    | Name | Zdrojový atribut|
    | ---------------| --------------- |
    | FirstName |User. křestní jméno |
    | polím |User. příjmení |
    | e-mail |uživatel. pošta |

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení CloudPassage** zkopírujte na základě vašeho požadavku příslušné adresy URL.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k CloudPassage.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **CloudPassage**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-cloudpassage-sso"></a>Konfigurace jednotného přihlašování CloudPassage

1. V jiném okně prohlížeče se přihlaste k webu CloudPassage společnosti jako správce.

1. V nabídce v horní části klikněte na **Nastavení**a pak klikněte na **Správa webu**. 
   
    ![Snímek obrazovky s vybraným webem pro CloudPassage.][12]

1. Klikněte na kartu **nastavení ověřování** . 
   
    ![Snímek obrazovky se zobrazí na webu CloudPassage s vybranou kartou nastavení ověřování.][13]

1. V části **nastavení jednotného přihlašování** proveďte následující kroky: 
   
    ![Snímek obrazovky se zobrazí část nastavení jednotného přihlašování, kde můžete zadat informace v tomto kroku.][14]

    a. Zaškrtněte políčko **Povolit jednotné přihlašování (SSO) (v dokumentaci k nastavení jednotného přihlašování** ).
    
    b. Do textového pole **Adresa URL vystavitele SAML** vložte **identifikátor Azure AD** .
  
    c. Vložte **přihlašovací adresu URL** do TEXTOVÉHO pole **URL koncového bodu SAML** .
  
    d. Vložte **odhlašovací adresu URL** do textového pole **Odhlásit úvodní stránku** .
  
    e. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah staženého certifikátu do schránky a vložte jej do textového pole **certifikátu x 509** .
  
    f. Klikněte na **Uložit**.

### <a name="create-cloudpassage-test-user"></a>Vytvořit testovacího uživatele CloudPassage

Cílem této části je vytvořit uživatele s názvem B. Simon v CloudPassage.

**Chcete-li vytvořit uživatele s názvem B. Simon v CloudPassage, proveďte následující kroky:**

1. Přihlaste se k webu **CloudPassage** společnosti jako správce. 

1. Na panelu nástrojů v horní části klikněte na **Nastavení**a pak klikněte na **Správa webu**. 
   
    ![Snímek obrazovky zobrazuje CloudPassage s vybraným webem správy.][22] 

1. Klikněte na kartu **Uživatelé** a potom klikněte na tlačítko **Přidat nového uživatele**. 
   
    ![Snímek obrazovky zobrazuje CloudPassage správu lokality pomocí vybrané karty uživatelé a možnost Přidat nového uživatele.][23]

1. V části **Přidat nového uživatele** proveďte následující kroky: 
   
    ![Snímek obrazovky se zobrazí část přidat nového uživatele, kde můžete zadat informace o uživateli.][24]
    
    a. Do textového pole **jméno v prvním** poli zadejte Britta. 
  
    b. Do textového pole **příjmení** zadejte Simon.
  
    c. Do textového pole **uživatelské jméno** zadejte textové pole **e-mailu** a textové pole pro **přetyp e-mailu** , do Azure AD zadejte uživatelské jméno Britta.
  
    d. Jako **typ přístupu**vyberte **Povolit přístup k portálu Halo**.
  
    e. Klikněte na **Přidat**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici CloudPassage, měli byste se automaticky přihlásit k CloudPassage, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si CloudPassage s Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

