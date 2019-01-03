---
title: 'Kurz: Integrace Azure Active Directory s G Suite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: f4f7546efca0bd69fd71418116142acacb927604
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970895"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Kurz: Integrace Azure Active Directory s G Suite

V tomto kurzu se dozvíte, jak integrovat G Suite se službou Azure Active Directory (Azure AD).
Integrace G Suite s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup ke G Suite.
* Uživatelům se automaticky přihlášeni ke G Suite (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s G Suite, potřebujete následující položky:

- Předplatné Azure AD
- G Suite jednotného přihlašování povolená předplatného
- Google Apps předplatného nebo předplatného Google Cloud Platform.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí. Tento dokument byl vytvořen pomocí nového uživatele prostředí jednotného přihlašování. Pokud stále používáte starý, instalační program bude vypadat diferent. Můžete povolit nové prostředí v nastavení jednotného přihlašování aplikace G Suite. Přejděte na **Azure AD, podnikové aplikace**vyberte **G Suite**vyberte **Single Sign-on** a potom klikněte na **vyzkoušet naše nové rozhraní**.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. **DOTAZ: Podporuje tato integrační integraci Google Cloud Platform jednotného přihlašování s Azure AD?**

    ODPOVĚĎ: Ano. Google Cloud Platform a Google Apps sdílejí stejnou platformu ověřování. To provedete integraci GCP, budete muset nakonfigurovat jednotné přihlašování s Google Apps.

2. **DOTAZ: Jsou Chromebooks a dalších zařízeních Chrome, kompatibilní s Azure AD jednotné přihlašování?**
  
    ODPOVĚĎ: Ano, uživatelé se můžou k přihlašování do jejich zařízení Chromebook pomocí svých přihlašovacích údajů Azure AD. Najdete v tomto [článek podpory G Suite](https://support.google.com/chrome/a/answer/6060880) informace o tom, proč uživatelům může zobrazit výzva k zadání přihlašovacích údajů dvakrát.

3. **DOTAZ: Je-li povolit jednotné přihlašování, uživatelé budou moci pomocí svých přihlašovacích údajů Azure AD k přihlašování do jakékoli produktu Google, jako je Google Classroom, GMail, disk Google, YouTube a tak dále?**

    ODPOVĚĎ: Ano, v závislosti na [které G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) budete chtít povolit nebo zakázat pro vaši organizaci.

4. **DOTAZ: Můžete povolit jednotné přihlašování pro pouze podmnožinu uživatelů G Suite?**

    ODPOVĚĎ: Ne, zapnutí jednotného přihlašování okamžitě vyžaduje všichni uživatelé G Suite k ověření pomocí svých přihlašovacích údajů Azure AD. Protože G Suite nepodporuje existenci více zprostředkovatelů identity, zprostředkovatel identity pro vaše prostředí G Suite může být Azure AD nebo Google – ale ne obojí současně.

5. **DOTAZ: Pokud je uživatel přihlášený prostřednictvím Windows, jsou že automaticky ověřování ke G Suite bez získání zobrazí výzva k zadání hesla?**

    ODPOVĚĎ: Existují dvě možnosti pro povolení tohoto scénáře. Nejprve, můžou uživatelé můžou přihlašovat do zařízení s Windows 10 přes [Azure Active Directory Join](../device-management-introduction.md). Alternativně můžou uživatelé můžou přihlašovat do zařízení Windows, které jsou připojené k místní Active Directory byl povolen pro jednotné přihlašování do služby Azure AD prostřednictvím [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) nasazení. Obě možnosti vyžadují, abyste proveďte kroky v následujícím kurzu a povolit jednotné přihlašování mezi Azure AD a G Suite.

6. **DOTAZ: Co mám dělat, když se zobrazí chybová zpráva "neplatné e-mailu"?**

    ODPOVĚĎ: Pro toto nastavení je atribut e-mailové uživatelé moct přihlásit. Tento atribut nelze nastavit ručně.

    Atribut e-mailu se vyplní automaticky pro každý uživatel s platnou licenci systému Exchange. Pokud uživatel není povolený e-mail, bude tato chyba přijmout, protože aplikace je potřeba získat tento atribut přístup.

    Můžete přejít na portal.office.com pomocí účtu správce, pak klikněte na tlačítko v Centru pro správu, fakturace, předplatnými, vyberte předplatné Office 365 a potom kliknutím na přiřadit uživatele, vyberte uživatele, které chcete zkontrolovat svoje předplatné a v pravém podokně klikněte na Úprava licence.

    Po přiřazení licence O365, může trvat několik minut, který má být použita. Poté atribut user.mail se vyplní automaticky a problém by měly být opraveny.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* G Suite podporuje **SP** jednotné přihlašování zahájené pomocí
* G Suite podporuje  **[automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie

Konfigurace integrace G Suite do Azure AD, budete muset přidat G Suite v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat G Suite z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **G Suite**vyberte **G Suite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![G Suite v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s G Suite podle testu uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v G Suite.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s G Suite, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace G Suite Single Sign-On](#configure-g-suite-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele G Suite](#create-g-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon v G Suite, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s G Suite, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **G Suite** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![G Suite domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory G Suite klienta](https://www.google.com/contact/) k získání těchto hodnot.

5. G Suite aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje příklad pro tuto. Výchozí hodnota **jedinečný identifikátor uživatele** je **user.userprincipalname** ale G Suite očekává, že to namapovat s e-mailovou adresu uživatele. K tomu můžete použít **user.mail** atribut ze seznamu nebo použijte hodnotu odpovídajícího atributu na základě vaší konfigurace organizace.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut |
    | ---------------| --------------- |
    | Jedinečný identifikátor uživatele | User.Mail |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení G Suite** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-g-suite-single-sign-on"></a>Konfigurace G Suite jednotného přihlašování

1. Otevření nové záložky v prohlížeči a přihlaste se [konzoly pro správu G Suite](http://admin.google.com/) pomocí účtu správce.

2. Klikněte na tlačítko **zabezpečení**. Pokud nevidíte odkaz, mohou být skryty pod **další ovládací prvky** nabídce v dolní části obrazovky.

    ![Klikněte na Zabezpečení.][10]

3. Na **zabezpečení** klikněte na **nastavit jednotné přihlašování (SSO).**

    ![Klikněte na možnost jednotného přihlašování.][11]

4. Proveďte následující změny konfigurace:

    ![Konfigurace jednotného přihlašování][12]

    a. Vyberte **nastavení jednotného přihlašování pomocí zprostředkovatele identity třetí strany**.

    b. V **přihlašovací adresa URL stránky** pole v G Suite, vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. V **adresy URL odhlašovací stránky** pole v G Suite, vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. V **změnit adresy URL hesla** pole v G Suite, vložte hodnotu **změnit adresy URL hesla** zkopírovanou z webu Azure portal.

    e. V G Suite pro **ověřovací certifikát**, nahrát certifikát, který jste si stáhli z webu Azure portal.

    f. Vyberte **použít konkrétní vystavitele domény**.

    g. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke G Suite.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **G Suite**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **G Suite**.

    ![Odkaz G Suite v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-g-suite-test-user"></a>Vytvořit testovacího uživatele G Suite

Cílem této části je vytvořte uživatele Britta Simon v G Suite softwaru. G Suite podporuje automatické zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná akce vám v této části. Pokud uživatel ještě neexistuje v G Suite softwaru, je vytvořen nový při pokusu o přístup k softwaru G Suite.

> [!NOTE]
> Ujistěte se, že uživatel už existuje v G Suite. Pokud zřizování ve službě Azure AD není zapnutý před testováním jednotného přihlašování.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se [tým podpory Google](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici G Suite na přístupovém panelu, vám by měl být automaticky přihlášeni ke G Suite, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Konfigurace zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png