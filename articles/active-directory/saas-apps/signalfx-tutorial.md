---
title: 'Kurz: Integrace jednotného přihlašování (SSO) služby Azure Active Directory s SignalFx | Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149718dcd325ef6bd6a6754ba100ffdc34be0a07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79136410"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Kurz: Integrace jednotného přihlašování (SSO) služby Azure Active Directory s SignalFx

V tomto kurzu se dozvíte, jak integrovat SignalFx s Azure Active Directory (Azure AD). Když integrujete SignalFx s Azure AD, můžete:

* Řízení z Azure AD, který má přístup k SignalFx;
* Umožněte uživatelům, aby se automaticky přihlásili k SignalFx pomocí svých účtů Azure AD. A
* Spravujte své účty na jednom místě (na webu Azure Portal).

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

* Předplatné Azure AD
    * Pokud nemáte předplatné, můžete získat [bezplatný účet zde](https://azure.microsoft.com/free/).
* Předplatné s povoleným jednotném přihlašování SignalFx (SSO)

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete přisypzové zabezpečení Azure AD v testovacím prostředí.

* SignalFx podporuje iniciované sso iniciované **IDP**
* SignalFx podporuje zřizování uživatelů **just in time**
* Jakmile nakonfigurujete SignalFx, můžete vynutit řízení relace, které chrání exfiltraci a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutit řízení relací pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="step-1-add-the-signalfx-application-in-azure"></a>Krok 1: Přidání aplikace SignalFx v Azure

Pomocí těchto pokynů přidejte aplikaci SignalFx do seznamu spravovaných aplikací SaaS.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém navigačním okně vyberte **Možnost Azure Active Directory**.
1. Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.
1. Vyberte **Nová aplikace**.
1. V části **Přidat z galerie** do vyhledávacího pole zadejte a vyberte **SignalFx**.
     * Možná budete muset počkat několik minut pro aplikaci, které mají být přidány do vašeho tenanta.
1. Nechte portál Azure otevřený a pak otevřete novou webovou kartu.    

## <a name="step-2-begin-signalfx-sso-configuration"></a>Krok 2: Zahájení konfigurace spřiho v správě SignalFx

Pomocí těchto pokynů můžete zahájit proces konfigurace pro steso SignalFx.

1. Na nově otevřené kartě se přihlašuje te k ujmu a přihlašuj se do uzlení SignalFx. 
1. V horní nabídce klikněte na **Integrace .** 
1. Do vyhledávacího pole zadejte a vyberte **službu Azure Active Directory**.
1. Klepněte na **tlačítko Vytvořit novou integraci**.
1. Do **pole Název**zadejte snadno rozpoznatelný název, kterému uživatelé porozumí.
1. Označit **zobrazit na přihlašovací stránce**.
    * Tato funkce zobrazí na přihlašovací stránce přizpůsobené tlačítko, na které mohou uživatelé kliknout. 
    * Informace zadané v části **Název** se zobrazí na tlačítku. V důsledku toho zadejte **název,** který uživatelé poznají. 
    * Tato možnost bude fungovat pouze v případě, že používáte vlastní subdoménu pro aplikaci SignalFx, například **yourcompanyname.signalfx.com**. Chcete-li získat vlastní subdoménu, obraťte se na podporu SignalFx. 
1. Zkopírujte **ID integrace**. Tyto informace budete potřebovat v pozdějším kroku. 
1. Ponechte ui SignalFx otevřené. 

## <a name="step-3-configure-azure-ad-sso"></a>Krok 3: Konfigurace přizpůsobovaného přizpůsobovaného služby Azure AD

Pomocí těchto pokynů povolte azure ad přisychací služby na webu Azure Portal.

1. Vraťte se na [portál Azure](https://portal.azure.com/)a na stránce integrace aplikace **SignalFx** **vyhledejte** část Správa a vyberte **Možnost Jednotné přihlašování**.
1. Na stránce **Vybrat metodu jednotného přihlašování** vyberte **možnost SAML**.
1. Na stránce **Nastavit jednotné přihlašování pomocí saml** klikněte na ikonu pera (upravit pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Na stránce **Nastavit jednotné přihlašování pomocí saml** vyplňte následující pole: 

    a. Do **identifikátoru**zadejte `https://api.<realm>.signalfx.com/v1/saml/metadata` následující `<realm>` adresu URL a nahraďte ji sférou SignalFx. 

    b. V **url odpovědi**zadejte `https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>` následující `<realm>` adresu URL a nahraďte `<integration ID>` sférou SignalFx, stejně jako **ID integrace,** které jste zkopírovali dříve z ui SignalFx.

1. Aplikace SignalFx očekává kontrolní výrazy SAML v určitém formátu, který vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. 
    
1. Zkontrolujte a ověřte, zda se následující deklarace identity mapují na zdrojové atributy, které jsou naplněny ve službě Active Directory. 

    | Name (Název) |  Atribut zdroje|
    | ------------------- | -------------------- |
    | Jméno uživatele  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | Jméno uživatele.Příjmení       | user.příjmení    |

    > [!NOTE]
    > Tento proces vyžaduje, aby služba Active Directory byla nakonfigurována alespoň s jednou ověřenou vlastní doménou a také má přístup k e-mailovým účtům v této doméně. Pokud si nejste jisti nebo potřebujete pomoc s touto konfigurací, obraťte se na podporu SignalFx.  

1. Na stránce **Nastavit jednotné přihlašování pomocí saml** v části **Podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a pak vyberte **Stáhnout**. Stáhněte si certifikát a uložte jej do počítače. Potom zkopírujte hodnotu **url metadat federace aplikace;** budete potřebovat tyto informace v pozdějším kroku v ui SignalFx. 

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavit SignalFx** zkopírujte hodnotu **identifikátoru Azure AD.** Budete potřebovat tyto informace v pozdějším kroku v ui SignalFx. 

## <a name="step-4-create-an-azure-ad-test-user"></a>Krok 4: Vytvoření testovacího uživatele Azure AD

Pomocí těchto pokynů vytvořte testovacího uživatele na webu Azure portal s názvem **B.Simon**.

1. Na webu Azure Portal v levém navigačním okně vyberte **Azure Active Directory**, pak vyberte **Uživatelé**a pak vyberte **Všichni uživatelé**.
1. V horní části stránky vyberte **Nový uživatel**.
1. Ve vlastnostech **Uživatele:**
   1. Do **pole Uživatelské jméno**zadejte `username@companydomain.extension`, například `b.simon@contoso.com`.
   1. Do **pole** `B.Simon`Název zadejte .
   1. Označit **zobrazit heslo**a potom zkopírovat zobrazenou hodnotu v části **Heslo**. Tyto informace budete potřebovat v pozdějším kroku, abyste mohli tuto integraci otestovat. 
   1. Klikněte na **Vytvořit**.

## <a name="step-5-assign-the-azure-ad-test-user"></a>Krok 5: Přiřazení testovacího uživatele Azure AD

Pomocí těchto pokynů povolte testovacímu uživateli používat jednotné přihlašování Azure pro SignalFx.

1. Na portálu Azure vyberte **Podnikové aplikace**a pak vyberte **Všechny aplikace**.
1. V seznamu aplikací vyberte **SignalFx**.
1. Na stránce s přehledem aplikace najděte část **Spravovat** a vyberte **Uživatelé a skupiny**.

   ![Odkaz "Uživatelé a skupiny"](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a potom v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu **Uživatelé** v yberte **B.Simon**a v dolní části stránky klepněte na tlačítko **Vybrat**.
1. Pokud očekáváte libovolnou hodnotu role v kontrolním výrazu SAML, vyberte v dialogovém okně **Vybrat roli** příslušnou roli pro uživatele ze seznamu a potom klikněte na **Vybrat** v dolní části stránky.
1. V dialogovém okně **Přidat přiřazení** klepněte na tlačítko **Přiřadit**.

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>Krok 6: Dokončení konfigurace spřiho zabezpečení SignalFx 

1. Otevřete předchozí kartu a vraťte se do unového rozhraní SignalFx a zobrazte aktuální stránku integrace služby Azure Active Directory. 
1. Vedle **certifikátu (Base64)** klikněte na **Nahrát soubor**a vyhledejte soubor **certifikátu zakódovaný base64,** který jste si předtím stáhli z webu Azure Portal.
1. Vedle **identifikátoru Azure AD**vložte hodnotu **identifikátoru Azure AD,** kterou jste zkopírovali dříve z webu Azure Portal. 
1. Vedle **adresy URL metadat federace**vložte hodnotu **url metadat federace aplikace,** kterou jste zkopírovali dříve z webu Azure Portal. 
1. Klikněte na **Uložit**.

## <a name="step-7-test-sso"></a>Krok 7: Testování přisuzu je

Přečtěte si následující informace týkající se testování přihlašování k opětovnému přihlašování, stejně jako očekávání pro přihlášení do SignalFx poprvé. 

### <a name="test-logins"></a>Testování přihlášení

* Chcete-li otestovat přihlášení, měli byste použít soukromé / anonymní okno, nebo se můžete odhlásit z portálu Azure. Pokud ne, soubory cookie pro uživatele, který aplikaci nakonfiguroval, budou zasahovat a bránit úspěšnému přihlášení k testovacímu uživateli.

* Když se nový testovací uživatel přihlásí poprvé, Azure vynutí změnu hesla. V takovém případě proces přihlášení při hlášce přihlašování nebude dokončena; testovací uživatel bude přesměrován na portál Azure. Chcete-li poradce při potížích, testovací uživatel by měl změnit své heslo a přejděte na přihlašovací stránku SignalFx nebo na přístupový panel a zkuste to znovu.
    * Po kliknutí na dlaždici SignalFx na přístupovém panelu, měli byste být automaticky přihlášeni do SignalFx. 
        * Další informace o přístupovém panelu naleznete [v tématu Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

* Aplikace SignalFx je přístupná z přístupového panelu nebo prostřednictvím vlastní přihlašovací stránky přiřazené organizaci. Testovací uživatel by měl otestovat integraci počínaje některou z těchto umístění.
    * Testovací uživatel může použít pověření vytvořená dříve **b.simon@contoso.com**v tomto procesu pro aplikaci .

### <a name="first-time-logins"></a>První přihlášení

* Když se uživatel přihlásí do SignalFx z saml sso sondy poprvé, uživatel obdrží e-mail SignalFx s odkazem. Uživatel musí kliknout na odkaz pro účely ověřování. Toto ověření e-mailu proběhne pouze pro první uživatele. 

* SignalFx podporuje vytvoření uživatele **Just In Time,** což znamená, že pokud uživatel v SignalFx neexistuje, bude účet uživatele vytvořen při prvním pokusu o přihlášení.

## <a name="additional-resources"></a>Další zdroje

- [Seznam výukových programů o integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Služby Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Vyzkoušejte SignalFx pomocí Azure AD](https://aad.portal.azure.com/)