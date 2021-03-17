---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s SignalFx | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a SignalFx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.openlocfilehash: 1fbc42864761360d252ed62cea1aef6f2937b599
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516065"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s SignalFx

V tomto kurzu se dozvíte, jak integrovat SignalFx s Azure Active Directory (Azure AD). Když integrujete SignalFx s Azure AD, můžete:

* Řízení z Azure AD, kteří mají přístup k SignalFx;
* Umožněte, aby se vaši uživatelé automaticky přihlásili k SignalFx svým účtům Azure AD; ani
* Spravujte svoje účty na jednom místě (Azure Portal).

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Než začnete, budete potřebovat:

* Předplatné Azure AD
    * Pokud předplatné nemáte, můžete si [bezplatný účet získat tady](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotným přihlašováním (SSO) SignalFx

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu budete konfigurovat a testovat jednotné přihlašování Azure AD v testovacím prostředí.

* SignalFx podporuje jednotné přihlašování **IDP** .
* SignalFx podporuje zřizování uživatelů **jenom v čase** .
* Po nakonfigurování SignalFx můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Krok 1: Přidání aplikace SignalFx v Azure

Pomocí těchto pokynů přidejte aplikaci SignalFx do seznamu spravovaných aplikací SaaS.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V navigačním okně na levé straně vyberte **Azure Active Directory**.
1. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. Vyberte **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte a vyberte **SignalFx**.
     * Je možné, že budete muset několik minut počkat, než se aplikace přidá do vašeho tenanta.
1. Nechejte Azure Portal otevřít a pak otevřete novou kartu Web.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Krok 2: začátek konfigurace jednotného přihlašování SignalFx

Pomocí těchto pokynů můžete zahájit proces konfigurace SignalFx jednotného přihlašování.

1. Na nově otevřené kartě získáte přístup k uživatelskému rozhraní SignalFx a přihlaste se k němu. 
1. V horní nabídce klikněte na **integrace**. 
1. Do vyhledávacího pole zadejte a vyberte **Azure Active Directory**.
1. Klikněte na **vytvořit novou integraci**.
1. Do **název**zadejte snadno rozpoznatelný název, který budou uživatelé rozumět.
1. Značka **Zobrazit na přihlašovací stránce**
    * Tato funkce zobrazí vlastní tlačítko na přihlašovací stránce, na které můžou uživatelé kliknout. 
    * Informace, které jste zadali v poli **název** , se zobrazí na tlačítku. V důsledku toho zadejte **název** , který budou uživatelé rozpoznávat. 
    * Tato možnost bude fungovat jenom v případě, že pro aplikaci SignalFx používáte vlastní subdoménu, jako je například **yourcompanyname.signalfx.com**. Pokud chcete získat vlastní subdoménu, obraťte se na podporu SignalFx. 
1. Zkopírujte **ID integrace**. Tyto informace budete potřebovat v pozdějším kroku. 
1. Ponechte uživatelské rozhraní SignalFx otevřené. 

## <a name="step-3-configure-azure-ad-sso"></a>Krok 3: Konfigurace jednotného přihlašování služby Azure AD

Pomocí těchto pokynů povolte jednotné přihlašování služby Azure AD v Azure Portal.

1. Vraťte se do [Azure Portal](https://portal.azure.com/)a na stránce integrace aplikací **SignalFx** vyhledejte část **Správa** a pak vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu pera (Upravit) pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** vyplňte následující pole: 

    a. Do pole **identifikátor**zadejte následující adresu URL `https://api.<realm>.signalfx.com/v1/saml/metadata` a nahraďte ji `<realm>` svou sférou SignalFx. 

    b. Do pole **Adresa URL odpovědi**zadejte následující adresu URL `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` a nahraďte ji `<realm>` svou sférou SignalFx a `<integration ID>` **ID integrace** , které jste zkopírovali dříve z uživatelského rozhraní SignalFx.

1. SignalFx aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. 
    
1. Zkontrolujte a ověřte, zda jsou následující deklarace identity mapovány na zdrojové atributy, které jsou vyplněny ve službě Active Directory. 

    | Name |  Zdrojový atribut|
    | ------------------- | -------------------- |
    | User. FirstName  | User. křestní jméno |
    | Uživatel. e-mail  | uživatel. pošta |
    | PersonImmutableID       | User. userPrincipalName    |
    | User. LastName       | User. příjmení    |

    > [!NOTE]
    > Tento proces vyžaduje, aby vaše služba Active Directory byla nakonfigurovaná aspoň s jednou ověřenou vlastní doménou a měla přístup k e-mailovým účtům v této doméně. Pokud si nejste jistí nebo potřebujete pomoc s touto konfigurací, obraťte se prosím na podporu SignalFx.  

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** Najděte **certifikát (Base64)** a pak vyberte **Stáhnout**. Stáhněte si certifikát a uložte ho do svého počítače. Pak zkopírujte hodnotu **adresy URL federačních metadat aplikace** . Tyto informace budete potřebovat v pozdějším kroku v uživatelském rozhraní SignalFx. 

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení SignalFx** Zkopírujte hodnotu **identifikátoru Azure AD** . Tyto informace budete potřebovat v pozdějším kroku v uživatelském rozhraní SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Krok 4: Vytvoření testovacího uživatele Azure AD

Pomocí těchto pokynů můžete vytvořit testovacího uživatele ve Azure Portal s názvem **B. Simon**.

1. V Azure Portal v levém navigačním okně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části stránky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** :
   1. Do **uživatelského jména**zadejte `username@companydomain.extension` , například `b.simon@contoso.com` .
   1. Do **název**zadejte `B.Simon` .
   1. Označte **Zobrazit heslo**a potom zkopírujte zobrazenou hodnotu v **hesle**. Tyto informace budete potřebovat v pozdějším kroku, aby bylo možné tuto integraci otestovat. 
   1. Klikněte na **Vytvořit**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Krok 5: přiřazení testovacího uživatele Azure AD

Pomocí těchto pokynů můžete testovacímu uživateli povolit použití jednotného přihlašování Azure pro SignalFx.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **SignalFx**.
1. Na stránce Přehled aplikace najděte část **Správa** a pak vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** v seznamu **Uživatelé** vyberte **B. Simon**a potom v dolní části stránky klikněte na **Vybrat**.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a potom klikněte na **Vybrat** v dolní části stránky.
1. V dialogovém okně **Přidat přiřazení** klikněte na **přiřadit**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Krok 6: dokončení konfigurace jednotného přihlašování SignalFx 

1. Otevřete předchozí kartu a vraťte se do uživatelského rozhraní SignalFx, abyste zobrazili aktuální stránku Azure Active Directory Integration. 
1. V poli **certifikát (Base64)** klikněte na **nahrát soubor**a vyhledejte soubor certifikátu s **kódováním base64** , který jste předtím stáhli z Azure Portal.
1. Vedle pole **identifikátor Azure AD**vložte hodnotu **identifikátoru Azure AD** , kterou jste zkopírovali dříve z Azure Portal. 
1. Do pole **Adresa URL federačních metadat**vložte hodnotu **URL federačních metadat aplikace** , kterou jste zkopírovali dříve z Azure Portal. 
1. Klikněte na **Uložit**.

## <a name="step-7-test-sso"></a>Krok 7: ověření jednotného přihlašování

Přečtěte si následující informace týkající se testování jednotného přihlašování a také očekávání při prvním přihlášení do SignalFx. 

### <a name="test-logins"></a>Testování přihlášení

* K otestování přihlášení byste měli použít soukromé nebo anonymním okno, nebo se můžete odhlásit z Azure Portal. V takovém případě soubory cookie pro uživatele, kteří aplikaci nakonfigurovali, narušují a zabraňují úspěšnému přihlášení pomocí testovacího uživatele.

* Při prvním přihlášení se k novému testovacímu uživateli Azure vynutí změnu hesla. V takovém případě se přihlašovací proces jednotného přihlašování nedokončí. testovací uživatel bude přesměrován na Azure Portal. Chcete-li vyřešit potíže, měl by testovací uživatel změnit heslo, přejít na přihlašovací stránku SignalFx nebo na přístupový panel a akci opakovat.
    * Po kliknutí na dlaždici SignalFx na přístupovém panelu byste měli být automaticky přihlášeni k SignalFx. 
        * Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

* K aplikaci SignalFx se dá získat přístup prostřednictvím přístupového panelu nebo vlastní přihlašovací stránky přiřazené k organizaci. Testovací uživatel by měl otestovat integraci od některého z těchto umístění.
    * Testovací uživatel může použít přihlašovací údaje vytvořené dříve v tomto procesu pro **b. simon \@ contoso.com**.

### <a name="first-time-logins"></a>Přihlašovací jména při prvním přihlášení

* Když se uživatel poprvé přihlásí k SignalFx z jednotného přihlašování SAML, dostane e-mail s odkazem na SignalFx. Uživatel musí kliknout na odkaz pro účely ověřování. Toto ověření e-mailu bude provedeno jenom pro uživatele, kteří budou první čas. 

* SignalFx podporuje vytvoření uživatelem **pouze v čase** , což znamená, že pokud uživatel v SignalFx neexistuje, bude účet uživatele vytvořen při prvním pokusu o přihlášení.

## <a name="additional-resources"></a>Další materiály

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Vyzkoušejte si SignalFx s Azure AD](https://aad.portal.azure.com/)