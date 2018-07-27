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
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: c8ec2b8e312b9bedbd19cb203c0a59177c7fd6a5
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265008"
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

- S předplatným služby Azure AD
- G Suite jednotného přihlašování povolená předplatného
- Google Apps předplatného nebo předplatného Google Cloud Platform.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
1.  **Otázka: Tato integrační podpora integraci Google Cloud Platform jednotného přihlašování s Azure AD?**
    
    Odpověď: Ano. Google Cloud Platform a Google Apps sdílejí stejnou platformu ověřování. To provedete integraci GCP, budete muset nakonfigurovat jednotné přihlašování s Google Apps.


2. **Otázka: jsou Chromebooks a dalších zařízeních Chrome kompatibilní s Azure AD jednotné přihlašování?**
   
    Odpověď: Ano, uživatelé se můžou k přihlašování do jejich zařízení Chromebook pomocí svých přihlašovacích údajů Azure AD. Najdete v tomto [článek podpory G Suite](https://support.google.com/chrome/a/answer/6060880) informace o tom, proč uživatelům může zobrazit výzva k zadání přihlašovacích údajů dvakrát.

3. **Otázka: Pokud povolit jednotné přihlašování, uživatelé budou moci přihlásit libovolný produkt Google, jako je Google Classroom, GMail, disk Google, YouTube a tak dále pomocí svých přihlašovacích údajů Azure AD?**
   
    Odpověď: Ano, v závislosti na [které G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) budete chtít povolit nebo zakázat pro vaši organizaci.

4. **Dotaz: lze povolit jednotné přihlašování pro pouze podmnožinu uživatelů G Suite?**
   
    Odpověď: Ne, zapnutí jednotného přihlašování okamžitě vyžaduje všichni uživatelé G Suite k ověření pomocí svých přihlašovacích údajů Azure AD. Protože G Suite nepodporuje existenci více zprostředkovatelů identity, zprostředkovatel identity pro vaše prostředí G Suite může být Azure AD nebo Google – ale ne obojí současně.

5. **Otázka: Pokud je uživatel přihlášený prostřednictvím Windows, jsou že automaticky ověřování ke G Suite bez získání zobrazí výzva k zadání hesla?**
   
    Odpověď: existují dvě možnosti pro povolení tohoto scénáře. Nejprve, můžou uživatelé můžou přihlašovat do zařízení s Windows 10 přes [Azure Active Directory Join](../device-management-introduction.md). Alternativně můžou uživatelé můžou přihlašovat do zařízení Windows, které jsou připojené k místní Active Directory byl povolen pro jednotné přihlašování do služby Azure AD prostřednictvím [Active Directory Federation Services (AD FS)](../connect/active-directory-aadconnect-user-signin.md) nasazení. Obě možnosti vyžadují, abyste proveďte kroky v následujícím kurzu a povolit jednotné přihlašování mezi Azure AD a G Suite.

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání G Suite z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie
Konfigurace integrace G Suite do Azure AD, budete muset přidat G Suite v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat G Suite z galerie, postupujte následovně:**

1. V  **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![V okně podnikové aplikace][2]
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **G Suite**vyberte **G Suite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![G Suite v seznamu výsledků](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí G Suite podle testovacího uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v G Suite je pro uživatele ve službě Azure AD. Jinými slovy musí navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v G Suite.

V G Suite, přiřaďte hodnotu **uživatelské jméno** ve službě Azure AD jako hodnotu **uživatelské jméno** a tím vytvoří vztah odkazu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s G Suite, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvoření zkušebního uživatele G Suite](#create-a-g-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon v G Suite, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci G Suite.

**Ke konfiguraci Azure AD jednotné přihlašování s G Suite, proveďte následující kroky:**

1. Na webu Azure Portal na **G Suite** integrace stránka aplikace, klikněte na tlačítko **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz][4]

2. Na **jednotného přihlašování** dialogového okna, vyberte **režimu** jako **přihlašování na základě SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování – dialogové okno](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. Na **domény G Suite a adresy URL** části, pokud chcete nakonfigurovat pro **Gmail** proveďte následující kroky:

    ![G Suite domény a adresy URL jednotného přihlašování – informace](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory G Suite klienta](https://www.google.com/contact/) k získání těchto hodnot.

4. Na **domény G Suite a adresy URL** části, pokud chcete nakonfigurovat pro **Google Cloud Platform** proveďte následující kroky:

    ![G Suite domény a adresy URL jednotného přihlašování – informace](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. V **identifikátor** textového pole zadejte adresu URL pomocí následujícímu vzoru: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Kontakt [tým podpory G Suite klienta](https://www.google.com/contact/) k získání těchto hodnot. 

5. Na **podpisový certifikát SAML** klikněte na tlačítko **certifikát** a uložte soubor certifikátu v počítači.

    ![Odkaz ke stažení certifikátu](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/google-apps-tutorial/tutorial_general_400.png)

7. Na **konfigurace G Suite** klikněte na tlačítko **konfigurace G Suite** otevřete **nakonfigurovat přihlašování** okna. Kopírování **URL odhlašování SAML jednotné přihlašování – adresa URL služby a změnit adresy URL hesla** z **Stručná referenční příručka oddílu.**

    ![Konfigurace G Suite](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

8. Otevření nové záložky v prohlížeči a přihlaste se [konzoly pro správu G Suite](http://admin.google.com/) pomocí účtu správce.

9. Klikněte na tlačítko **zabezpečení**. Pokud nevidíte odkaz, mohou být skryty pod **další ovládací prvky** nabídce v dolní části obrazovky.
   
    ![Klikněte na Zabezpečení.][10]

10. Na **zabezpečení** klikněte na **nastavit jednotné přihlašování (SSO).**
   
    ![Klikněte na možnost jednotného přihlašování.][11]

11. Proveďte následující změny konfigurace:
   
    ![Konfigurace jednotného přihlašování][12]
   
    a. Vyberte **nastavení jednotného přihlašování pomocí zprostředkovatele identity třetí strany**.

    b. V **přihlašovací adresa URL stránky** pole v G Suite, vložte hodnotu **jednotné přihlašování – adresa URL služby** zkopírovanou z webu Azure portal.

    c. V **adresy URL odhlašovací stránky** pole v G Suite, vložte hodnotu **odhlašování URL** zkopírovanou z webu Azure portal. 

    d. V **změnit adresy URL hesla** pole v G Suite, vložte hodnotu **změnit adresy URL hesla** zkopírovanou z webu Azure portal. 

    e. V G Suite pro **ověřovací certifikát**, nahrát certifikát, který jste si stáhli z webu Azure portal.

    f. Vyberte **použít konkrétní vystavitele domény**.

    g. Klikněte na tlačítko **uložit změny**.

> [!TIP]
> Teď si můžete přečíst stručné verzi těchto pokynů uvnitř [webu Azure portal](https://portal.azure.com), zatímco jsou nastavení aplikace!  Po přidání této aplikace z **služby Active Directory > podnikové aplikace** části, stačí kliknout **Single Sign-On** kartu a přístup k vložené dokumentaci prostřednictvím  **Konfigurace** oblast v dolní části. Další informace o funkci vložená dokumentace: [dokumentace ke službě Azure AD embedded]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

   ![Vytvořit testovacího uživatele Azure AD][100]

**Chcete-li vytvořit testovacího uživatele ve službě Azure AD, postupujte následovně:**

1. Na webu Azure Portal, v levém podokně klikněte na tlačítko **Azure Active Directory** tlačítko.

    ![Tlačítko Azure Active Directory](./media/google-apps-tutorial/create_aaduser_01.png)

2. Chcete-li zobrazit seznam uživatelů, přejděte na **uživatelů a skupin**a potom klikněte na tlačítko **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](./media/google-apps-tutorial/create_aaduser_02.png)

3. Chcete-li otevřít **uživatele** dialogové okno, klikněte na tlačítko **přidat** v horní části **všichni uživatelé** dialogové okno.

    ![Tlačítko Přidat](./media/google-apps-tutorial/create_aaduser_03.png)

4. V **uživatele** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno uživatele](./media/google-apps-tutorial/create_aaduser_04.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte e-mailovou adresu uživatele Britta Simon.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí **heslo** pole.

    d. Klikněte na možnost **Vytvořit**.
 
### <a name="create-a-g-suite-test-user"></a>Vytvoření zkušebního uživatele G Suite

Cílem této části je vytvořte uživatele Britta Simon v G Suite softwaru. G Suite podporuje automatické zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná akce vám v této části. Pokud uživatel ještě neexistuje v G Suite softwaru, je vytvořen nový při pokusu o přístup k softwaru G Suite.

>[!NOTE] 
>Pokud je potřeba ručně vytvořit uživatele, obraťte se [tým podpory Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu ke G Suite.

![Přiřazení role uživatele][200] 

**Pokud chcete přiřadit Britta Simon ke G Suite, postupujte následovně:**

1. Na webu Azure Portal, otevřete zobrazení aplikací a pak přejděte do zobrazení adresáře a přejděte na **podnikové aplikace** klikněte **všechny aplikace**.

    ![Přiřadit uživatele][201] 

2. V seznamu aplikací vyberte **G Suite**.

    ![Odkaz G Suite v seznamu aplikací](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

3. V nabídce na levé straně klikněte na tlačítko **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"][202]

4. Klikněte na tlačítko **přidat** tlačítko. Potom vyberte **uživatelů a skupin** na **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení][203]

5. Na **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů.

6. Klikněte na tlačítko **vyberte** tlačítko **uživatelů a skupin** dialogového okna.

7. Klikněte na tlačítko **přiřadit** tlačítko **přidat přiřazení** dialogového okna.
    
### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici G Suite na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci G Suite.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png

