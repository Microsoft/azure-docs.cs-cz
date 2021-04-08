---
title: 'Kurz: Azure Active Directory integrace s Snowflake | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Snowflake.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1af0209265ec4945950120e80a83e19c8ab2eb4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726242"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Kurz: Azure Active Directory integrace s Snowflake

V tomto kurzu se dozvíte, jak integrovat Snowflake s Azure Active Directory (Azure AD). Když integrujete Snowflake s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Snowflake.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Snowflake svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Snowflake potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Snowflakem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

- Snowflake podporuje jednotné přihlašování (SSO) **a IDP** .
- Snowflake podporuje [automatizované zřizování a rušení zřizování uživatelů](snowflake-provisioning-tutorial.md) (doporučeno)

## <a name="adding-snowflake-from-the-gallery"></a>Přidání Snowflake z Galerie

Pokud chcete nakonfigurovat integraci Snowflake do služby Azure AD, musíte přidat Snowflake z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Snowflake** .
1. Na panelu výsledků vyberte **Snowflake** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-snowflake"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Snowflake

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Snowflake pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Snowflake.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Snowflake, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte SNOWFLAKE SSO](#configure-snowflake-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Snowflake Test User](#create-snowflake-test-user)** -to, abyste měli protějšek B. Simon v Snowflake, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **Snowflake** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** :

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované SP:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`
    
    b. Do textového pole **odhlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/logout`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Snowflake](https://support.snowflake.net/s/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Snowflake** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Snowflake.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Snowflake**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-snowflake-sso"></a>Konfigurace jednotného přihlašování Snowflake

1. V jiném okně webového prohlížeče se přihlaste k Snowflake jako správce zabezpečení.

1. **Přepněte roli** na **ACCOUNTADMIN** kliknutím na **profil** v pravé horní části stránky.

    > [!NOTE]
    > To je oddělené od kontextu, který jste vybrali v pravém horním rohu pod vaším uživatelským jménem.
    
    ![Správce Snowflake](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

1. Otevřete **stažený certifikát základní 64** v programu Poznámkový blok. Zkopírujte hodnotu mezi "-----BEGIN CERTIFICATE-----" a "-----END CERTIFICATE-----" a vložte ji do uvozovek vedle **certifikátu** níže. Do pole **ssoUrl** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal. Vyberte **všechny dotazy** a klikněte na **Spustit**.

   ![Snowflake SQL](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

   ```
   use role accountadmin;
   alter account set saml_identity_provider = '{
   "certificate": "<Paste the content of downloaded certificate from Azure portal>",
   "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
   "type":"custom",
   "label":"AzureAD"
   }';
   alter account set sso_login_page = TRUE;
   ```


### <a name="create-snowflake-test-user"></a>Vytvořit testovacího uživatele Snowflake

Aby se uživatelé Azure AD mohli přihlásit k Snowflake, musí se zřídit v Snowflake. V Snowflake je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Snowflake jako správce zabezpečení.

2. **Přepněte roli** na **ACCOUNTADMIN** kliknutím na **profil** v pravé horní části stránky.  

    ![Správce Snowflake](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Vytvořte uživatele spuštěním následujícího dotazu SQL a zajistěte, aby se přihlašovací jméno na listu nastavilo na uživatelské jméno Azure AD, jak je znázorněno níže.

    ![Snowflake adminsql](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

#### <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k Snowflake, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k Snowflake přímo a zahájit tok přihlášení.

#### <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k Snowflake, pro které jste nastavili jednotné přihlašování. 

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici Snowflake v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k Snowflake, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování Snowflake můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)