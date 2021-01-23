---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SuccessFactors | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SuccessFactors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: a903eb6000cdd5212ad358cae4952e27a4719070
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725257"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SuccessFactors

V tomto kurzu se dozvíte, jak integrovat SuccessFactors s Azure Active Directory (Azure AD). Když integrujete SuccessFactors s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k SuccessFactors.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SuccessFactors svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.


## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* SuccessFactors odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* SuccessFactors podporuje jednotné přihlašování iniciované v **SP** .

## <a name="adding-successfactors-from-the-gallery"></a>Přidání SuccessFactors z Galerie

Pokud chcete nakonfigurovat integraci SuccessFactors do služby Azure AD, musíte přidat SuccessFactors z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k Azure Portal pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **SuccessFactors** .
1. Na panelu výsledků vyberte **SuccessFactors** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Konfigurace a testování jednotného přihlašování Azure AD pro SuccessFactors

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí SuccessFactors pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v SuccessFactors.

K nakonfigurování a testování jednotného přihlašování Azure AD pomocí SuccessFactors postupujte takto:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
2. **[Nakonfigurujte SUCCESSFACTORS SSO](#configure-successfactors-sso)** – ke konfiguraci nastavení jednoho Sign-On na straně aplikace.
    1. **[Vytvořte SuccessFactors Test User](#create-successfactors-test-user)** -to, abyste měli protějšek B. Simon v SuccessFactors, která je propojená s reprezentací uživatele v Azure AD.
3. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V Azure Portal na stránce integrace aplikací **SuccessFactors** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL, identifikátoru a adresy URL odpovědi. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta SuccessFactors](https://www.sap.com/support.html) .

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte si certifikát a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení SuccessFactors** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
    1. Do pole **Název** zadejte `B.Simon`.  
    1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například `B.Simon@contoso.com`.
    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
    1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k SuccessFactors.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SuccessFactors**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.
1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte, že role má být přiřazena uživatelům, můžete ji vybrat v rozevíracím seznamu **Vybrat roli** . Pokud pro tuto aplikaci není nastavená žádná role, zobrazí se vybraná role výchozí přístup.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-successfactors-sso"></a>Konfigurace jednotného přihlašování SuccessFactors

1. V jiném okně webového prohlížeče se přihlaste k **portálu pro správu SuccessFactors** jako správce.

2. Přejděte na **zabezpečení aplikace** a nativně do **funkce jednotného přihlašování**.

3. Přiložte do **tokenu Reset** libovolnou hodnotu a kliknutím na **Uložit token** povolte jednotné přihlašování SAML.

    ![Snímek obrazovky se zobrazí karta zabezpečení aplikace s funkcemi jednotného přihlašování, které se nazývají, kde můžete zadat token.][11]

    > [!NOTE]
    > Tato hodnota se používá jako přepínač Zapnuto/vypnuto. Pokud se nějaká hodnota uloží, je jednotné přihlašování SAML ZAPNUTé. Pokud je uložena prázdná hodnota, je jednotné přihlašování SAML vypnuto.

4. Nativně na snímek obrazovky a proveďte následující akce:

    ![Snímek obrazovky ukazuje podokno pro s podporou SAML, kde můžete zadat hodnoty, které jsou popsány.][12]
  
    a. Vyberte přepínač **jednotného přihlašování SAML v2** .
  
    b. Nastavte **název strany pro vyhodnocení SAML**(například Vydavatel SAML + název společnosti).

    c. Do textového pole **Adresa URL vystavitele** vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali z Azure Portal.

    d. Vyberte **kontrolní výraz** jako **vyžadovat povinný podpis**.

    e. Vyberte **Povolit** **příznak SAML**.

    f. Vyberte možnost **ne** jako **podpis žádosti o přihlášení (SF vygenerované/SP/RP)**.

    například Jako **profil SAML** vyberte **browser nebo post profil** .

    h. Vyberte **ne** , jako **vynutilo platné období certifikátu**.

    i. Zkopírujte obsah staženého souboru certifikátu z Azure Portal a pak ho vložte do textového pole pro **ověření certifikátu SAML** .

    > [!NOTE] 
    > Obsah certifikátu musí mít počáteční certifikát a značky koncového certifikátu.

5. Přejděte na SAML v2 a pak proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v podokně pro odhlášení inicializované SAML v2 S P, kde můžete zadat hodnoty, které jsou popsány.][13]

    a. V případě **podpory globálního odhlašování iniciované SP** vyberte **Ano** .

    b. Do textového pole Adresa URL služby **Websigning** **Service (LogoutRequest Destination)** vložte hodnotu URL pro odhlášení, kterou jste zkopírovali, do formuláře Azure Portal.

    c. Vyberte **ne** , protože **vyžadovat SP musí zašifrovat všechny NameId elementy**.

    d. Vyberte **Neurčeno** jako **Formát NameId**.

    e. Vyberte **Ano** jako **Povolit přihlášení iniciované SP (AuthnRequest)**.

    f. Do textového pole **Odeslat žádost jako Company-Wide vystavitele** vložte hodnotu **URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

6. Tyto kroky proveďte, pokud chcete, aby přihlašovací jména uživatele necitlivá velká a malá písmena.

    ![Konfigurace jednoho Sign-On][29]

    a. Přejděte na **nastavení společnosti**(v dolní části).

    b. Vyberte zaškrtávací políčko v blízkosti možnost **Povolit uživatelské jméno bez rozlišení velkých a malých písmen**.

    c. Klikněte na **Uložit**.

    > [!NOTE]
    > Pokud se pokusíte tuto možnost povolit, systém zkontroluje, jestli vytvoří duplicitní přihlašovací jméno SAML. Například pokud má zákazník uživatelská jména uživatel1 a uživatel1. Při zahození rozlišování malých a velkých písmen tyto duplicity provede. Systém vám poskytne chybovou zprávu a funkci nepovoluje. Zákazník musí změnit jedno ze svých uživatelských jmen, aby bylo možné je napsané jinak.

### <a name="create-successfactors-test-user"></a>Vytvořit testovacího uživatele SuccessFactors

Aby se uživatelé Azure AD mohli přihlašovat k SuccessFactors, musí se zřídit v SuccessFactors. V případě SuccessFactors je zřizování ručním úkolem.

Pokud chcete získat uživatele vytvořené v SuccessFactors, musíte se obrátit na [tým podpory SuccessFactors](https://www.sap.com/support.html).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí následujících možností. 

* Kliknutím na **test této aplikace** v Azure Portal. Tím se přesměruje na adresu URL pro přihlášení k SuccessFactors, kde můžete spustit tok přihlášení. 

* Přejít na adresu URL pro přihlášení k SuccessFactors přímo a zahájit tok přihlášení.

* Můžete použít aplikaci Microsoft moje aplikace. Když kliknete na dlaždici SuccessFactors v části Moje aplikace, přesměruje se na přihlašovací adresu SuccessFactors. Další informace o mých aplikacích najdete v tématu [Úvod do mých aplikací](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Další kroky

Po nakonfigurování SuccessFactors můžete vymáhat ovládací prvky relací, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Ovládací prvky relace se rozšíří z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png