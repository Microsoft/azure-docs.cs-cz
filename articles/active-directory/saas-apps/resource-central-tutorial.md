---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s využitím prostředků Central | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrálou prostředků.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586624"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s využitím prostředků Central

V tomto kurzu se dozvíte, jak integrovat prostředky Central pomocí Azure Active Directory (Azure AD). Při integraci prostředků Central s Azure AD můžete:

* Řízení ve službě Azure AD, která má přístup k centru zdrojů.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k prostředkům centrálně pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s podporou jednotného přihlašování k prostředkům (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Centrála prostředků podporuje jednotné přihlašování, které iniciuje **SP**

* Centrála prostředků podporuje zřizování uživatelů **jenom v čase** .

## <a name="add-resource-central-from-the-gallery"></a>Přidání zdroje z Galerie do středu

Pokud chcete nakonfigurovat integraci prostředků do Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat z Galerie prostředky střed.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Resource Central**.
1. Z panelu výsledků vyberte **prostředek Central** a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro prostředky Central

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Resource Central pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v centru zdrojů.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD s využitím prostředků Central, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
    1. **[Vytvořte uživatele centrálního testovacího uživatele](#create-resource-central-test-user)** – můžete mít protějšek B. Simon v centru prostředků, který je propojený s reprezentací uživatele Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování prostředků](#configure-resource-central-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace pro **centrální správu prostředků** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

   1. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<DOMAIN_NAME>/ResourceCentral`

   1. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Tyto hodnoty nejsou literálové hodnoty. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pro získání těchto hodnot se obraťte na [tým podpory pro klienta podpory prostředků](mailto:st@aod.vn) .  Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v **podpisovém certifikátu SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení centrálního Resource** zkopírujte příslušné adresy URL na základě vašeho požadavku.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte `username@companydomain.extension` . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k centru zdrojů.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **prostředky Central**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V podokně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v části **Vybrat roli**. Pokud pro tuto aplikaci nebyla nastavena žádná role, zobrazí se vybraná role **výchozí přístup** .
1. V podokně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-resource-central-test-user"></a>Vytvořit uživatele centrálního testu prostředků

V této části se v **Centru zdrojů** vytvoří uživatel s názvem **B. Simon** .

1. V centru zdrojů vyberte možnost **zabezpečení**  >  **osoby**  >  **nové**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Snímek obrazovky zobrazující podokno osoby v centru zdrojů s zvýrazněným novým tlačítkem":::

1. V části **Podrobnosti o osobě** zadejte pro **zobrazované jméno** uživatele **B. Simon**. Do pole **adresa SMTP** zadejte uživatelské jméno uživatele Azure AD. Například `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Snímek obrazovky, který zobrazuje podokno podrobností o osobě v centru zdrojů":::

## <a name="configure-resource-central-sso"></a>Konfigurace centrálního přihlašování prostředků

V této části nakonfigurujete jednotné přihlašování ve **Správci prostředků centrálního systému**.

1. V nástroji Správce centrálního systému prostředků vyberte **externí ověřování**.
1.  V **nastavení povolit konfiguraci** vyberte **Ano**.

    ![Snímek obrazovky, který ukazuje možnost povolit konfiguraci vybranou v podokně externí ověřování v centru zdrojů](./media/resource-central/enable.png)

1. V **ověřovacím protokolu** vyberte **typu Saml2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Snímek obrazovky, který zobrazuje typu Saml2 vybrané pro protokol ověřování v centru zdrojů":::

1. V části **Konfigurace typu Saml2** zadejte hodnoty následujících polí:

    1. V poli **identifikátor (ID entity)**, **přihlašovací adresa URL**, **Adresa URL pro odhlášení** a **identifikátor služby Azure AD** zadejte příslušné adresy URL:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Snímek obrazovky s podoknem konfigurace typu Saml2 v centru zdrojů":::

        Zkopírujte adresy URL z podokna **nastavit centrální podokno prostředků** :

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Snímek obrazovky nastavení centrálního podokna prostředků v centru zdrojů":::

   1. Jako **návratovou adresu URL** zadejte `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. V případě **certifikátu** Nahrajte certifikát a zadejte heslo.

   ![Snímek obrazovky oddílu certifikátu v centru zdrojů](./media/resource-central/cert.png)
   
1. Vyberte **Uložit**.

1. Vraťte se na **Azure Portal**. V **podpisovém certifikátu SAML** Nahrajte certifikát a zadejte heslo.

   ![Snímek obrazovky s podoknem importovat certifikát v Azure Portal.](./media/resource-central/cert2.png).

1. Vyberte **Přidat**.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD. K otestování jednotného přihlašování máte tři možnosti:

* V Azure Portal vyberte možnost **testovat tuto aplikaci**. Odkaz přesměruje na adresu URL centrálního přihlašování prostředků, kde můžete zahájit přihlášení.

* Přímo na adrese URL pro přihlášení k prostředkům přihlaste a zahajte přihlášení.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Snímek webové stránky pro testování centrálního jednotného přihlašování pro prostředky":::

* Použijte portál moje aplikace od Microsoftu. Na portálu moje aplikace vyberte centrální dlaždici **prostředků** pro přesměrování na adresu URL centrálního přihlašování prostředků. Další informace najdete v tématu [přihlášení a spouštění aplikací na portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nastavení centrálního přihlašování pro jednotné přihlašování pomocí Azure AD můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
