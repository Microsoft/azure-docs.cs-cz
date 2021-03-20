---
title: 'Kurz: Azure Active Directory integrace se správcem aplikace Adobe Experience Manager | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a aplikací Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653350"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Kurz: Azure Active Directory integrace se správcem prostředí Adobe Experience Manager

V tomto kurzu se dozvíte, jak integrovat správce platformy Adobe Experience pomocí Azure Active Directory (Azure AD). Když integrujete aplikaci Adobe Experience Manager s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k programu Adobe Experience Manager.
* Umožněte uživatelům, aby se do programu Adobe Experience Manager automaticky přihlásili pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné nástroje Adobe Experience Manager jednotného přihlašování (SSO) s povoleným jednotným přihlašováním.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Adobe Experience Manager podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.

* Správce platformy Adobe Experience podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Přidání programu Adobe Experience Manager z Galerie

Chcete-li nakonfigurovat integraci programu Adobe Experience Manager do služby Azure AD, je nutné přidat správce aplikace Adobe Experience Manager z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **program Adobe Experience Manager** .
1. Na panelu výsledků vyberte **správce prostředí Adobe Experience** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Konfigurace a testování jednotného přihlašování služby Azure AD pro správce aplikace Adobe Experience Manager

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí programu Adobe Experience Manager pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve Správci Adobe Experience Manager.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí programu Adobe Experience Manager, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte jednotné přihlašování pro Adobe Experience Manager](#configure-adobe-experience-manager-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte testovacího uživatele v programu Adobe Experience Manager](#create-adobe-experience-manager-test-user)** – abyste měli protějšek Britta Simon ve Správci Adobe Experience Manageru, který je propojený s reprezentací uživatele v Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **správce prostředí Adobe Experience** , najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte jedinečnou hodnotu, kterou definujete i na serveru AEM.

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte hodnotu adresy URL odpovědi skutečnou adresou URL odpovědi. Chcete-li získat tuto hodnotu, obraťte se na [tým podpory pro správce aplikace Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) , který tuto hodnotu získá. Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL serveru aplikace Adobe Experience Manager.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **Nastavení programu Adobe Experience Manager** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k programu Adobe Experience Manager.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **správce prostředí Adobe Experience**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-adobe-experience-manager-sso"></a>Konfigurace jednotného přihlašování k programu Adobe Experience Manager

1. V jiném okně prohlížeče otevřete portál pro správu **nástroje Adobe Experience Manager** .

2. Vyberte **Nastavení**  >  **zabezpečení**  >  **Uživatelé**.

    ![Snímek obrazovky, který zobrazuje dlaždici uživatelé ve správci programu Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Vyberte **správce** nebo jakýkoli jiný příslušný uživatel.

    ![Snímek obrazovky, který zvýrazní uživatele Adminisrator.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Vyberte **Nastavení účtu**  >  **Spravovat TrustStore**.

    ![Snímek obrazovky, který ukazuje správu TrustStore v části nastavení účtu.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. V části **Přidat certifikát ze souboru CER** klikněte na **Vybrat soubor certifikátu**. Vyhledejte a vyberte soubor certifikátu, který jste už stáhli z Azure Portal.

    ![Snímek obrazovky, který zvýrazní tlačítko Vybrat soubor certifikátu](./media/adobe-experience-manager-tutorial/user-2.png)

6. Certifikát se přidá do TrustStore. Poznamenejte si alias certifikátu.

    ![Snímek obrazovky, který ukazuje, že se certifikát přidal do TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Na stránce **Uživatelé** vyberte **ověřování-služba**.

    ![Sreenshot, který zvýrazní ověřování – služba na obrazovce.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Vyberte **Nastavení účtu**  >  **vytvořit/spravovat úložiště klíčů**. Vytvořte úložiště klíčů zadáním hesla.

    ![Snímek obrazovky, který zvýrazní správu úložiště klíčů.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Vraťte se zpátky na obrazovku správce. Pak vyberte **Nastavení**  >  **Operations**  >  **Web Console**.

    ![Snímek obrazovky, který zvýrazní webovou konzolu v rámci operací v části nastavení.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Otevře se stránka konfigurace.

    ![Konfigurovat tlačítko pro uložení jednotného přihlašování](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Najděte **obslužnou rutinu ověřování SAML 2,0 pro Adobe Granite**. Pak vyberte ikonu **Přidat** .

    ![Snímek obrazovky, který zvýrazňuje obslužnou rutinu ověřování SAML 2,0 pro Adobe Granite.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Na této stránce proveďte následující akce.

    ![Tlačítko pro konfiguraci jednoho Sign-On Uložit](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Do pole **cesta** zadejte **/** .

    b. Do pole **Adresa URL IDP** zadejte **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. Do pole **alias certifikátu IDP** zadejte hodnotu **aliasu certifikátu** , kterou jste přidali v TrustStore.

    d. Do pole **ID entity poskytnuté zabezpečení** zadejte jedinečnou hodnotu **identifikátoru Azure AD** , kterou jste nakonfigurovali v Azure Portal.

    e. Do pole **Adresa URL služby pro příjemce kontrolního výrazu** zadejte hodnotu **adresy URL odpovědi** , kterou jste nakonfigurovali v Azure Portal.

    f. Do pole **heslo pro úložiště klíčů** zadejte **heslo** , které jste nastavili v úložišti klíčů.

    například V poli **ID atributu uživatele** zadejte **ID** nebo jiné ID uživatele, které je ve vašem případě relevantní.

    h. Vyberte možnost **autocreate CRX Users**.

    i. Do pole **Adresa URL pro odhlášení** zadejte jedinečnou **adresu URL pro odhlášení** , kterou jste získali z Azure Portal.

    j. Vyberte **Uložit**.

### <a name="create-adobe-experience-manager-test-user"></a>Vytvořit testovacího uživatele pro aplikaci Adobe Experience Manager

V této části vytvoříte uživatele s názvem Britta Simon ve správci aplikace Adobe Experience Manager. Pokud jste vybrali možnost automaticky **vytvořit uživatele CRX** , budou uživatelé po úspěšném ověření automaticky vytvořeni.

Pokud chcete ručně vytvořit uživatele, spolupracujte s [týmem podpory správce aplikace Adobe Experience](https://helpx.adobe.com/support/experience-manager.html) a přidejte uživatele na platformě programu Adobe Experience Manager.

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení správce Adobe Experience, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Adobe Experience Manageru přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k programu Adobe Experience Manager, pro který jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Správce prostředí Adobe Experience v části Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro zahájení toku přihlášení a v případě, že se nakonfiguruje v režimu IDP, měli byste se automaticky přihlásit k programu Adobe Experience Manager, pro který jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování programu Adobe Experience Manager můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).