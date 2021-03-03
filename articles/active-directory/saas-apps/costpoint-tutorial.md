---
title: 'Kurz: Azure Active Directory integrace s Costpoint | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 781d1509b69ecb09189772f4a31d4872201d0b2e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652925"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Kurz: integrace Costpoint s Azure Active Directory

V tomto kurzu se dozvíte, jak integrovat Costpoint s Azure Active Directory (Azure AD). Když integrujete Costpoint s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Costpoint.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Costpoint svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Costpoint odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí. 

* Costpoint podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

## <a name="generate-costpoint-metadata"></a>Generovat metadata Costpoint

Costpoint konfigurace jednotného přihlašování SAML se vysvětluje v příručce pro **DeltekCostpoint711Security.pdf** . Tento průvodce si můžete stáhnout z webu podpory Deltek Costpoint a přečtěte si téma **instalace jednotného přihlašování SAML**  >  **Konfigurace jednotného přihlašování SAML mezi Costpoint a Microsoft Azure** . Postupujte podle pokynů a vygenerujte soubor **XML federačních metadat pro COSTPOINT SP** . 

![Snímek obrazovky zobrazující, že nástroj pro konfiguraci produktu má vybranou kartu WebLogic-Security.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Přidání Costpoint z Galerie

Pokud chcete nakonfigurovat integraci Costpoint do služby Azure AD, musíte přidat Costpoint z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Costpoint** .
1. Na panelu výsledků vyberte **Costpoint** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Costpoint

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Costpoint pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Costpoint.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí Costpoint postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte COSTPOINT SSO](#configure-costpoint-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Costpoint Test User](#create-costpoint-test-user)** -to, abyste měli protějšek B. Simon v Costpoint, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. Na stránce integrace aplikací **Costpoint** vyberte **jednotné přihlašování**.

1. V části **základní konfigurace SAML** Pokud máte **soubor metadat poskytovatele služby**, proveďte tyto kroky:

   > [!NOTE]
   > Soubor metadat poskytovatele služeb získáte v části [generování metadat Costpoint](#generate-costpoint-metadata). Použití souboru je vysvětleno dále v tomto kurzu.
 
   1. Vyberte tlačítko **nahrát soubor metadat** a pak vyberte soubor **XML s metadaty Costpoint SP** , který dřív vygenerovala Costpoint, a pak vyberte tlačítko **Přidat** , aby se soubor nahrál.

      ![Nahrání souboru metadat](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Po úspěšném nahrání souboru metadat se hodnoty **adresy URL** **identifikátoru** a odpovědi vyplní automaticky v části Costpoint.

      > [!NOTE]
      > Pokud hodnoty **adresy URL** **identifikátoru** a odpovědi nejsou automaticky polulated, zadejte hodnoty ručně podle vašich požadavků. Ověřte, že je správně nastavený **identifikátor (ID entity)** a **Adresa URL odpovědi (adresa URL služby pro příjemce kontrolního výrazu)** a že **Adresa URL služby ACS** je platná adresa URL Costpoint, která končí na **/LoginServlet.CPS**.

   1. Vyberte **nastavit další adresy URL**. Do pole **stav přenosu** zadejte hodnotu pomocí následujícího vzoru: `system=[your system]` (například **System = DELTEKCP**).

1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte v části **podpisový certifikát SAML** ikonu **kopírování** a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do poznámkového bloku.

   ![Podpisový certifikát SAML](common/copy-metadataurl.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Costpoint.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Costpoint**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-costpoint-sso"></a>Konfigurace jednotného přihlašování Costpoint

1. Vraťte se do konfiguračního nástroje Costpoint. Do textového pole **XML federačních metadat IDP** vložte obsah souboru *adresy URL federačních metadat aplikace* . 

   ![Nástroj pro konfiguraci Costpoint](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Pokračujte v pokynech průvodce **DeltekCostpoint711Security.pdf** a dokončete instalaci Costpoint SAML.

### <a name="create-costpoint-test-user"></a>Vytvořit testovacího uživatele Costpoint

V této části vytvoříte uživatele v Costpoint. Předpokládejme, že ID uživatele je **b. Simon** a jméno uživatele je **b. Simon**. Pokud chcete přidat uživatele na platformě Costpoint, pracujte s [týmem podpory klienta Costpoint](https://www.deltek.com/about/contact-us) . Aby bylo možné použít jednotné přihlašování, musí být uživatel vytvořen a aktivován.

Po vytvoření uživatele musí být výběr **metody ověřování** uživatele ve **službě Active Directory**, musí být zaškrtnuto políčko **jednotné přihlašování SAML** a uživatelské jméno z Azure Active Directory musí být **Služba Active Directory nebo ID certifikátu** (zobrazené na následujícím snímku obrazovky).

![Uživatel Costpoint](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Costpoint, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Costpoint přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Costpoint, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Costpoint v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Costpoint, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování Costpoint můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).