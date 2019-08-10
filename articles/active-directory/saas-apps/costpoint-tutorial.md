---
title: 'Kurz: Azure Active Directory integrace s Costpoint | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1a8b916feb2ad67623434f2b63468be72bf1aa
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879611"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Kurz: Integrace Costpoint s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Costpoint s Azure Active Directory (Azure AD). Když integrujete Costpoint s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Costpoint.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Costpoint svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Costpointm jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí. Costpoint podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-costpoint-from-the-gallery"></a>Přidání Costpoint z Galerie

Pokud chcete nakonfigurovat integraci Costpoint do služby Azure AD, musíte přidat Costpoint z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Costpoint** .
1. Na panelu výsledků vyberte **Costpoint** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Costpoint pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Costpoint.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Costpoint, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
2. **[Nakonfigurujte Costpoint](#configure-costpoint)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
4. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
5. **[Vytvořte uživatele Costpoint test](#create-costpoint-test-user)** , který bude mít protějšek B. Simon v Costpoint, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Costpoint** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud máte **soubor metadat poskytovatele služeb**v **základní části Konfigurace SAML** , proveďte následující kroky:

    > [!NOTE]
    > Soubor metadat poskytovatele služeb získáte v části **generování metadat Costpoint** , která je vysvětlena dále v tomto kurzu.
 
    1. Klikněte na tlačítko **nahrát soubor metadat**.
    
    1. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.
    
    1. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** identifikátoru a odpovědi automaticky naplní v textových polích oddílu Costpoint.

        > [!Note]
        > Pokud hodnoty **adresy URL** pro **identifikátor** a odpověď nezískávají automaticky polulated, zadejte je ručně podle vašich požadavků. Ověřte, že je správně nastavený **identifikátor (ID entity)** a **Adresa URL odpovědi (adresa URL služby pro příjemce kontrolního výrazu)** a že **Adresa URL služby ACS** je platná adresa URL Costpoint končící na **/LoginServlet.CPS**.

    1. Klikněte na **nastavit další adresy URL**.

    1. Do textového pole **stav přenosu** zadejte hodnotu pomocí následujícího vzoru:`system=[your system], (for example, **system=DELTEKCP**)`

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** , proveďte tento krok:
    
    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:`https://costpointteea.deltek.com/cpweb/cploginform.htm`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a stavem přenosu. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Costpoint](https://www.deltek.com/about/contact-us) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na ikonu Kopírovat a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do poznámkového bloku.

   ![Odkaz ke stažení certifikátu](common/copy-metadataurl.png)

### <a name="generate-costpoint-metadata"></a>Generovat metadata Costpoint

Costpoint konfigurace jednotného přihlašování SAML se vysvětluje v průvodci **DeltekCostpoint711Security. PDF** . V této části najdete informace o **nastavení jednotného přihlašování SAML – > konfiguraci jednotného přihlašování pomocí SAML mezi Costpoint a Azure AD** . Postupujte podle pokynů a vygenerujte soubor **XML metadat federace COSTPOINT SP** . Toto použijte v **základní konfiguraci SAML** v Azure Portal.

![Nástroj pro konfiguraci Costpoint](./media/costpoint-tutorial/config02.png)

> [!NOTE]
> Zobrazí se průvodce **DeltekCostpoint711Security. PDF** z [týmu podpory Costpoint klientů](https://www.deltek.com/about/contact-us). Pokud tento soubor nemáte, požádejte ho, aby tento soubor získali.

### <a name="configure-costpoint"></a>Konfigurace Costpoint

Vraťte se do **konfiguračního nástroje Costpoint** a vložte **adresu URL federačních metadat aplikace** do textového pole **XML federačních metadat IDP** a pokračujte podle pokynů v průvodci **DeltekCostpoint711Security. PDF** a dokončete Costpoint nastavení SAML. 

![Nástroj pro konfiguraci Costpoint](./media/costpoint-tutorial/config01.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. Vyberte **nového uživatele** v horní části obrazovky.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte B. Simon přístup k Costpoint.

1. V Azure Portal vyberte možnost **podnikové aplikace** > **všechny aplikace**.
1. V seznamu aplikace vyberte **Costpoint**.
1. V části **Správa** na stránce Přehled aplikace vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **Britta Simon** a klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-costpoint-test-user"></a>Vytvořit testovacího uživatele Costpoint

V této části vytvoříte uživatele v Costpoint. Předpokládejme, že **ID uživatele** je **b. Simon** a název **B. Simon**. Pokud chcete přidat uživatele na platformě Costpoint, pracujte s [týmem podpory klienta Costpoint](https://www.deltek.com/about/contact-us) . Před použitím jednotného přihlašování se musí vytvořit a aktivovat uživatel.
 
Po vytvoření musí být výběr **metody ověřování** uživatele ve **službě Active Directory**, musí být zaškrtnuto políčko **jednotné přihlašování SAML** a uživatelské jméno z Azure Active Directory musí být **Active Directory nebo ID certifikátu** . (jak vidíte níže).

![Uživatel Costpoint](./media/costpoint-tutorial/user01.png)

### <a name="test-sso"></a>Test SSO

Když na přístupovém panelu vyberete dlaždici Costpoint, měli byste se automaticky přihlásit k Costpoint, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)