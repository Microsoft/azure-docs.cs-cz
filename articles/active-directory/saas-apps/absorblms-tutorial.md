---
title: 'Kurz: Azure Active Directory integrace s absorpčním LMS | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a absorpčním LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 268943463002ddd1dbdbf67df9587758f81f537f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646516"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Kurz: Azure Active Directory integrace s absorpčním LMS

V tomto kurzu se dozvíte, jak integrovat pomocí Azure Active Directory (Azure AD). Když integrujte pomocí služby Azure AD přívedený LMS, můžete:

* Řízení ve službě Azure AD, která má přístup k pohlcení LMS.
* Umožněte uživatelům, aby se automaticky přihlásili k pohlcování LMS pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s použitím nástroje pro zavedení s použitím absorpčního LMS potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k odběru povolenému jednotného přihlašování LMS.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LMS s absorpcí podporuje **IDP** , které INICIOVALO jednotné přihlášení

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="add-absorb-lms-from-the-gallery"></a>Přidání pohlcování pro LMS z Galerie

Chcete-li nakonfigurovat integraci systému pro pohlcování do služby Azure AD, je třeba přidat do seznamu spravovaných aplikací pro SaaS absorpční LMS z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole text **absorbovaný LMS** .
1. Na panelu výsledků vyberte **absorpční LMS** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro LMS

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím LMS s pohlcováním pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v LMS v absorpčním prostředí.

K nakonfigurování a testování jednotného přihlašování služby Azure AD s využitím LMS proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování pro LMS](#configure-absorb-lms-sso)** systém, abyste na straně aplikace nakonfigurovali nastavení jednotného přihlašování.
    1. **[Vytvořte uživatele s pohlcováním testů pro LMS](#create-absorb-lms-test-user)** , který bude mít protějšek B. Simon v systému v rámci absorpčního LMS, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce věnované integraci aplikací pro **LMS** systém, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na tlačítko **Upravit** a otevřete tak základní dialogové okno **Konfigurace SAML** .

    Pokud používáte **pohlcování 5 – uživatelské rozhraní** používá tuto konfiguraci:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Pokud používáte **absorpci 5 – nové prostředí** pro práci s dalšími postupy, použijte následující konfiguraci:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory pro klienty LMS](https://support.absorblms.com/hc/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde **NameIdentifier** je mapován pomocí **User. userPrincipalName**.

    ![image](common/edit-attribute.png)

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **set LMS pro pohlcování** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k tomuto LMS.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **absorpční LMS**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-absorb-lms-sso"></a>Konfigurace jednotného přihlašování k pohlcování LMS

1. V novém okně webového prohlížeče se přihlaste k vašemu firemnímu LMS webu jako správce.

2. V pravém horním rohu vyberte tlačítko **účet** .

    ![Tlačítko účet](./media/absorblms-tutorial/account.png)

3. V podokně účet vyberte **nastavení portálu**.

    ![Odkaz nastavení portálu](./media/absorblms-tutorial/portal.png)

4. Vyberte kartu **Správa nastavení jednotného přihlašování** .

    ![Karta uživatelé](./media/absorblms-tutorial/sso.png)

5. Na stránce **Spravovat nastavení jednoho Sign-On** postupujte takto:

    ![Stránka Konfigurace jednotného přihlašování](./media/absorblms-tutorial/settings.png)

    a. Do textového pole **název** zadejte název, jako je například jednotné přihlašování Azure AD Marketplace.

    b. Jako **metodu** vyberte **SAML** .

    c. V programu Poznámkový blok otevřete certifikát, který jste stáhli z Azure Portal. Odeberte **---počáteční---certifikátu** a **------značky koncového certifikátu** . Potom do pole **klíč** vložte zbývající obsah.

    d. V poli **režim** vyberte **poskytovatele identity iniciované**.

    e. V poli **vlastnost ID** vyberte atribut, který jste nakonfigurovali jako identifikátor uživatele v Azure AD. Pokud je například ve službě Azure AD vybraný *NameIdentifier* , vyberte **uživatelské jméno**.

    f. Jako **typ podpisu** vyberte **SHA256** .

    například Do pole **Adresa URL pro přihlášení** vložte **adresu URL přístupu uživatele** ze stránky **vlastností** aplikace Azure Portal.

    h. Do pole **Adresa URL pro odhlášení** vložte hodnotu **URL** pro odhlášení, kterou jste zkopírovali z okna **Konfigurace přihlášení** Azure Portal.

    i. Přepínač **automaticky přesměrovat** na **zapnuto**.

6. Vyberte **Uložit.**

    ![Jediný přepínač povolení přihlášení SSO](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Vytvořit uživatele testu na absorpční LMS

Aby se uživatelé Azure AD přihlásili ke absorpčnímu LMS, musí být nastaveni v LMS v absorbované. V případě programu absorpčního LMS je zřizování ručním úkolem.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se ke svému webu společnosti s příznakem pro LMS jako správce.

2. V podokně **Uživatelé** vyberte **Uživatelé**.

    ![Odkaz uživatelé](./media/absorblms-tutorial/users.png)

3. Vyberte kartu **uživatel** .

    ![Rozevírací seznam přidat nový](./media/absorblms-tutorial/add.png)

4. Na stránce **Přidat uživatele** postupujte takto:

    ![Stránka Přidat uživatele](./media/absorblms-tutorial/user.png)

    a. Do pole **jméno zadejte jméno,** například **Britta**.

    b. Do pole **příjmení** zadejte příjmení, například **Simon**.

    c. Do pole **uživatelské jméno** zadejte celé jméno, například **Britta Simon**.

    d. Do pole **heslo** zadejte heslo uživatele.

    e. Do pole **Potvrdit heslo** zadejte heslo znovu.

    f. Nastavte přepínač **je aktivní** na **aktivní**.

5. Vyberte **Uložit.**

    ![Jediný přepínač povolení přihlášení SSO](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Ve výchozím nastavení není zřizování uživatelů v jednotném přihlašování povolené. Pokud chce zákazník tuto funkci povolit, musí ji nastavit tak, jak je uvedeno v [této](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) dokumentaci. Upozorňujeme také, že pro uživatele je k dispozici pouze **pohlcování 5 – nové prostředí s možností učení** s adresou URL služby ACS –`https://company.myabsorb.com/api/rest/v2/authentication/saml`

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni k LMS, pro který jste nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici pohlcená LMS v okně moje aplikace, měli byste být automaticky přihlášeni k LMS, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Jakmile nastavíte LMS, můžete vynutilit řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).