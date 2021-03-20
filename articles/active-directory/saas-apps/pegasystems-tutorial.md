---
title: 'Kurz: Azure Active Directory integrace se systémy PEGA | Microsoft Docs'
description: V tomto kurzu se dozvíte, jak nakonfigurovat jednotné přihlašování mezi systémy Azure Active Directory a PEGA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 802bd61d499f64a128a4d1c0585363cb1962f8a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649997"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Azure Active Directory integrací se systémy PEGA

V tomto kurzu se dozvíte, jak integrovat systémy PEGA s Azure Active Directory (Azure AD). Když integrujete systémy PEGA s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k systémům PEGA.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k PEGA systémům pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO) PEGA Systems.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Systémy PEGA podporují jednotné přihlašování (SSO) iniciované v SP a IdP.

## <a name="add-pega-systems-from-the-gallery"></a>Přidání systémů PEGA z Galerie

Pokud chcete nakonfigurovat integraci systémů PEGA do služby Azure AD, musíte přidat systémy PEGA z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **PEGA Systems** .
1. Na panelu výsledků vyberte **PEGA systémy** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Konfigurace a testování jednotného přihlašování Azure AD pro systémy PEGA

Nakonfigurujte a otestujte jednotné přihlašování Azure AD se systémy PEGA pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v PEGA systémech.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD se systémy PEGA, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace systémů PEGA SSO](#configure-pega-systems-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele PEGA systémy](#create-pega-systems-test-user)** – pro pokaždé, když máte protějšek B. Simon v systémech PEGA, které jsou propojené s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **PEGA Systems** vyhledejte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované IdP, proveďte v dialogovém okně **základní konfiguraci SAML** následující kroky.

    ![Základní dialogové okno Konfigurace SAML](common/idp-intiated.png)

    1. Do pole **identifikátor** zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL v tomto vzoru:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Chcete-li nakonfigurovat aplikaci v režimu iniciované SP, vyberte možnost **nastavit další adresy URL** a proveďte následující kroky.

    ![Informace o jednotném přihlašování PEGA systémů a adres URL](common/both-advanced-urls.png)

    1. Do pole **přihlašovací adresa URL** zadejte hodnotu adresy URL pro přihlášení.

    1. Do pole **stav přenosu** zadejte adresu URL v tomto vzoru: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Zde uvedené hodnoty jsou zástupné symboly. Musíte použít skutečný identifikátor, adresu URL odpovědi, adresu URL pro přihlášení a adresu URL stavu přenosu. Hodnoty adresy URL pro odpovědi můžete získat z aplikace PEGA, jak je vysvětleno dále v tomto kurzu. Pokud chcete získat hodnotu stavu přenosu, obraťte se na [tým podpory PEGA Systems](https://www.pega.com/contact-us). Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Aplikace PEGA Systems potřebuje, aby byly kontrolní výrazy SAML v konkrétním formátu. Pokud je chcete získat ve správném formátu, je nutné přidat mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje výchozí atributy. Výběrem ikony **Upravit** otevřete dialogové okno **atributy uživatele** :

    ![Atributy uživatele](common/edit-attribute.png)

7. Kromě atributů uvedených na předchozím snímku obrazovky vyžaduje aplikace PEGA Systems několik dalších atributů, které se vrátí zpět v odpovědi SAML. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** proveďte následující kroky a přidejte tyto atributy tokenu SAML:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Tyto hodnoty jsou specifické pro vaši organizaci. Zadejte odpovídající hodnoty.

    1. Výběrem možnosti **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** :

    ![Vyberte Přidat novou deklaraci identity.](common/new-save-attribute.png)

    ![Dialogové okno Spravovat deklarace identity uživatelů](common/new-attribute-details.png)

    1. Do pole **název** zadejte název atributu zobrazený pro tento řádek.

    1. Pole **oboru názvů** nechejte prázdné.

    1. Jako **zdroj** vyberte **atribut**.

    1. V seznamu **zdrojový atribut** vyberte hodnotu atributu zobrazenou pro tento řádek.

    1. Vyberte **OK**.

    1. Vyberte **Uložit**.

8. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** vyberte odkaz **ke stažení** vedle **metadat federace XML** podle vašich požadavků a uložte certifikát do počítače:

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

9. V části **Nastavení systémů PEGA** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat konfigurační adresy URL](common/copy-configuration-urls.png)

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k PEGA systémům.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte možnost **systémy PEGA**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="configure-pega-systems-sso"></a>Konfigurace jednotného přihlašování systémů PEGA

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně **systémů PEGA** , přihlaste se k portálu PEGA pomocí účtu správce v jiném okně prohlížeče.

2. Vyberte **vytvořit**  >    >  **ověřovací službu** sysadmin:

    ![Vybrat ověřovací službu](./media/pegasystems-tutorial/admin.png)
    
3. Na obrazovce **Vytvoření ověřovací služby** proveďte následující kroky.

    ![Obrazovka pro vytvoření ověřovací služby](./media/pegasystems-tutorial/admin1.png)

    1. V seznamu **typ** vyberte **SAML 2,0**.

    1. Do pole **název** zadejte libovolný název (například **Azure AD SSO**).

    1. Do pole **krátký popis** zadejte popis.  

    1. Vyberte **vytvořit a otevřít**.
    
4. V části **informace o poskytovateli identity (IDP)** vyberte **importovat metadata IDP** a vyhledejte soubor metadat, který jste stáhli z Azure Portal. Kliknutím na **Odeslat** načtěte metadata:

    ![IdP (informace o poskytovateli identity) – oddíl](./media/pegasystems-tutorial/admin2.png)
    
    Import naplní data IdP, jak je znázorněno zde:

    ![Importovaná data IdP](./media/pegasystems-tutorial/idp.png)
    
6. V části **Nastavení poskytovatele služeb (SP)** proveďte následující kroky.

    ![Nastavení poskytovatele služeb](./media/pegasystems-tutorial/sp.png)

    1. Zkopírujte hodnotu **Identifikace entity** a vložte ji do pole **identifikátor** v **základní části Konfigurace SAML** v Azure Portal.

    1. Zkopírujte hodnotu **umístění služby vyhodnocení uživatele (ACS)** a vložte ji do pole **Adresa URL odpovědi** v části **základní konfigurace SAML** v Azure Portal.

    1. Vyberte **zakázat podepisování požadavků**.

7. Vyberte **Uložit**.

### <a name="create-pega-systems-test-user"></a>Vytvořit testovacího uživatele pro systémy PEGA

Dále musíte vytvořit uživatele s názvem Britta Simon v systémech PEGA. Pracujte s [týmem podpory PEGA Systems](https://www.pega.com/contact-us) , aby mohli vytvářet uživatele.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL PEGA systémů, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlašování k PEGA systémům přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k systémům PEGA, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici PEGA systémy v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k systémům PEGA, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování systémů PEGA můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).