---
title: 'Kurz: Azure Active Directory integrace s ArcGIS Online | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a ArcGIS Online.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 7a445eefa31e741562105e89fa105d404ccc0c7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646346"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Kurz: Azure Active Directory integrace s ArcGIS Online

V tomto kurzu se dozvíte, jak integrovat ArcGIS Online s Azure Active Directory (Azure AD). Když integrujete ArcGIS Online s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k ArcGIS Online
* Umožněte uživatelům, aby se automaticky přihlásili k ArcGIS Online pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné ArcGIS Online jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* ArcGIS Online podporuje jednotné přihlašování iniciované v **SP** .

## <a name="add-arcgis-online-from-the-gallery"></a>Přidání ArcGIS Online z Galerie

Pokud chcete nakonfigurovat integraci ArcGIS Online do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat ArcGIS Online z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **ArcGIS Online** .
1. Na panelu výsledků vyberte **ArcGIS Online** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-online"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro ArcGIS Online

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí ArcGIS Online s použitím testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v ArcGIS Online.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí ArcGIS Online, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte ARCGIS jednotné](#configure-arcgis-online-sso)** přihlašování online – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte online testovacího uživatele ArcGIS](#create-arcgis-online-test-user)** , abyste měli protějšek B. Simon v ArcGIS Online, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce online integrace aplikací **ArcGIS** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.maps.arcgis.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, kontaktujte [tým podpory online klientů ArcGIS](https://support.esri.com/en/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. Pokud chcete automatizovat konfiguraci v rámci služby **ArcGIS Online**, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![image](./media/arcgis-tutorial/install-extension.png)

7. Po přidání rozšíření do prohlížeče klikněte na **Nastavení ArcGIS Online** a přesměrujte vás na online aplikaci ArcGIS. Odtud zadejte přihlašovací údaje správce pro přihlášení k ArcGIS Online. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky v části **Konfigurace jednotného přihlašování ArcGIS Online**.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k ArcGIS Online.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **ArcGIS Online**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-arcgis-online-sso"></a>Konfigurace online jednotného přihlašování ArcGIS

1. Pokud chcete nastavit ArcGIS Online ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu ArcGIS společnosti jako správce a proveďte následující kroky:

2. Klikněte na **Upravit nastavení**.

    ![Upravit nastavení](./media/arcgis-tutorial/settings.png "Upravit nastavení")

3. Klikněte na **zabezpečení**.

    ![Zabezpečení](./media/arcgis-tutorial/secure.png "Zabezpečení")

4. V části **Podniková přihlášení** klikněte na **nastavit poskytovatele identity**.

    ![Podniková přihlášení](./media/arcgis-tutorial/enterprise.png "Podniková přihlášení")

5. Na stránce **nastavit konfiguraci zprostředkovatele identity** proveďte následující kroky:

    ![Nastavit zprostředkovatele identity](./media/arcgis-tutorial/identity-provider.png "Nastavit zprostředkovatele identity")

    a. Do textového pole **název** zadejte název vaší organizace.

    b. **Metadata pro podnikového zprostředkovatele identity se doplní pomocí**, vyberte **soubor**.

    c. Pokud chcete nahrát stažený soubor metadat, klikněte na **zvolit soubor**.

    d. Klikněte na **nastavit poskytovatele identity**.

### <a name="create-arcgis-online-test-user"></a>Vytvořit online testovacího uživatele ArcGIS

Aby se uživatelé Azure AD mohli přihlásit k ArcGIS Online, musí se zřídit do ArcGIS Online.  
V případě ArcGIS Online je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému tenantovi **ArcGIS** .

2. Klikněte na **pozvat členy**.

    ![Pozvat členy](./media/arcgis-tutorial/invite.png "Pozvat členy")

3. Vyberte možnost **přidat členy automaticky bez odeslání e-mailu** a potom klikněte na tlačítko **Další**.

    ![Přidat členy automaticky](./media/arcgis-tutorial/members.png "Přidat členy automaticky")

4. Na stránce dialog **Členové** proveďte následující kroky:

    ![Přidat a zkontrolovat](./media/arcgis-tutorial/review.png "Přidat a zkontrolovat")

     a. Zadejte **e-mail**, **jméno** a **příjmení** platného účtu Azure AD, který chcete zřídit.

     b. Klikněte na **Přidat a zkontrolovat**.
5. Zkontrolujte data, která jste zadali, a pak klikněte na **přidat členy**.

    ![Přidat člena](./media/arcgis-tutorial/add.png "Přidat člena")

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na ArcGIS adresu URL pro přihlášení do online režimu, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL online přihlašování na ArcGIS a spustit tok přihlášení přímo z tohoto účtu.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici ArcGIS Online v nabídce Moje aplikace, přesměruje se na ArcGIS Online přihlašovací adresu URL. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování ArcGIS Online můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).