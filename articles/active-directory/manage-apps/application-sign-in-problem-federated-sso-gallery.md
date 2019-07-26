---
title: Problémy s přihlašováním do aplikace v galerii federovaného jednotného přihlašování | Microsoft Docs
description: Doprovodné materiály k určitým chybám při přihlašování do aplikace, kterou jste nakonfigurovali pro federované jednotné přihlašování založené na SAML pomocí Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32f3b2f45a808ebfa71f456c015de3dd59d60bd9
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381366"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problémy s přihlašováním k aplikaci Galerie nakonfigurované pro federované jednotné přihlašování

Pokud chcete řešit potíže s přihlašováním níže, doporučujeme vám postupovat podle těchto návrhů a získat tak lepší diagnostiku a automatizovat postup řešení:

- Pokud chcete zajistit lepší diagnostiku a řešení při použití zkušebního prostředí v Azure Portal, nainstalujte [rozšíření prohlížeče moje aplikace](access-panel-extension-problem-installing.md) na pomoc Azure Active Directory (Azure AD).
- Reprodukuje chybu pomocí prostředí testování na stránce konfigurace aplikace v Azure Portal. Další informace o [ladění aplikací jednotného přihlašování založeného na SAML](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři.

*Chyba AADSTS70001: V adresáři\/* se nenašla aplikace s identifikátorem https:/contoso.com.

**Možná příčina**

`Issuer` Atribut odeslaný z aplikace do služby Azure AD v požadavku SAML neodpovídá hodnotě identifikátoru, která je nakonfigurovaná pro aplikaci ve službě Azure AD.

**Řešení**

Zajistěte `Issuer` , aby se atribut v požadavku SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD. Pokud používáte prostředí pro [testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků.

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

1.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

1.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte, zda se hodnota v textovém poli identifikátoru shoduje s hodnotou identifikátoru zobrazeného v chybě.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Tato adresa pro odpovědi neodpovídá adresám pro odpovědi nakonfigurovaným pro danou aplikaci.

*Chyba AADSTS50011: Adresa pro odpověď "https:\//contoso.com" neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci.*

**Možná příčina**

`AssertionConsumerServiceURL` Hodnota v požadavku SAML se neshoduje s hodnotou adresy URL odpovědi nebo vzorem nakonfigurovaným ve službě Azure AD. `AssertionConsumerServiceURL` Hodnota v požadavku SAML je adresa URL, která se zobrazí v chybě.

**Řešení**

Zajistěte `AssertionConsumerServiceURL` , aby hodnota v požadavku SAML odpovídala hodnotě adresy URL odpovědi nakonfigurované ve službě Azure AD. Pokud používáte prostředí pro [testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků.

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

1.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

1.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte nebo aktualizujte hodnotu v textovém poli adresy URL odpovědi tak, `AssertionConsumerServiceURL` aby odpovídala hodnotě v požadavku SAML.    
    
Po aktualizaci hodnoty adresy URL odpovědi v Azure AD, která odpovídá hodnotě odeslané aplikací v žádosti SAML, byste měli být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživatel nemá přiřazenou roli.

*Chyba AADSTS50105: Přihlášený uživatel "Brian\@contoso.com" není přiřazen k roli pro aplikaci.*

**Možná příčina**

Uživateli nebyl udělen přístup k aplikaci ve službě Azure AD.

**Řešení**

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků. Pokud používáte prostředí pro [testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků.

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce**.

1.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

1.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

1.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

1.  V seznamu aplikací vyberte ten, ke kterému chcete přiřadit uživatele.

1.  Po načtení aplikace vyberte **Uživatelé a skupiny** z nabídky navigace na levé straně aplikace.

1.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

1.  V podokně **Přidat přiřazení** vyberte selektor **Uživatelé a skupiny** .

1. Do vyhledávacího pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a příjmení nebo e-mailovou adresu uživatele, kterého chcete přidat.

1. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga uživatele přidáte uživatele do **vybraného** seznamu.

1. **Volitelné** Chcete-li **Přidat více než jednoho uživatele**, zadejte do pole **Hledat podle názvu nebo e-mailové adresy** jiné celé jméno nebo e-mailovou adresu a kliknutím na zaškrtávací políčko přidejte uživatele do **vybraného** seznamu.

1. Až budete s výběrem uživatelů hotovi, klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

1. **Volitelné** Kliknutím na výběr **Role vyberte** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

1. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace pomocí metod popsaných v části popis řešení.

## <a name="not-a-valid-saml-request"></a>Nejedná se o platnou žádost SAML.

*Chyba AADSTS75005: Požadavek není platná zpráva protokolu typu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML odeslaný aplikací pro jednotné přihlašování. Mezi běžné problémy patří:

-   V požadavku SAML chybí povinná pole.
-   Kódovaná metoda požadavku SAML

**Řešení**

1. Zachytit požadavek SAML. V tomto kurzu se dozvíte, jak pomocí [SAML ladit jednotné přihlašování k aplikacím ve službě Azure AD](../develop/howto-v1-debug-saml-sso-issues.md) a Naučte se zachytit požadavek SAML.

1. Obraťte se na dodavatele aplikace a podělte se s ním o následující informace:

   -   Požadavek SAML

   -   [Požadavky protokolu SAML jednotného přihlašování Azure AD](../develop/single-sign-on-saml-protocol.md)

Dodavatel aplikace by měl ověřit, jestli podporují implementaci Azure AD SAML pro jednotné přihlašování.

## <a name="misconfigured-application"></a>Chybně nakonfigurovaná aplikace

*Chyba AADSTS650056: Chybně nakonfigurovaná aplikace Může to být způsobené jedním z následujících důvodů: Klient neuvádí žádná oprávnění pro ' AAD Graph ' v požadovaných oprávněních v registraci aplikace klienta. Nebo správce v tenantovi nesouhlasí. Případně můžete v žádosti zkontrolovat identifikátor aplikace a zajistit, aby odpovídaly nakonfigurovanému identifikátoru klientské aplikace. Obraťte se prosím na správce, aby opravil konfiguraci nebo souhlas jménem tenanta.* .

**Možná příčina**

`Issuer` Atribut odeslaný z aplikace do služby Azure AD v žádosti SAML se neshoduje s hodnotou identifikátoru nakonfigurovanou pro aplikaci ve službě Azure AD.

**Řešení**

Zajistěte `Issuer` , aby se atribut v požadavku SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD. Pokud používáte [prostředí testování](../develop/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle těchto kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

1.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

1.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte, zda se hodnota v textovém poli identifikátoru shoduje s hodnotou identifikátoru zobrazeného v chybě.


## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíč není nakonfigurovaný.

*Chyba AADSTS50003: Není nakonfigurovaný žádný podpisový klíč.*

**Možná příčina**

Objekt aplikace je poškozený a Azure AD nerozpozná certifikát nakonfigurovaný pro aplikaci.

**Řešení**

Chcete-li odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

1. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

1. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

1. Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

1. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

1. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

1. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

1. V části **podpisový certifikát SAML** vyberte **vytvořit nový certifikát** .

1. Vyberte datum vypršení platnosti a pak klikněte na **Uložit**.

1. Pokud chcete aktivní certifikát přepsat, zkontrolujte, **že je aktivní nový certifikát** . Pak v horní části podokna klikněte na **Uložit** a přijměte aktivaci certifikátu výměny.

1. V části **podpisový certifikát SAML** kliknutím na **Odebrat** odeberte nepoužitý certifikát.

## <a name="saml-request-not-present-in-the-request"></a>Požadavek SAML není v žádosti přítomen.

*Chyba AADSTS750054: SAMLRequest nebo SAMLResponse musí být přítomné jako parametry řetězce dotazu v požadavku HTTP pro vazbu přesměrování SAML.*

**Možná příčina**

Služba Azure AD nemohla identifikovat požadavek SAML v rámci parametrů adresy URL v požadavku HTTP. K tomu může dojít, když aplikace při odesílání požadavku SAML do Azure AD nepoužívá vazbu přesměrování HTTP.

**Řešení**

Aplikace potřebuje odeslat požadavek SAML zakódovaný do hlavičky umístění pomocí vazby přesměrování HTTP. Další informace o způsobu implementace najdete v části věnované vazbě HTTP Redirect v [dokumentu specifikace protokolu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD posílá token do nesprávného koncového bodu.

**Možná příčina**

Pokud v rámci jednotného přihlašování požadavek na přihlášení neobsahuje adresu URL explicitní odpovědi (adresa URL služby potvrzení), služba Azure AD vybere kteroukoli z konfigurovaných adres URL pro danou aplikaci. I když má aplikace nakonfigurovanou adresu URL pro explicitní odpověď, může být uživatel přesměrován https://127.0.0.1:444. 

Při přidávání aplikace jako aplikace mimo galerii služba Azure Active Directory vytvořila tuto adresu URL pro odpověď jako výchozí hodnotu. Toto chování se změnilo a Azure Active Directory už tuto adresu URL ve výchozím nastavení nepřidává. 

**Řešení**

Odstraní nepoužívané adresy URL odpovědí nakonfigurované pro aplikaci.

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .

4.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.

5.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

6.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7.  Po načtení aplikace otevřete **základní konfiguraci SAML**. V **adrese URL odpovědi (adresa URL služby vyhodnocení zákazníka)** Odstraňte nepoužívané nebo výchozí adresy URL odpovědí vytvořené systémem. Například, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobení deklarací SAML odeslaných do aplikace

Informace o tom, jak přizpůsobit deklarace atributů SAML odeslané do vaší aplikace, najdete [v tématu Mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Další kroky

[Jak ladit jednotné přihlašování založené na SAML pro aplikace v Azure AD](../develop/howto-v1-debug-saml-sso-issues.md)
