---
title: 'Kurz: Azure Active Directory integrace s plátnem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a plátnem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: cc9b165dce34e667515be9ac9a69b171d6587354
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062929"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Kurz: Azure Active Directory integrace s plátnem

V tomto kurzu se dozvíte, jak integrovat plátno s Azure Active Directory (Azure AD). Když integrujete plátno s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k plátnu
* Umožněte uživatelům, aby se automaticky přihlásili k plátnu pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:
 
* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s jednotným přihlašováním (SSO) s podporou jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Plátno podporuje jednotné přihlašování na webu **SP**

## <a name="add-canvas-from-the-gallery"></a>Přidání plátna z Galerie

Pokud chcete nakonfigurovat integraci plátna do služby Azure AD, musíte přidat plátno z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **plátno** .
1. Vyberte **plátno** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Konfigurace a testování jednotného přihlašování Azure AD pro plátno

Nakonfigurujte a otestujte jednotné přihlašování Azure AD s plátnem pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem na plátně.

K nakonfigurování a testování jednotného přihlašování služby Azure AD s plátnem proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování plátna](#configure-canvas-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořit testovacího uživatele plátna](#create-canvas-test-user)** – můžete mít protějšek B. Simon na plátně, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace na **plátně** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování domény plátna a adresy URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.instructure.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pro získání těchto hodnot [tým podpory pro klienty](https://community.canvaslms.com/community/help) s kontaktními osobami. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. V části **podpisový certifikát SAML** kliknutím na tlačítko **Upravit** otevřete dialogové okno **podpisový certifikát SAML** .

    ![Upravit podpisový certifikát SAML](common/edit-certificate.png)

6. V části **podpisový certifikát SAML** zkopírujte **kryptografický otisk** a uložte ho do svého počítače.

    ![Kopírovat hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. V části **nastavit plátno** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k plátnu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **plátno**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-canvas-sso"></a>Konfigurace jednotného přihlašování k plátnu

1. V jiném okně webového prohlížeče se přihlaste k webu plátna jako správce.

2. Přejít na **kurzy \> spravované účty \> Microsoft**.

    ![Plátno](./media/canvas-lms-tutorial/ic775990.png "Plátno")

3. V navigačním podokně na levé straně vyberte **ověřování** a pak klikněte na **Přidat novou konfiguraci SAML**.

    ![Authentication](./media/canvas-lms-tutorial/ic775991.png "Authentication")

4. Na stránce aktuální integrace proveďte následující kroky:

    ![Aktuální integrace](./media/canvas-lms-tutorial/save.png "Aktuální integrace")

    a. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru služby Azure AD** , který jste zkopírovali z Azure Portal.

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **Adresa URL** pro odhlášení vložte hodnotu **adresy URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    d. Do textového pole **změnit odkaz na heslo** vložte hodnotu **Adresa URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.

    e. Do textového pole **otisku certifikátu** vložte hodnotu **kryptografického otisku** certifikátu, který jste zkopírovali z Azure Portal.

    f. V seznamu **atribut přihlášení** vyberte **NameId**.

    například V seznamu **Formát identifikátoru** vyberte možnost **EmailAddress**.

    h. Klikněte na **Uložit nastavení ověřování**.

### <a name="create-canvas-test-user"></a>Vytvořit testovacího uživatele plátna

Aby se uživatelé Azure AD mohli přihlásit k plátnu, musí se zřídit do plátna. V případě plátna je zřizování uživatelů ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k tenantovi **plátna** .

2. Přejít na **kurzy \> spravované účty \> Microsoft**.

   ![Plátno](./media/canvas-lms-tutorial/ic775990.png "Plátno")

3. Klikněte na **Uživatelé**.

   ![Snímek obrazovky s vybranými uživateli zobrazí nabídku plátna.](./media/canvas-lms-tutorial/ic775995.png "Uživatelé")

4. Klikněte na tlačítko **Přidat nového uživatele**.

   ![Snímek obrazovky se zobrazí tlačítko Přidat nového uživatele.](./media/canvas-lms-tutorial/ic775996.png "Uživatelé")

5. Na stránce Přidat nový uživatelský dialog proveďte následující kroky:

   ![Přidat uživatele](./media/canvas-lms-tutorial/ic775997.png "Přidání uživatele")

   a. Do textového pole **celé jméno** zadejte jméno uživatele, jako je **BrittaSimon**.

   b. Do textového pole **e-mail** zadejte e-maily uživatele, jako je **brittasimon \@ contoso.com**.

   c. Do textového pole pro **přihlášení** zadejte e-mailovou adresu Azure AD, jako je **brittasimon \@ contoso.com**.

   d. Vyberte **Odeslat e-mail uživateli o vytvoření tohoto účtu**.

   e. Klikněte na **Add User** (Přidat uživatele).

> [!NOTE]
> K zřizování uživatelských účtů Azure AD můžete použít jakékoli nástroje pro vytváření uživatelských účtů nebo rozhraní API, které poskytuje plátno.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení na plátně, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k plátnu přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici plátna v části Moje aplikace byste se měli automaticky přihlášeni k plátnu, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování plátna můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).