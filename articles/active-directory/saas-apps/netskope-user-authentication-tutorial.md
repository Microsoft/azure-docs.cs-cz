---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s ověřováním uživatele Netskope | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Netskope ověřování uživatelů.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.openlocfilehash: 62d6d5527cd917304e4c8bc0c4740f4edb772218
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939165"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s ověřováním uživatele Netskope

V tomto kurzu se dozvíte, jak integrovat ověřování uživatelů Netskope pomocí Azure Active Directory (Azure AD). Když integrujete Netskope ověřování uživatelů pomocí Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ověření uživatele Netskope.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Netskope ověřování uživatelů pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Netskope odběr ověřování uživatelů jednotného přihlašování (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Netskope ověřování uživatelů podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>Přidání ověření uživatele Netskope z Galerie

Pokud chcete nakonfigurovat integraci Netskope ověřování uživatelů do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Netskope ověřování uživatelů z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Netskope ověřování uživatele** .
1. Na panelu výsledků vyberte **ověřování uživatele Netskope** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>Konfigurace a testování jednotného přihlašování Azure AD pro ověřování uživatelů Netskope

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ověřování uživatelů Netskope pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Netskope ověřování uživatelů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s ověřováním uživatele Netskope, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování pro ověřování uživatelů Netskope](#configure-netskope-user-authentication-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořit testovacího uživatele pro ověření uživatele Netskope](#create-netskope-user-authentication-test-user)** – Pokud chcete mít protějšek B. Simon v ověřování uživatelů Netskope, které je propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **ověřování uživatele Netskope** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.goskope.com/<customer entered string>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Tyto hodnoty se zobrazí později v tomto kurzu.

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Hodnoty přihlašovacích adres URL nejsou reálné. Aktualizujte hodnotu URL pro přihlášení pomocí skutečné přihlašovací adresy URL. Pokud chcete získat hodnotu URL pro přihlášení, obraťte se na [tým podpory klienta pro ověřování uživatelů Netskope](mailto:support@netskope.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

1. V části **nastavení ověření uživatele Netskope** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k ověřování uživatelů Netskope.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Netskope ověřování uživatele**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-netskope-user-authentication-sso"></a>Konfigurace jednotného přihlašování pro ověřování uživatelů Netskope

1. V prohlížeči otevřete novou kartu a přihlaste se k webu ověřování uživatele Netskope jako správce.

1. Klikněte na kartu **Aktivní platforma** .

    ![Snímek obrazovky se zobrazí aktivní Platforma vybraná z nastavení.](./media/netskope-user-authentication-tutorial/user1.png)

1. Posuňte se dolů k **dopředné proxy** a vyberte **SAML**.

    ![Snímek obrazovky zobrazuje SAML vybraný z aktivní platformy.](./media/netskope-user-authentication-tutorial/config-saml.png)

1. Na stránce **Nastavení SAML** proveďte následující kroky:

    ![Snímek obrazovky ukazuje nastavení SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. Zkopírujte hodnotu **ID entity SAML** a vložte ji do textového pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal.

    b. Zkopírujte hodnotu **URL služby ACS pro SAML** a vložte ji do textového pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

1. Klikněte na **Přidat účet**.

    ![Snímek obrazovky ukazuje přidat účet vybraný v podokně SAML.](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. Na stránce **Přidat účet SAML** proveďte následující kroky:

    ![Snímek obrazovky ukazuje přidat účet SAML, kde můžete zadat hodnoty, které jsou popsány.](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. Do textového pole **název** zadejte název jako Azure AD.

    b. Do textového pole **Adresa URL IDP** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Otevřete stažený soubor metadat v programu Poznámkový blok, zkopírujte jeho obsah do schránky a vložte ho do textového pole **IDP certifikátu** .

    e. Klikněte na **Uložit**.

### <a name="create-netskope-user-authentication-test-user"></a>Vytvořit testovacího uživatele pro ověření uživatele Netskope

1. V prohlížeči otevřete novou kartu a přihlaste se k webu ověřování uživatele Netskope jako správce.

1. V levém navigačním podokně klikněte na kartu **Nastavení** .

    ![Snímek obrazovky se zobrazením vybraného nastavení.](./media/netskope-user-authentication-tutorial/config-settings.png)

1. Klikněte na kartu **Aktivní platforma** .

    ![Snímek obrazovky se zobrazí aktivní Platforma vybraná z nastavení.](./media/netskope-user-authentication-tutorial/user1.png)

1. Klikněte na kartu **Uživatelé** .

    ![Snímek obrazovky zobrazuje uživatele vybrané z aktivní platformy.](./media/netskope-user-authentication-tutorial/add-user.png)

1. Klikněte na **Přidat uživatele**.

    ![Snímek obrazovky se zobrazí v dialogovém okně uživatelé, kde můžete vybrat přidat uživatele.](./media/netskope-user-authentication-tutorial/user-add.png)

1. Zadejte e-mailovou adresu uživatele, kterého chcete přidat, a klikněte na **Přidat**.

    ![Snímek obrazovky ukazuje přidat uživatele, kde můžete zadat seznam uživatelů.](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici ověření uživatele Netskope, měli byste se automaticky přihlásit k ověření uživatele Netskope, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte Netskope ověřování uživatelů pomocí Azure AD](https://aad.portal.azure.com/)