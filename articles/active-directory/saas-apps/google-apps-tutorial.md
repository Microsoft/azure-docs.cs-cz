---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s G Suite | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e4449ac3519757bb9670d2d7fec53cb5f3ce152
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948299"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Kurz: Azure Active Directory integraci jednotného přihlašování s G Suite

V tomto kurzu se dozvíte, jak integrovat G Suite s Azure Active Directory (Azure AD). Když integrujete G Suite s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k G Suite.
* Umožněte uživatelům, aby se automaticky přihlásili k G Suite pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

- Předplatné služby Azure AD.
- Předplatné G Suite s podporou jednotného přihlašování (SSO).
- Předplatné Google Apps nebo předplatné Google Cloud Platform.

> [!NOTE]
> K otestování kroků v tomto kurzu nedoporučujeme používat produkční prostředí. Tento dokument byl vytvořen pomocí jednotného přihlašování uživatele. Pokud stále používáte starou, bude instalace vypadat jinak. Nové prostředí můžete povolit v nastavení jednotného přihlašování aplikace G-Suite. Přejděte na **Azure AD, podnikové aplikace**, vyberte **G Suite**, vyberte **jednotné přihlašování** a potom klikněte na **vyzkoušet nové prostředí**.

K otestování kroků v tomto kurzu byste měli postupovat podle těchto doporučení:

- Nepoužívejte své provozní prostředí, pokud není nutné.
- Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

1. **Otázka: Tato integrace podporuje Google Cloud Platform integraci jednotného přihlašování se službou Azure AD?**

    Odpověď: Ano. Aplikace Google Cloud Platform a Google sdílí stejnou platformu ověřování. Proto je třeba provést integraci GCP, abyste mohli nakonfigurovat jednotné přihlašování s aplikacemi Google.

2. **Otázka: jsou Chromebooks a jiná zařízení Chrome kompatibilní s jednotným přihlašováním Azure AD?**
  
    Odpověď: Ano, uživatelé se mohou přihlašovat ke svým zařízením Chromebook pomocí svých přihlašovacích údajů Azure AD. Informace o tom, proč se uživatelům může zobrazit výzva k zadání přihlašovacích údajů dvakrát, najdete v tomto [článku podpory G Suite](https://support.google.com/chrome/a/answer/6060880) .

3. **Otázka: Pokud povolíte jednotné přihlašování, budou uživatelé moci používat svoje přihlašovací údaje Azure AD k tomu, aby se mohli přihlásit k libovolnému produktu Google, například Google Classroom, GMail, disk Google, YouTube atd.?**

    Odpověď: Ano, v závislosti na [tom, kterou sadu G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) jste zvolili pro povolení nebo zakázání pro vaši organizaci.

4. **Otázka: můžu povolit jednotné přihlašování jenom pro podmnožinu svých uživatelů G Suite?**

    Odpověď: Ne, zapnutí jednotného přihlašování okamžitě vyžaduje, aby všichni uživatelé sady G Suite ověřili své přihlašovací údaje služby Azure AD. Vzhledem k tomu, že G Suite nepodporuje více zprostředkovatelů identity, může být poskytovatel identity pro prostředí G Suite buď Azure AD, nebo Google, ale ne současně současně.

5. **Otázka: Pokud je uživatel přihlášený prostřednictvím Windows, automaticky se ověří v G Suite, aniž by se zobrazila výzva k zadání hesla?**

    Odpověď: Existují dvě možnosti pro povolení tohoto scénáře. Nejdřív se uživatelé mohli do zařízení s Windows 10 přihlásit pomocí [Azure Active Directory JOIN](../device-management-introduction.md). Další možností je, že se uživatelé můžou přihlašovat do zařízení s Windows, která jsou připojená k doméně, do místní služby Active Directory, u které se povolilo jednotné přihlašování ke službě Azure AD prostřednictvím nasazení [Active Directory Federation Services (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) . Obě možnosti vyžadují, abyste provedli kroky v následujícím kurzu a povolili jste jednotné přihlašování mezi Azure AD a G Suite.

6. **Otázka: co mám dělat, když se zobrazí chybová zpráva "Neplatný e-mail"?**

    Odpověď: pro tuto instalaci je vyžadován atribut e-mail, aby se uživatelé mohli přihlásit. Tento atribut nelze nastavit ručně.

    Atribut e-mail je automaticky vyplněný pro každého uživatele s platnou licencí Exchange. Pokud uživatel není povolený e-mailem, zobrazí se tato chyba, protože aplikace potřebuje získat přístup k tomuto atributu.

    Můžete přejít na portal.office.com s účtem správce, potom kliknout na centrum pro správu, fakturace, předplatná, vybrat předplatné Office 365 a potom kliknout na přiřadit k uživatelům, vybrat uživatele, u kterých chcete ověřit předplatné, a v pravém podokně kliknout na Upravte licence.

    Po přiřazení licence O365 může trvat několik minut, než se použije. Potom bude automaticky vyplněn atribut User. mail, který by měl vyřešit problém.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* G Suite podporuje jednotné přihlašování (SSO) iniciované **SP**

* G Suite podporuje [ **automatizované** zřizování uživatelů.](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

## <a name="adding-g-suite-from-the-gallery"></a>Přidání G Suite z Galerie

Pokud chcete nakonfigurovat integraci G Suite do Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat G Suite z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** zadejte do vyhledávacího pole **G Suite** .
1. Vyberte **G Suite** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Konfigurace a testování jednotného přihlašování Azure AD pro G Suite

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí G Suite pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte v G Suite vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí G Suite, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[NAKONFIGURUJTE jednotné přihlašování G Suite](#configure-g-suite-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte testovacího uživatele G Suite](#create-g-suite-test-user)** , abyste měli protějšek B. Simon v g Suite, který je propojený s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace s aplikacemi **G Suite** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurační oddíl SAML** , pokud chcete nakonfigurovat pro **Gmail** , proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`.

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. Pokud chcete pro **Google Cloud Platform** nakonfigurovat **základní konfigurační oddíl SAML** , proveďte následující kroky:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`.

    b. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné přihlašovací adresy URL a identifikátoru. G Suite při konfiguraci jednotného přihlašování neposkytuje hodnotu ID nebo identifikátoru entity, takže když zrušíte kontrolu pro možnost **vystavitele specifické pro doménu** , hodnota identifikátoru bude `google.com`. Pokud zaškrtnete možnost **vystavitele specifické pro doménu** , bude `google.com/a/<yourdomainname.com>`. Pokud chcete zaškrtnout/zrušit kontrolu pro **vystavitele specifické pro doménu** , musíte přejít do oddílu **Konfigurace G Suite jednotného přihlašování** , které se vysvětluje později v tomto kurzu. Další informace získáte od [týmu podpory pro klienty pro sadu G Suite](https://www.google.com/contact/).

1. Vaše aplikace G Suite očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Příklad ukazuje následující snímek obrazovky. Výchozí hodnotou **jedinečného identifikátoru uživatele** je **User. userPrincipalName,** ale G Suite očekává, že tato hodnota bude namapována pomocí e-mailové adresy uživatele. Pro tuto funkci můžete použít atribut **User. mail** ze seznamu nebo použít odpovídající hodnotu atributu na základě konfigurace vaší organizace.

    ![obrázek](common/edit-attribute.png)

1. V části **deklarace identity uživatelů** v dialogovém okně **atributy uživatele** upravte deklarace pomocí **ikony upravit** nebo přidejte deklarace pomocí **Přidat novou deklaraci identity** , jak je znázorněno na obrázku výše, a proveďte následující kroky:

    | Name | Zdrojový atribut |
    | ---------------| --------------- |
    | Jedinečný identifikátor uživatele | Uživatel. pošta |

    a. Kliknutím na **Přidat novou deklaraci identity** otevřete dialogové okno **Spravovat deklarace identity uživatelů** .

    ![obrázek](common/new-save-attribute.png)

    ![obrázek](common/new-attribute-details.png)

    b. Do textového pole **název** zadejte název atributu zobrazeného pro tento řádek.

    r. Ponechte **obor názvů** prázdný.

    trojrozměrné. Jako **atribut**vyberte zdroj.

    cerebrální. V seznamu **zdrojový atribut** zadejte hodnotu atributu zobrazenou pro tento řádek.

    FJ. Klikněte na **OK** .

    věcn. Klikněte na **Uložit**.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení G Suite** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k G Suite.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **G Suite**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-g-suite-sso"></a>Konfigurace jednotného přihlašování G Suite

1. V prohlížeči otevřete novou kartu a přihlaste se ke [konzole pro správu G Suite](https://admin.google.com/) pomocí účtu správce.

2. Klikněte na **zabezpečení**. Pokud odkaz nevidíte, může být skrytý v nabídce **Další ovládací prvky** v dolní části obrazovky.

    ![Klikněte na zabezpečení.][10]

3. Na stránce **zabezpečení** klikněte na **nastavit jednotné přihlašování (SSO).**

    ![Klikněte na jednotné přihlašování.][11]

4. Proveďte následující změny konfigurace:

    ![Konfigurace jednotného přihlašování][12]

    a. Vyberte **nastavení jednotného přihlašování s poskytovatelem identity od jiného výrobce**.

    b. V poli **Adresa URL přihlašovací stránky** v G Suite vložte hodnotu **adresy URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    r. V poli **Adresa URL stránky** pro odhlášení v G Suite vložte hodnotu **Adresa URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    trojrozměrné. V poli **změnit adresu URL hesla** v G Suite vložte hodnotu **Adresa URL pro změnu hesla** , kterou jste zkopírovali z Azure Portal.

    cerebrální. V G Suite pro **ověřovací certifikát**Nahrajte certifikát, který jste stáhli z Azure Portal.

    FJ. Zaškrtnout/zrušit kontrolu **použití možnosti vystavitele specifické pro doménu** podle poznámky uvedené výše v části **základní konfigurace SAML** ve službě Azure AD.

    věcn. Klikněte na **Uložit změny**.

### <a name="create-g-suite-test-user"></a>Vytvořit testovacího uživatele G Suite

Cílem této části je vytvořit uživatele s názvem B. Simon v softwaru G Suite. G Suite podporuje automatické zřizování, které je ve výchozím nastavení povolené. V této části není žádná akce. Pokud uživatel v softwaru G Suite ještě neexistuje, vytvoří se nový, když se pokusíte o přístup k softwaru G Suite.

> [!NOTE]
> Ujistěte se, že váš uživatel už existuje v G Suite, pokud zřizování ve službě Azure AD nebylo před testováním jednotného přihlašování zapnuté.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Google](https://www.google.com/contact/).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici G Suite na přístupovém panelu byste měli být automaticky přihlášení do sady G, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Konfigurace zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [Vyzkoušejte si G Suite s Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png