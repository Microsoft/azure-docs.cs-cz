---
title: 'Kurz: Azure Active Directory integrace se službou Marketo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Marketo.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 433303bf0d51eff3bd3ab37726c9e98e8a766d25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686950"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Azure Active Directory integrace se službou Marketo

V tomto kurzu se naučíte integrovat Marketo s Azure Active Directory (Azure AD).
Integrování Marketo se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Marketo.
* Uživatelům můžete povolit, aby se automaticky přihlásili ke službě Marketo (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD se službou Marketo potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním na úrovni Market

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Marketo podporuje **IDP** jednotné přihlašování.

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-marketo-from-the-gallery"></a>Přidání Marketo z Galerie

Pokud chcete nakonfigurovat integraci služby Marketo do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Marketo z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Marketo** .
1. Z panelu výsledků vyberte **Marketo** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso-for-marketo"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Marketo

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se službou Marketo na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve službě Marketo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Marketo, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD s Britta Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování služby Azure AD.
2. **[Konfigurace služby Marketo SSO](#configure-marketo-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořte testovacího uživatele Marketo](#create-marketo-test-user)** , abyste měli protějšek Britta Simon ve službě Marketo, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Marketo** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL: `https://saml.marketo.com/sp`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://login.marketo.com/saml/assertion/<munchkinid>`

    c. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a stavu přenosu. Pro získání těchto hodnot kontaktujte [tým podpory Marketo pro klienty](https://investors.marketo.com/contactus.cfm) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Marketo** zkopírujte příslušné adresy URL podle vašich požadavků.

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

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Marketo.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Marketo**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-marketo-sso"></a>Konfigurace jednotného přihlašování k Marketosti

1. Pokud chcete automatizovat konfiguraci v rámci služby Marketo, je potřeba nainstalovat **rozšíření prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **Nastavení Marketo** , které vás přesměruje do aplikace Marketo. Odtud zadejte přihlašovací údaje správce pro přihlášení ke službě Marketo. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-6.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete ručně nastavit Marketo, v jiném okně webového prohlížeče se přihlaste ke svojí webu Marketo společnosti jako správce.

1. Pokud chcete získat ID Munchkin vaší aplikace, proveďte následující akce:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Konfigurace jednoho Sign-On1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na **odkaz Munchkin**.
   
    ![Konfigurace jednoho Sign-On2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Zkopírujte ID Munchkin zobrazené na obrazovce a dokončete adresu URL odpovědi v Průvodci konfigurací služby Azure AD.
   
    ![Konfigurace jednoho Sign-On3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. K nakonfigurování jednotného přihlašování v aplikaci použijte následující postup:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Konfigurace jednoho Sign-On4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky integrace a klikněte na **jednotné přihlašování**.
   
    ![Konfigurace jednoho Sign-On5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Chcete-li povolit nastavení SAML, klikněte na tlačítko **Upravit** .
   
    ![Konfigurace jednoho Sign-On6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Povoleno** Nastavení jednoho Sign-On.
   
    f. Do textového pole **ID vystavitele** vložte **identifikátor Azure AD**.
   
    například Do textového pole **ID entity** zadejte adresu URL `http://saml.marketo.com/sp` .
   
    h. Vyberte umístění ID uživatele jako **element identifikátoru názvu**.
   
    ![Konfigurace jednoho Sign-On7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Pokud váš identifikátor uživatele není hodnota hlavního názvu uživatele (UPN), změňte hodnotu na kartě atribut.
   
    i. Nahrajte certifikát, který jste si stáhli z Průvodce konfigurací služby Azure AD. **Uložte** nastavení.
   
    j. Upravte nastavení přesměrování stránek.
   
    k. Vložte **přihlašovací adresu URL** do textového pole **přihlašovací adresa URL** .
   
    l. Do textového pole **Adresa URL pro odhlášení** vložte **adresu URL pro odhlášení** .
   
    m. V **chybové adrese URL** zkopírujte **adresu URL instance Marketo** a kliknutím na **Uložit** uložte nastavení.
   
    ![Konfigurace jednoho Sign-On8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Pokud chcete povolit jednotné přihlašování pro uživatele, proveďte následující akce:
   
    a. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.
   
    b. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![Konfigurace jednoho Sign-On9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Přejděte do nabídky **zabezpečení** a klikněte na tlačítko **Nastavení přihlášení**.
   
    ![Konfigurace jednoho Sign-On10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Ověřte možnost **vyžadovat jednotné přihlašování** a **uložte** nastavení.
   
    ![Konfigurace jednoho Sign-On11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Vytvořit testovacího uživatele Marketo

V této části vytvoříte uživatele s názvem Britta Simon ve Marketo. pomocí těchto kroků můžete vytvořit uživatele na Market platformě.

1. Přihlaste se do aplikace Marketo pomocí přihlašovacích údajů správce.

2. Klikněte na tlačítko **správce** v horním navigačním podokně.
   
    ![test user1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Přejděte do nabídky **zabezpečení** a klikněte na **Uživatelé & rolí** .
   
    ![test uživatel2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klikněte na odkaz **pozvat nového uživatele** na kartě Uživatelé.
   
    ![user3 testu](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. V průvodci pozvání nového uživatele vyplňte následující informace.
   
    a. Do textového pole zadejte **e-mailovou** adresu uživatele.
   
    ![user4 testu](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Zadejte **jméno** do textového pole.
   
    c. Zadejte **příjmení**  do textového pole.
   
    d. Klikněte na **Další** .

6. Na kartě **oprávnění** vyberte **položka userroles může** a klikněte na **Další** .
   
    ![user5 testu](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kliknutím na tlačítko **Odeslat** odešlete pozvánku uživateli.
   
    ![user6 testu](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Uživatel dostane e-mailové oznámení a musí kliknout na odkaz a změnit heslo pro aktivaci účtu. 

### <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností.

* Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni ke službě Marketo, pro kterou jste si nastavili jednotné přihlašování.

* Můžete použít aplikaci Microsoft moje aplikace. Po kliknutí na dlaždici Marketo v okně moje aplikace byste se měli automaticky přihlášeni ke službě Marketo, pro kterou jste si nastavili jednotné přihlašování. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Další kroky

Po konfiguraci služby Marketo můžete vyhovět řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).