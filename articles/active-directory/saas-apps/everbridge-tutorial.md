---
title: 'Kurz: Azure Active Directory integrace s EverBridge | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a EverBridge.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 6e281931eb4646e09bb9aa3226ed7d0735c84e3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643775"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Kurz: Azure Active Directory integrace s EverBridge

V tomto kurzu se dozvíte, jak integrovat EverBridge s Azure Active Directory (Azure AD).
Když integrujete EverBridge s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k EverBridge.
* Umožněte uživatelům, aby se automaticky přihlásili k EverBridge pomocí svých účtů Azure AD. Toto řízení přístupu se nazývá jednotné přihlašování (SSO).
* Spravujte své účty v jednom centrálním umístění pomocí Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s EverBridge potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné EverBridge, které používá jednotné přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* EverBridge podporuje jednotné přihlašování iniciované IDP.

## <a name="add-everbridge-from-the-gallery"></a>Přidání EverBridge z Galerie

Pokud chcete nakonfigurovat integraci EverBridge do služby Azure AD, musíte přidat EverBridge z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **EverBridge** .
1. Na panelu výsledků vyberte **EverBridge** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Konfigurace a testování jednotného přihlašování Azure AD pro EverBridge

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí EverBridge pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v EverBridge.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí EverBridge postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte EVERBRIDGE SSO](#configure-everbridge-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte EverBridge Test User](#create-everbridge-test-user)** -to, abyste měli protějšek B. Simon v EverBridge, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **EverBridge** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

    >[!NOTE]
    >Nakonfigurujte aplikaci buď jako portál správce, *nebo* jako členský portál na Azure Portal i na portálu EverBridge.

4. Chcete-li nakonfigurovat aplikaci **EverBridge** jako **portál správce EverBridge**, v **základní části Konfigurace SAML** použijte následující postup:

    ![Informace o jednotném přihlašování v doméně EverBridge a adresách URL](common/idp-intiated.png)

    a. Do pole **identifikátor** zadejte adresu URL, která následuje za vzorem.
    `https://sso.everbridge.net/<API_Name>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která následuje za vzorem.
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a hodnotami adresy URL odpovědi. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory EverBridge](mailto:support@everbridge.com). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Chcete-li nakonfigurovat aplikaci **EverBridge** jako **členský portál EverBridge**, v **základní části Konfigurace SAML** proveďte následující kroky:

  * Pokud chcete nakonfigurovat aplikaci v režimu iniciované IDP, postupujte takto:

     ![Informace jednotného přihlašování EverBridge domény a adres URL pro režim iniciované IDP](common/idp-intiated.png)

    a. Do pole **identifikátor** zadejte adresu URL, která následuje za vzorem. `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Do pole **Adresa URL odpovědi** zadejte adresu URL, která následuje za vzorem. `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL** a postupujte podle tohoto kroku:

     ![Informace o jednotném přihlašování EverBridge domény a adres URL pro režim inicializovaný v režimu SP](common/both-signonurl.png)

     a. Do pole **přihlašovací adresa URL** zadejte adresu URL, která následuje za vzorem. `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a hodnotami adresy URL pro přihlášení. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory EverBridge](mailto:support@everbridge.com). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte v části **podpisový certifikát SAML** možnost **Stáhnout** a Stáhněte si **XML federačních metadat**. Uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení EverBridge** zkopírujte adresy URL, které potřebujete pro vaše požadavky:

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k EverBridge.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **EverBridge**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-everbridge-sso"></a>Konfigurace jednotného přihlašování EverBridge

Pokud chcete nakonfigurovat jednotné přihlašování na **EverBridge** jako aplikaci **portálu EverBridge Manager** , postupujte podle těchto kroků.
 
1. V jiném okně webového prohlížeče se přihlaste k EverBridge jako správce.

1. V nabídce v horní části vyberte kartu **Nastavení** . V části **zabezpečení** vyberte **jednotné přihlašování**.
   
     ![Konfigurace jednotného přihlašování](./media/everbridge-tutorial/sso.png)
   
     a. Do pole **název** zadejte název poskytovatele identifikátoru. Příkladem je název vaší společnosti.
   
     b. Do pole **název rozhraní API** zadejte název rozhraní API.
   
     c. Vyberte **zvolit soubor** a odešlete soubor metadat, který jste stáhli z Azure Portal.
   
     d. V části **umístění identity SAML** vyberte **Identita v elementu NameIdentifier příkazu Subject**.
   
     e. Do pole **Adresa URL pro přihlášení zprostředkovatele identity** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.
   
     f. U **poskytovatele služby iniciovala vazby žádosti** vyberte **Přesměrování protokolu HTTP**.

     například Vyberte **Uložit**.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Konfigurace EverBridge jako jednotného přihlašování EverBridge pro členské portály

Pokud chcete nakonfigurovat jednotné přihlašování na **EverBridge** jako **portál pro členské EverBridge**, pošlete stažený **kód XML federačních metadat** do [týmu podpory EverBridge](mailto:support@everbridge.com). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-everbridge-test-user"></a>Vytvořit testovacího uživatele EverBridge

V této části vytvoříte testovacího uživatele Britta Simon v EverBridge. Pokud chcete přidat uživatele na platformě EverBridge, pracujte s [týmem podpory EverBridge](mailto:support@everbridge.com). Než začnete používat jednotné přihlašování, musí se uživatelé vytvořit a aktivovat v EverBridge. 

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k EverBridge, pro které jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici EverBridge v okně moje aplikace, měli byste se automaticky přihlásit k EverBridge, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování EverBridge můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).