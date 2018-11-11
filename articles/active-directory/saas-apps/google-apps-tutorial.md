---
title: 'Kurz: Integrace Azure Active Directory se službou G Suite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2018
ms.author: jeedes
ms.openlocfilehash: 4ed571d34e5df67f556f39b898e7ae5efc06a3e1
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51288930"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Kurz: Integrace Azure Active Directory se službou G Suite

V tomto kurzu se dozvíte, jak integrovat G Suite se službou Azure Active Directory (Azure AD).

Integrace G Suite s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup ke G Suite.
- Uživatele, aby automaticky získat přihlášeného ke G Suite (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s G Suite, potřebujete následující položky:

- Předplatné Azure AD
- G Suite jednotného přihlašování povolená předplatného
- Google Apps předplatného nebo předplatného Google Cloud Platform.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. **Otázka: Tato integrační podpora integraci Google Cloud Platform jednotného přihlašování s Azure AD?**

    Odpověď: Ano. Google Cloud Platform a Google Apps sdílejí stejnou platformu ověřování. To provedete integraci GCP, budete muset nakonfigurovat jednotné přihlašování s Google Apps.

2. **Otázka: jsou Chromebooks a dalších zařízeních Chrome kompatibilní s Azure AD jednotné přihlašování?**
  
    Odpověď: Ano, uživatelé se můžou k přihlašování do jejich zařízení Chromebook pomocí svých přihlašovacích údajů Azure AD. Najdete v tomto [článek podpory G Suite](https://support.google.com/chrome/a/answer/6060880) informace o tom, proč uživatelům může zobrazit výzva k zadání přihlašovacích údajů dvakrát.

3. **Otázka: Pokud povolit jednotné přihlašování, uživatelé budou moci přihlásit libovolný produkt Google, jako je Google Classroom, GMail, disk Google, YouTube a tak dále pomocí svých přihlašovacích údajů Azure AD?**

    Odpověď: Ano, v závislosti na [které G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) budete chtít povolit nebo zakázat pro vaši organizaci.

4. **Dotaz: lze povolit jednotné přihlašování pro pouze podmnožinu uživatelů G Suite?**

    Odpověď: Ne, zapnutí jednotného přihlašování okamžitě vyžaduje všichni uživatelé G Suite k ověření pomocí svých přihlašovacích údajů Azure AD. Protože G Suite nepodporuje existenci více zprostředkovatelů identity, zprostředkovatel identity pro vaše prostředí G Suite může být Azure AD nebo Google – ale ne obojí současně.

5. **Otázka: Pokud je uživatel přihlášený prostřednictvím Windows, jsou že automaticky ověřování ke G Suite bez získání zobrazí výzva k zadání hesla?**

    Odpověď: existují dvě možnosti pro povolení tohoto scénáře. Nejprve, můžou uživatelé můžou přihlašovat do zařízení s Windows 10 přes [Azure Active Directory Join](../device-management-introduction.md). Alternativně můžou uživatelé můžou přihlašovat do zařízení Windows, které jsou připojené k místní Active Directory byl povolen pro jednotné přihlašování do služby Azure AD prostřednictvím [Active Directory Federation Services (AD FS)](../hybrid/plan-connect-user-signin.md) nasazení. Obě možnosti vyžadují, abyste proveďte kroky v následujícím kurzu a povolit jednotné přihlašování mezi Azure AD a G Suite.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí.
Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání G Suite z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie

Konfigurace integrace G Suite do Azure AD, budete muset přidat G Suite v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat G Suite z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/google-apps-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/google-apps-tutorial/a_select_app.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/google-apps-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **G Suite**vyberte **G Suite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![image](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí G Suite podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v G Suite je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v G Suite.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s G Suite, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele G Suite](#create-a-g-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon v G Suite, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci G Suite.

**Ke konfiguraci Azure AD jednotné přihlašování s G Suite, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **G Suite** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. Klikněte na tlačítko **režim změnit jednotného přihlašování** nad obrazovky, vyberte **SAML** režimu.

      ![image](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. Na **vybrat jedinou metodu přihlašování** dialogového okna, klikněte na tlačítko **vyberte** pro **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![image](./media/google-apps-tutorial/b1-domains_and_urls.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory G Suite klienta](https://www.google.com/contact/) k získání těchto hodnot.

6. G Suite aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](./media/google-apps-tutorial/i3-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    a. Klikněte na tlačítko **upravit** tlačítko Otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/google-apps-tutorial/i2-attribute.png)

    ![image](./media/google-apps-tutorial/i4-attribute.png)

    b. Z **zdrojový atribut** seznamu selelct hodnotu atributu.

    c. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** stáhnout příslušný certifikát, jak je uvedeno vaše požadavek a uložte ho do počítače.

    ![image](./media/google-apps-tutorial/certificatebase64.png)

9. Na **nastavení G Suite** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    Všimněte si, že adresa URL může třeba následující:

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    ![image](./media/google-apps-tutorial/d1_saml.png) 

10. Otevření nové záložky v prohlížeči a přihlaste se [konzoly pro správu G Suite](http://admin.google.com/) pomocí účtu správce.

11. Klikněte na tlačítko **zabezpečení**. Pokud nevidíte odkaz, mohou být skryty pod **další ovládací prvky** nabídce v dolní části obrazovky.

    ![Klikněte na Zabezpečení.][10]

12. Na **zabezpečení** klikněte na **nastavit jednotné přihlašování (SSO).**

    ![Klikněte na možnost jednotného přihlašování.][11]

13. Proveďte následující změny konfigurace:

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

    ![image](./media/google-apps-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/google-apps-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/google-apps-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.

### <a name="create-a-g-suite-test-user"></a>Vytvoření zkušebního uživatele G Suite

Cílem této části je vytvořte uživatele Britta Simon v G Suite softwaru. G Suite podporuje automatické zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná akce vám v této části. Pokud uživatel ještě neexistuje v G Suite softwaru, je vytvořen nový při pokusu o přístup k softwaru G Suite.

> [!NOTE]
> Ujistěte se, že uživatel už existuje v G Suite. Pokud zřizování ve službě Azure AD není zapnutý před testováním jednotného přihlašování.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se [tým podpory Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke G Suite.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/google-apps-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **G Suite**.

    ![image](./media/google-apps-tutorial/d_all_proapplications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/google-apps-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/google-apps-tutorial/d_assign_user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici G Suite na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci G Suite.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png