---
title: 'Kurz: Azure Active Directory integrace s MobileIron | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a MobileIron.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 5561a4cdeef725eba7e48d7767aa0ee5d3c6d9cf
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625444"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Kurz: Azure Active Directory integrace s MobileIron

 V tomto kurzu se dozvíte, jak integrovat MobileIron s Azure Active Directory (Azure AD). Když integrujete MobileIron s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k MobileIron.
* Umožněte uživatelům, aby se automaticky přihlásili k MobileIron pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s MobileIron potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným MobileIronm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* MobileIron podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="add-mobileiron-from-the-gallery"></a>Přidání MobileIron z Galerie

Pokud chcete nakonfigurovat integraci MobileIron do služby Azure AD, musíte přidat MobileIron z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém podokně vyberte **Azure Active Directory**.
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **MobileIron** .
1. Z výsledků vyberte **MobileIron** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>Konfigurace a testování jednotného přihlašování Azure AD pro MobileIron

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí MobileIron pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v MobileIron.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí MobileIron postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
     1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
2. **[Nakonfigurujte MOBILEIRON SSO](#configure-mobileiron-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvoření MobileIron Test User](#create-mobileiron-test-user)** – pro Britta Simon v MobileIron, který je propojený s reprezentací uživatele Azure AD.
6. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.
 
1. V Azure Portal na stránce integrace aplikací **MobileIron** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **vybrat jednu Sign-On metodu** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML vyberte ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky, pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** :

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.MobileIron.com/<key>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<host>.MobileIron.com/saml/SSO/alias/<key>`

    c. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

     Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<host>.MobileIron.com/user/login.html`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Hodnoty klíč a Host získáte z portálu pro správu MobileIron, který je vysvětlen dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom heslo zapište.
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k MobileIron.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **MobileIron**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte možnost **Přidat uživatele**. Pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelů položku **B. Simon** . Pak zvolte **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-mobileiron-sso"></a>Konfigurace jednotného přihlašování MobileIron

1. V jiném okně webového prohlížeče se přihlaste k webu MobileIron společnosti jako správce.

2.   >   V poli **informace o nastavení cloudu IDPu** vyberte možnost AAD a vyberte možnost **AAD** .

    ![Snímek obrazovky se zobrazí karta pro správu MobileIron webu s vybranou identitou.](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. Zkopírujte hodnoty **klíč** a **Host** a vložte je, abyste dokončili adresy URL v **základní části Konfigurace SAML** v Azure Portal.

    ![Snímek obrazovky s nastavením klíče a hodnoty hostitele zobrazuje možnost nastavení SAML.](./media/MobileIron-tutorial/key.png)

4. V **poli exportovat soubor metadat z AAD a importovat do MobileIron cloudu** klikněte na **zvolit soubor** a nahrajte stažená metadata z Azure Portal. Po nahrání klikněte na **Hotovo** .

    ![Tlačítko pro konfiguraci jednoho Sign-On metadat správců](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


##  <a name="create-mobileiron-test-user"></a>Vytvořit testovacího uživatele MobileIron

Aby se uživatelé Azure AD mohli přihlásit k MobileIron, musí se zřídit v MobileIron.  
V případě MobileIron je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu MobileIron společnosti jako správce.

1. Přejděte na **Uživatelé** a klikněte na **Přidat**  >  **jednoho uživatele**.

    ![Tlačítko pro konfiguraci jednoho Sign-On uživatele](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. Na stránce **"jeden uživatel"** proveďte následující kroky:

    ![Tlačítko pro přidání uživatele s jedním Sign-On](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. Do textového pole **e-mailová adresa** zadejte e-maily uživatele jako brittasimon@contoso.com .

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například Britta.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je Simon.

    d. Klikněte na **Hotovo**.

## <a name="test-sso"></a>Test SSO
V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

## <a name="sp-initiated"></a>Zahájena SP:

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k MobileIron, kde můžete spustit tok přihlášení.  

* Přejít na adresu URL pro přihlášení k MobileIron přímo a zahájit tok přihlášení.

##  <a name="idp-initiated"></a>Iniciované IDP:

* Klikněte na **testovat tuto aplikaci** v Azure Portal a měli byste se automaticky přihlášeni k MobileIron, pro které jste nastavili jednotné přihlašování.

K otestování aplikace v jakémkoli režimu můžete také použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici MobileIron v nabídce Moje aplikace, pokud je nakonfigurovaná v režimu SP, budete přesměrováni na přihlašovací stránku aplikace pro inicializaci toku přihlášení a pokud je nakonfigurovaná v režimu IDP, měli byste se automaticky přihlásit k MobileIron, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování MobileIron můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
