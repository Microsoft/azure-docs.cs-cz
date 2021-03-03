---
title: 'Kurz: Azure Active Directory integrace s podpisem Adobe | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a symbolem Adobe.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 7162c38aae2fec4ea21aae56fa8c3649f7a55425
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649947"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Kurz: Azure Active Directory integrací pomocí Adobe Sign

V tomto kurzu se dozvíte, jak integrovat znaménko Adobe pomocí Azure Active Directory (Azure AD). Když integrujete přihlašování Adobe s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Adobe Sign.
* Umožněte, aby se vaši uživatelé automaticky přihlásili pomocí svých účtů Azure AD do programu Adobe Sign.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:
 
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlášení k odběru s jednotným přihlašováním (SSO) společnosti Adobe.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe **Sign podporuje jednotné** přihlašování.

## <a name="add-adobe-sign-from-the-gallery"></a>Přidání znaménka Adobe z Galerie

Pokud chcete nakonfigurovat integraci Adobe přihlašování do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat znaménko Adobe z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **symbol Adobe** .
1. Na panelu výsledků vyberte **symbol Adobe** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Adobe Sign

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí programu Adobe Signing na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v symbolech Adobe.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí přihlášení Adobe, musíte provést následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
1. **[Nakonfigurujte přihlašování Adobe Sign SSO](#configure-adobe-sign-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořit Adobe Sign Test User](#create-adobe-sign-test-user)** – abyste měli protějšek Britta Simon v symbolech Adobe, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí přihlášení Adobe, proveďte následující kroky:

1. V Azure Portal na stránce **Adobe Sign** aplikace Integration vyberte **jednotné přihlašování**.

1. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky a otevřete dialogové okno **základní konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.echosign.com/`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory společnosti Adobe Signer support](https://helpx.adobe.com/in/contact/support.html) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **nastavit znaménko Adobe** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Adobe Sign.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **Adobe Sign**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-adobe-sign-sso"></a>Konfigurace přihlašování Adobe SSO

1. Před konfigurací se obraťte na [tým podpory společnosti Adobe Signer support](https://helpx.adobe.com/in/contact/support.html) , který přidá vaši doménu do seznamu povolených podpisů Adobe. Tady je postup, jak přidat doménu:

    a. [Tým podpory podepisování klientů společnosti Adobe](https://helpx.adobe.com/in/contact/support.html) vám pošle náhodně vygenerovaný token. Pro vaši doménu bude token vypadat takto: **Adobe-Sign-ověřování = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Publikujte token ověřování v textovém záznamu DNS a upozorněte [tým podpory klienta podpory společnosti Adobe](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Tato možnost může trvat několik dní nebo déle. Všimněte si, že zpoždění šíření DNS znamenají, že hodnota publikovaná v DNS nemusí být viditelná za hodinu nebo déle. Váš správce IT by měl být znalostí o tom, jak tento token publikovat v textovém záznamu DNS.

    c. Když [klientovi podpory společnosti Adobe podepíše](https://helpx.adobe.com/in/contact/support.html) prostřednictvím lístku podpory, po publikování tokenu ověří doménu a přidá ji do svého účtu.

    d. Obecně platí, že tady je postup publikování tokenu na záznamu DNS:

    * Přihlaste se ke svému účtu domény.
    * Vyhledejte stránku pro aktualizaci záznamu DNS. Tato stránka se může jmenovat správa DNS, Správa názvového serveru nebo rozšířená nastavení.
    * Vyhledejte záznamy TXT pro vaši doménu.
    * Přidejte záznam TXT s hodnotou úplného tokenu poskytnutou společností Adobe.
    * Uložte provedené změny.

1. V jiném okně webového prohlížeče se přihlaste k webu společnosti Adobe Signing Company jako správce.

1. V nabídce SAML vyberte **Nastavení účtu**  >  **Nastavení SAML**.

    ![Snímek obrazovky se stránkou nastavení pro Adobe – podepsání SAML](./media/adobe-echosign-tutorial/settings.png "Účet")

1. V části **Nastavení SAML** proveďte následující kroky:

    ![Snímek obrazovky, který zvýrazní nastavení SAML, včetně povinného SAML.](./media/adobe-echosign-tutorial/saml1.png "Nastavení SAML")

   ![Snímek obrazovky s nastavením SAML](./media/adobe-echosign-tutorial/saml.png "Nastavení SAML")

   a. V části **režim SAML** vyberte **SAML povinný**.

   b. Vyberte možnost **dovolit správcům účtu EchoSign se přihlaste pomocí svých přihlašovacích údajů EchoSign**.

   c. V části **Vytvoření uživatele** vyberte možnost **automaticky přidat uživatele ověřené prostřednictvím SAML**.

   d. Do textového pole **ID entity IDP** vložte **identifikátor Azure AD**, který jste zkopírovali z Azure Portal.

   e. Vložte **adresu URL pro přihlášení**, kterou jste zkopírovali z Azure Portal do textového pole **přihlašovací adresa URL pro IDP** .

   f. Vložte **adresu URL pro odhlášení**, kterou jste zkopírovali z Azure Portal do textového pole **Adresa URL pro odhlášení IDP** .

   například Otevřete stažený soubor **certifikátu (Base64)** v programu Poznámkový blok. Zkopírujte obsah této složky do schránky a vložte ji do textového pole **certifikát IDP** .

   h. Vyberte **Uložit změny**.

### <a name="create-adobe-sign-test-user"></a>Vytvořit Adobe Sign Test User

Aby se uživatelé Azure AD mohli přihlásit k přihlašování do Adobe, musí se zřídit v symbolech Adobe. Toto je ruční úloha.

>[!NOTE]
>K zřizování uživatelských účtů Azure AD můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů nebo rozhraní API pro přihlášení společnosti Adobe, které poskytuje symbol Adobe Signing. 

1. Přihlaste se k webu společnosti **Adobe Sign** společnosti jako správce.

2. V nabídce v horní části vyberte **účet**. Pak v levém podokně vyberte **Uživatelé & skupiny**  >  **vytvořit nového uživatele**.

    ![Snímek obrazovky společnosti Adobe podepsat firemní web, s účtem, uživateli &skupin a vytvořit nově zvýrazněného uživatele](./media/adobe-echosign-tutorial/account.png "Účet")

3. V části **vytvořit nového uživatele** proveďte následující kroky:

    ![Snímek obrazovky s oddílem pro vytvoření nového uživatele](./media/adobe-echosign-tutorial/user.png "Vytvořit uživatele")

    a. Zadejte **e-mailovou adresu**, **jméno** a **příjmení** platného účtu Azure AD, který chcete zřídit, do příslušných textových polí.

    b. Vyberte **vytvořit uživatele**.

>[!NOTE]
>Držitel účtu Azure Active Directory obdrží e-mail, který obsahuje odkaz na potvrzení účtu, než bude aktivní. 

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Adobe, kde můžete spustit tok přihlášení. 

* Přejít přímo na adresu URL pro přihlášení k Adobe a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici symbol Adobe v části Moje aplikace, měli byste se automaticky přihlásit k znaménku Adobe, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování podepisování Adobe můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).