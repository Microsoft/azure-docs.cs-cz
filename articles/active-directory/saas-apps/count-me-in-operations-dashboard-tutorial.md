---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s počtem mě v provozním řídicím panelu | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a počet já na řídicím panelu v rámci provozu.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/30/2020
ms.author: jeedes
ms.openlocfilehash: 3339516193af6e1ff832ac586f4a81f8799c5b83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-count-me-in---operations-dashboard"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s počtem mě v Operations Dashboard

V tomto kurzu se dozvíte, jak pomocí Azure Active Directory (Azure AD) integrovat do řídicího panelu počet já v provozu. Při integraci funkce počet já na řídicím panelu s Azure AD můžete provádět tyto akce:

* Řízení ve službě Azure AD, která má přístup k čítači mě v provozním řídicím panelu.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k počtu mnou probíhajících řídicích panelů s účty Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Počet já předplatného jednotného přihlašování v Operations řídicím panelu (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Počet mnou spouštěný řídicím panelem podporuje jednotné přihlašování **SP** iniciované.

## <a name="adding-count-me-in---operations-dashboard-from-the-gallery"></a>Přidání počtu já do řídicího panelu v rámci provozu z Galerie

Pokud chcete nakonfigurovat integraci funkce počet já v rámci provozní řídicího panelu do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat z Galerie příkaz počet mě v rámci provozní řídicího panelu.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **počet já moje provozní řídicí panel** .
1. Na panelu výsledků vyberte možnost **počet mě v rámci provozu** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-count-me-in---operations-dashboard"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro počet mnou probíhajících operací na řídicím panelu

Pomocí testovacího uživatele s názvem **B. Simon** nakonfigurujte a otestujte jednotné přihlašování Azure AD s využitím tohoto počtu operací na řídicím panelu. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v řídicím panelu počet mnou probíhajících operací.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí řídicího panelu počet já v provozu, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte In-Operations přihlašovacích panelů pro řídicí panel](#configure-count-me-in-operations-dashboard-sso)** , abyste mohli konfigurovat nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořit počet mě In-Operations testovací uživatel řídicího panelu](#create-count-me-in-operations-dashboard-test-user)** – Pokud chcete mít protějšek B. Simon na řídicím panelu počet já v provozu, který je propojený s reprezentací uživatele Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce pro integraci aplikace na **řídicím panelu počet po provozu** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `api-us.localz.io/<PROJECT_ID>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://api-us.localz.io/user/v1/saml/initsso?projectId=<PROJECT_ID>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Počet kontaktů, které [mi tým podpory klientů podporuje v Operations Dashboard](mailto:support@localz.co) , získá tyto hodnoty. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Počet já v rámci aplikace řídicího panelu v Operations se očekává kontrolní výraz SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů.

    ![image](common/default-attributes.png)

1. Kromě výše očekává aplikace na řídicím panelu počet já, kdy se v odpovědi SAML vrátí několik atributů, které jsou uvedené dál. Tyto atributy jsou také předem vyplněné, ale můžete je zkontrolovat podle vašich požadavků.
    
    | Name |  Zdrojový atribut|
    | ----------- | --------- |
    | přiřazené role | User. assignedroles |

    > [!NOTE]
    > Počet já v operačním řídicím panelu očekává role pro uživatele přiřazené k aplikaci. Nastavte prosím tyto role ve službě Azure AD, aby bylo možné uživatelům přiřadit příslušné role. Informace o tom, jak nakonfigurovat role v Azure AD, najdete [tady](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **nastavit počet mnou v provozním řídicím panelu** zkopírujte příslušné adresy URL na základě vašeho požadavku.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k počtu mě v provozním řídicím panelu.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **počet mě na řídicím panelu**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-count-me-in-operations-dashboard-sso"></a>Nakonfigurovat počet já In-Operations jednotné přihlašování k řídicímu panelu

Chcete-li nakonfigurovat jednotné přihlašování na straně **řídicího panelu v poli Počet mnou v rámci operace** , je třeba odeslat stažený **certifikát (Base64)** a příslušné zkopírované adresy URL z Azure Portal, aby se mohl [Vypočítat tým podpory řídicího panelu v rámci provozu](mailto:support@localz.co). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-count-me-in-operations-dashboard-test-user"></a>Vytvořit počet já In-Operations testovací uživatel řídicího panelu

V této části vytvoříte uživatele s názvem Britta Simon na řídicím panelu počet já v provozu. Pokud chcete přidat uživatele na platformě na řídicím panelu počet mnou v provozu, pracujte s [počtem já v Operations Dashboard](mailto:support@localz.co) . Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení řídicího panelu počet operací, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k místnímu řídicímu panelu v rámci operace přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici řídicí panel počet po operacích v okně moje aplikace, přesměruje se na přihlašování na základě přihlašovacích adres řídicího panelu v rámci operace. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování řídicího panelu počet já po provozu můžete vyhovět řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
