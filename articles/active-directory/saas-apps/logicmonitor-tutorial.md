---
title: 'Kurz: Azure Active Directory integrace s LogicMonitor | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LogicMonitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d5342782c26b5c274699bacc4ea0c7cdf5b7f880
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649400"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Kurz: Azure Active Directory integrace s LogicMonitor

V tomto kurzu se dozvíte, jak integrovat LogicMonitor s Azure Active Directory (Azure AD). Když integrujete LogicMonitor s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k LogicMonitor.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k LogicMonitor svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s LogicMonitor potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* LogicMonitor odběr povoleného jednotného přihlašování.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* LogicMonitor podporuje jednotné přihlašování iniciované v **SP**

## <a name="add-logicmonitor-from-the-gallery"></a>Přidání LogicMonitor z Galerie

Pokud chcete nakonfigurovat integraci LogicMonitor do služby Azure AD, musíte přidat LogicMonitor z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **LogicMonitor** .
1. Na panelu výsledků vyberte **LogicMonitor** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-logicmonitor"></a>Konfigurace a testování jednotného přihlašování Azure AD pro LogicMonitor

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí LogicMonitor pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v LogicMonitor.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí LogicMonitor postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte LOGICMONITOR SSO](#configure-logicmonitor-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte LogicMonitor Test User](#create-logicmonitor-test-user)** -to, abyste měli protějšek B. Simon v LogicMonitor, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **LogicMonitor** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně LogicMonitor a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.logicmonitor.com`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.logicmonitor.com`
    
    c. Do textového pole **Adresa URL odpovědi (adresa URL služby vyhodnocení příjemce)** zadejte adresu URL pomocí následujícího vzoru: `https://companyname.logicmonitor.com/santaba/saml/SSO/` 
  
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným přihlašovacím jménem a identifikátorem URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta LogicMonitor](https://www.logicmonitor.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení LogicMonitor** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user&quot;></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k LogicMonitor.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **LogicMonitor**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name=&quot;configure-logicmonitor-sso&quot;></a>Konfigurace jednotného přihlašování LogicMonitor

1. Přihlaste se k webu **LogicMonitor** společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení**.

    ![Nastavení](./media/logicmonitor-tutorial/ic790052.png &quot;Nastavení")

3. V navigačním bat na levé straně klikněte na **jednotné přihlašování**.

    ![Jednotné přihlašování](./media/logicmonitor-tutorial/ic790053.png "Jednotné přihlašování")

4. V části **nastavení jednotného přihlašování (SSO)** proveďte následující kroky:

    ![Nastavení jednoho Sign-On](./media/logicmonitor-tutorial/ic790054.png "Nastavení jednoho Sign-On")

    a. Vyberte **Povolit jednotné přihlašování**.

    b. Jako **výchozí přiřazení role** vyberte **jen pro čtení**.

    c. Otevřete stažený soubor metadat v programu Poznámkový blok a vložte obsah souboru do textového pole **metadata poskytovatele identity** .

    d. Klikněte na **Save Changes** (Uložit změny).

### <a name="create-logicmonitor-test-user"></a>Vytvořit testovacího uživatele LogicMonitor

Aby se uživatelé Azure AD mohli přihlásit, musí se zřídit v aplikaci LogicMonitor s použitím jejich Azure Active Directory uživatelských jmen.

**Při konfiguraci zřizování uživatelů proveďte následující kroky:**

1. Přihlaste se k webu LogicMonitor společnosti jako správce.

2. V nabídce v horní části klikněte na **Nastavení** a potom klikněte na **role a uživatelé**.

    ![Role a uživatelé](./media/logicmonitor-tutorial/ic790056.png "Role a uživatelé")

3. Klikněte na **Přidat**.

4. V části **Přidat účet** proveďte následující kroky:

    ![Přidání účtu](./media/logicmonitor-tutorial/ic790057.png "Přidání účtu")

    a. Do příslušných textových polí zadejte **uživatelské jméno**, **e-mail**, **heslo** a **Zadejte heslo** Azure Active Directoryho uživatele, kterého chcete zřídit.

    b. Vyberte **role**, **Zobrazit oprávnění** a **stav**.

    c. Klikněte na **Odeslat**.

> [!NOTE]
> K zřizování Azure Active Directorych uživatelských účtů můžete použít jakékoli jiné nástroje pro vytváření uživatelských účtů LogicMonitor nebo rozhraní API poskytovaná LogicMonitor.

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k LogicMonitor, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k LogicMonitor přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici LogicMonitor v okně moje aplikace, měli byste se automaticky přihlásit k LogicMonitor, pro které jste nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po nakonfigurování LogicMonitor můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).