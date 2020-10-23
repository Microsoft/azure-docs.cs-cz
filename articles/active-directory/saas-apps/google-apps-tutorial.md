---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování s konektorem Google Cloud (G Suite) | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a konektorem Google Cloud (G Suite).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: 9a5cb1e589481bb424507d08879da8cc1b14ff1c
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92448176"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-google-cloud-g-suite-connector"></a>Kurz: Azure Active Directory integrace jednotného přihlašování s konektorem Google Cloud (G Suite)

V tomto kurzu se dozvíte, jak integrovat konektor Google Cloud (G Suite) s Azure Active Directory (Azure AD). Když integrujete konektor Google Cloud (G Suite) s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup ke konektoru Google Cloud (G Suite).
* Umožněte, aby se vaši uživatelé automaticky přihlásili ke konektoru Google Cloud (G Suite) pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

- Předplatné služby Azure AD.
- Předplatné Google Cloud (G Suite) konektor s povoleným jednotným přihlašováním (SSO).
- Předplatné Google Apps nebo předplatné Google Cloud Platform.

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí. Tento dokument byl vytvořen pomocí jednotného přihlašování uživatele. Pokud stále používáte starou, bude instalace vypadat jinak. Nové prostředí můžete povolit v nastavení jednotného přihlašování aplikace G-Suite. Přejděte na **Azure AD, podnikové aplikace**, vyberte **konektor Google Cloud (G Suite)**, vyberte **jednotné přihlašování** a potom klikněte na **vyzkoušet nové prostředí**.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. **Otázka: Tato integrace podporuje Google Cloud Platform integraci jednotného přihlašování se službou Azure AD?**

    Odpověď: Ano. Aplikace Google Cloud Platform a Google sdílí stejnou platformu ověřování. Proto je třeba provést integraci GCP, abyste mohli nakonfigurovat jednotné přihlašování s aplikacemi Google.

2. **Otázka: jsou Chromebooks a jiná zařízení Chrome kompatibilní s jednotným přihlašováním Azure AD?**
  
    Odpověď: Ano, uživatelé se mohou přihlašovat ke svým zařízením Chromebook pomocí svých přihlašovacích údajů Azure AD. Informace o tom, proč se uživatelům může zobrazit výzva k zadání přihlašovacích údajů dvakrát, najdete v tomto [článku podpory konektoru Google Cloud (G Suite)](https://support.google.com/chrome/a/answer/6060880) .

3. **Otázka: Pokud povolíte jednotné přihlašování, budou uživatelé moci používat svoje přihlašovací údaje Azure AD k tomu, aby se mohli přihlásit k libovolnému produktu Google, například Google Classroom, GMail, disk Google, YouTube atd.?**

    Odpověď: Ano, v závislosti na [tom, který konektor Google Cloud (G Suite)](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) se rozhodnete pro vaši organizaci povolit nebo zakázat.

4. **Otázka: můžu povolit jednotné přihlašování jenom pro podmnožinu uživatelů konektoru Google Cloud (G Suite)?**

    Odpověď: Ne, okamžitě zapnout jednotné přihlašování vyžaduje, aby všichni uživatelé konektoru Google Cloud (G Suite) mohli provést ověření pomocí svých přihlašovacích údajů Azure AD. Protože konektor Google Cloud (G Suite) nepodporuje více zprostředkovatelů identity, poskytovatel identity pro prostředí konektoru Google Cloud (G Suite) může být buď Azure AD, nebo Google, ale ne současně současně.

5. **Otázka: Pokud je uživatel přihlášený prostřednictvím Windows, automaticky se ověří do konektoru Google Cloud (G Suite), aniž by se zobrazila výzva k zadání hesla?**

    Odpověď: Existují dvě možnosti pro povolení tohoto scénáře. Nejdřív se uživatelé mohli do zařízení s Windows 10 přihlásit pomocí [Azure Active Directory JOIN](../devices/overview.md). Další možností je, že se uživatelé můžou přihlašovat do zařízení s Windows, která jsou připojená k doméně, do místní služby Active Directory, u které se povolilo jednotné přihlašování ke službě Azure AD prostřednictvím nasazení [Active Directory Federation Services (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) . Obě možnosti vyžadují, abyste provedli kroky v následujícím kurzu a povolili jste jednotné přihlašování mezi Azure AD a konektorem Google Cloud (G Suite).

6. **Otázka: co mám dělat, když se zobrazí chybová zpráva "Neplatný e-mail"?**

    Odpověď: pro tuto instalaci je vyžadován atribut e-mail, aby se uživatelé mohli přihlásit. Tento atribut nelze nastavit ručně.

    Atribut e-mail je automaticky vyplněný pro každého uživatele s platnou licencí Exchange. Pokud uživatel není povolený e-mailem, zobrazí se tato chyba, protože aplikace potřebuje získat přístup k tomuto atributu.

    Můžete přejít na portal.office.com s účtem správce, potom kliknout na centrum pro správu, fakturaci, předplatná, vybrat předplatné Microsoft 365 a pak kliknout na přiřadit k uživatelům, vybrat uživatele, u kterých chcete kontrolu předplatného, a v pravém podokně kliknout na Upravit licence.

    Po přiřazení licence Microsoft 365 může trvat několik minut, než se použije. Potom bude automaticky vyplněn atribut User. mail, který by měl vyřešit problém.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Konektor Google Cloud (G Suite) podporuje jednotné přihlašování (SSO) spouštěné v **SP**

* Konektor Google Cloud (G Suite) podporuje [ **automatizované** zřizování uživatelů.](./google-apps-provisioning-tutorial.md)
* Jakmile nakonfigurujete konektor Google Cloud (G Suite), můžete vynutilit řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-google-cloud-g-suite-connector-from-the-gallery"></a>Přidání konektoru Google Cloud (G Suite) z Galerie

Pokud chcete nakonfigurovat integraci konektoru Google Cloud (G Suite) do Azure AD, musíte přidat konektor Google Cloud (G Suite) z Galerie do seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **konektor Google Cloud (G Suite)** .
1. Z panelu výsledků vyberte **konektor Google Cloud (G Suite)** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-google-cloud-g-suite-connector"></a>Konfigurace a testování jednotného přihlašování Azure AD pro konektor Google Cloud (G Suite)

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí konektoru Google Cloud (G Suite) pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v konektoru Google Cloud (G Suite) vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí konektoru Google Cloud (G Suite), dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace jednotného přihlašování ke konektoru Google Cloud (G Suite)](#configure-google-cloud-g-suite-connector-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvoření testovacího uživatele konektoru Google Cloud (g Suite)](#create-google-cloud-g-suite-connector-test-user)** – Pokud chcete mít protějšek B. Simon v konektoru Google Cloud (g Suite), který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **konektor Google Cloud (G Suite)** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat pro **Gmail** , proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: 

    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

1. Pokud chcete pro **Google Cloud Platform** nakonfigurovat **základní konfigurační oddíl SAML** , proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    google.com/a/<yourdomain.com>
    google.com
    https://google.com
    https://google.com/a/<yourdomain.com>
    ```
    
    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: 
    
    ```http
    https://www.google.com
    https://www.google.com/a/<yourdomain.com>
    ```

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným Sign-On URL a identifikátorem. Konektor Google Cloud (G Suite) při konfiguraci jednotného přihlašování neposkytuje hodnotu ID nebo identifikátoru entity, takže když zrušíte kontrolu **specifického vystavitele domény** , hodnota identifikátoru bude `google.com` . Pokud zaškrtnete možnost **vystavitele specifické pro doménu** , bude `google.com/a/<yourdomainname.com>` . Pokud chcete zaškrtnout/zrušit kontrolu pro **vystavitele specifické pro doménu** , musíte přejít do části **Konfigurace konektoru pro jednotné přihlašování Google Cloud (G Suite)** , který se vysvětluje později v tomto kurzu. Další informace získáte od [týmu podpory konektoru pro Google Cloud (G Suite)](https://www.google.com/contact/).

1. Vaše aplikace konektoru Google Cloud (G Suite) očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Příklad ukazuje následující snímek obrazovky. Výchozí hodnota **jedinečného identifikátoru uživatele** je **User. userPrincipalName** , ale konektor Google Cloud (G Suite) očekává, že bude namapován pomocí e-mailové adresy uživatele. Pro tuto funkci můžete použít atribut **User. mail** ze seznamu nebo použít odpovídající hodnotu atributu na základě konfigurace vaší organizace.

    ![image](common/default-attributes.png)


1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení konektoru Google Cloud (G Suite)** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup ke konektoru Google Cloud (G Suite).

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **konektor Google Cloud (G Suite)**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-google-cloud-g-suite-connector-sso"></a>Konfigurace jednotného přihlašování ke konektoru Google Cloud (G Suite)

1. V prohlížeči otevřete novou kartu a přihlaste se ke [konzole pro správu konektoru Google Cloud (G Suite)](https://admin.google.com/) pomocí účtu správce.

2. Klikněte na **zabezpečení**. Pokud odkaz nevidíte, může být skrytý v nabídce **Další ovládací prvky** v dolní části obrazovky.

    ![Klikněte na zabezpečení.][10]

3. Na stránce **zabezpečení** klikněte na **nastavit jednotné přihlašování (SSO).**

    ![Klikněte na jednotné přihlašování.][11]

4. Proveďte následující změny konfigurace:

    ![Konfigurace jednotného přihlašování][12]

    a. Vyberte **nastavení jednotného přihlašování s poskytovatelem identity od jiného výrobce**.

    b. V poli **Adresa URL přihlašovací stránky** v konektoru Google Cloud (G Suite) vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    c. V poli **Adresa URL stránky pro odhlášení** v konektoru Google Cloud (G Suite) vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    > [!NOTE]
    > Google Cloud (G Suite) je založený na protokolu odhlašovacího SAML. Takže v poli **Adresa URL stránky** pro odhlášení musíme použít adresu URL pro ODhlášení SAML, tj. adresu URL pro přihlášení jako hodnotu pro stejné.

    d. V konektoru Google Cloud (G Suite) pro **ověřovací certifikát**Nahrajte certifikát, který jste stáhli z Azure Portal.   

    e. Zaškrtnout/zrušit kontrolu **použití možnosti vystavitele specifické pro doménu** podle poznámky uvedené výše v části **základní konfigurace SAML** ve službě Azure AD.

    f. V poli **změnit adresu URL hesla** v konektoru Google Cloud (G Suite) vložte hodnotu **Adresa URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.

    například Klikněte na **Uložit**.

### <a name="create-google-cloud-g-suite-connector-test-user"></a>Vytvořit testovacího uživatele konektoru Google Cloud (G Suite)

Cílem této části je [vytvořit uživatele v konektoru Google Cloud (G Suite)](https://support.google.com/a/answer/33310?hl=en) s názvem B. Simon. Po vytvoření uživatele v konektoru Google Cloud (G Suite) se uživatel nyní bude moci přihlásit pomocí přihlašovacích údajů Microsoft 365.

Konektor Google Cloud (G Suite) podporuje taky Automatické zřizování uživatelů. Pokud chcete nakonfigurovat automatické zřizování uživatelů, musíte nejdřív [nakonfigurovat konektor Google Cloud (G Suite) pro Automatické zřizování uživatelů](./google-apps-provisioning-tutorial.md).

> [!NOTE]
> Ujistěte se, že váš uživatel už existuje v konektoru Google Cloud (G Suite), pokud se zřizování ve službě Azure AD nepřed testováním jednotného přihlašování nezapnulo.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když kliknete na dlaždici konektor Google Cloud (G Suite) na přístupovém panelu, měli byste se automaticky přihlásit ke konektoru Google Cloud (G Suite), pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Konfigurace zřizování uživatelů](./google-apps-provisioning-tutorial.md)

- [Vyzkoušejte si konektor Google Cloud (G Suite) s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit konektor Google Cloud (G Suite) s pokročilou viditelností a ovládacími prvky](/cloud-app-security/protect-gsuite)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png