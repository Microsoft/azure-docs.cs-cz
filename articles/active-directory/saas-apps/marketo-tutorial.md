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
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: fc98201ac97409edd9be8522532728d7bb9b15af
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021397"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Kurz: Azure Active Directory integrace se službou Marketo

V tomto kurzu se naučíte integrovat Marketo s Azure Active Directory (Azure AD).
Integrování Marketo se službou Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Marketo.
* Uživatelům můžete povolit, aby se automaticky přihlásili ke službě Marketo (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD se službou Marketo potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným jednotným přihlašováním na úrovni Market

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Marketo podporuje **IDP** jednotné přihlašování.

## <a name="adding-marketo-from-the-gallery"></a>Přidání Marketo z Galerie

Pokud chcete nakonfigurovat integraci služby Marketo do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat Marketo z galerie.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **Marketo** .
1. Z panelu výsledků vyberte **Marketo** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD se službou Marketo na základě testovacího uživatele s názvem **Britta Simon**.
Aby bylo jednotné přihlašování fungovat, je třeba vytvořit odkaz na propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem ve službě Marketo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí služby Marketo, proveďte následující kroky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD s Britta Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování služby Azure AD.
2. **[Konfigurace služby Marketo SSO](#configure-marketo-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    * **[Vytvořte testovacího uživatele Marketo](#create-marketo-test-user)** , abyste měli protějšek Britta Simon ve službě Marketo, který je propojený s reprezentací uživatele Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

### <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikace **Marketo** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://saml.marketo.com/sp`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    c. Do textového pole **stav přenosu** zadejte adresu URL pomocí následujícího vzoru: `https://<munchkinid>.marketo.com/`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a stavem přenosu. Pro získání těchto hodnot kontaktujte [tým podpory Marketo pro klienty](https://investors.marketo.com/contactus.cfm) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

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

1. Pokud chcete získat ID Munchkin vaší aplikace, přihlaste se k Marketě pomocí přihlašovacích údajů správce a proveďte následující akce:
   
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

1. Klikněte na testovat tuto aplikaci v Azure Portal a měli byste se automaticky přihlášeni ke službě Marketo, pro kterou jste si nastavili jednotné přihlašování.

1. Můžete použít panel Microsoft Access. Když kliknete na dlaždici Marketo na přístupovém panelu, měli byste se automaticky přihlásit ke službě Marketo, pro kterou jste si nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Další kroky

Po konfiguraci služby Marketo můžete vyhovět řízení relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

