---
title: Problémy s přihlašováním k aplikacím nakonfigurovaným pomocí jednotného přihlašování založené na SAML
description: Doprovodné materiály k určitým chybám při přihlašování do aplikace, kterou jste nakonfigurovali pro federované jednotné přihlašování založené na SAML pomocí Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperfq2
ms.openlocfilehash: ec39a6d106973808e26b7c06dce8b3054af490ff
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427376"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>Problémy s přihlašováním k aplikacím nakonfigurovaným pomocí jednotného přihlašování založené na SAML
Chcete-li vyřešit potíže s přihlášením, doporučujeme následující postup, který vám umožní lépe diagnostikovat a automatizovat kroky řešení:

- Pokud chcete zajistit lepší diagnostiku a řešení při použití zkušebního prostředí v Azure Portal, nainstalujte [rozšíření prohlížeče moje aplikace](access-panel-extension-problem-installing.md) na pomoc Azure Active Directory (Azure AD).
- Reprodukuje chybu pomocí prostředí testování na stránce konfigurace aplikace v Azure Portal. Další informace o [ladění aplikací jednotného přihlašování založeného na SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

Pokud používáte prostředí pro [testování](../azuread-dev/howto-v1-debug-saml-sso-issues.md) v Azure Portal s rozšířením zabezpečeného prohlížeče moje aplikace, nemusíte ručně postupovat podle následujících pokynů pro otevření konfigurační stránky jednotného přihlašování založené na SAML.

Otevření konfigurační stránky jednotného přihlašování založené na SAML:
1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.
1.  Otevřete **rozšíření Azure Active Directory** tím, že vyberete **všechny služby** v horní části hlavní navigační nabídky vlevo.
1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .
1.  Vyberte **podnikové aplikace** z navigační nabídky Azure Active Directory vlevo.
1.  Výběrem **všech aplikací** zobrazíte seznam všech aplikací.
    Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace**.
1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.
1. Po načtení aplikace vyberte v levé navigační nabídce aplikace **jednotné přihlašování** .
1. Vyberte jednotné přihlašování založené na SAML.

## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři.
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**Možná příčina**

`Issuer`Atribut odeslaný z aplikace do služby Azure AD v požadavku SAML neodpovídá hodnotě identifikátoru, která je nakonfigurovaná pro aplikaci ve službě Azure AD.

**Řešení**

Zajistěte, aby se `Issuer` atribut v požadavku SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD.

Na stránce konfigurace jednotného přihlašování založeného na SAML ověřte v části **základní konfigurační oddíl SAML** , že hodnota v textovém poli identifikátoru odpovídá hodnotě identifikátoru zobrazené v chybě.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adresa pro odpověď neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci.
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**Možná příčina**

`AssertionConsumerServiceURL`Hodnota v požadavku SAML se neshoduje s hodnotou adresy URL odpovědi nebo vzorem nakonfigurovaným ve službě Azure AD. `AssertionConsumerServiceURL`Hodnota v požadavku SAML je adresa URL, která se zobrazí v chybě.

**Řešení**

Zajistěte, aby `AssertionConsumerServiceURL` hodnota v požadavku SAML odpovídala hodnotě adresy URL odpovědi nakonfigurované ve službě Azure AD. 

Ověřte nebo aktualizujte hodnotu v textovém poli adresy URL odpovědi tak, aby odpovídala `AssertionConsumerServiceURL` hodnotě v požadavku SAML.   
    
Po aktualizaci hodnoty adresy URL odpovědi v Azure AD, která odpovídá hodnotě odeslané aplikací v žádosti SAML, byste měli být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživatel nemá přiřazenou roli.
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**Možná příčina**

Uživateli se v Azure AD neudělil přístup k aplikaci.​

**Řešení**

Chcete-li jednomu nebo více uživatelům přiřadit aplikaci přímo, přečtěte si téma [rychlý Start: přiřazení uživatelů k aplikaci](add-application-portal-assign-users.md).

## <a name="not-a-valid-saml-request"></a>Nejedná se o platnou žádost SAML.
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**Možná příčina**

Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Mezi běžné problémy patří:
- Chybějící požadovaná pole v požadavku SAML
- Kódovaná metoda požadavku SAML

**Řešení**

1. Zachytit požadavek SAML. V tomto kurzu se dozvíte, jak pomocí [SAML ladit jednotné přihlašování k aplikacím ve službě Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) a Naučte se zachytit požadavek SAML.
1. Obraťte se na dodavatele aplikace a podělte se s ním o následující informace:
    - Požadavek SAML
    - [Požadavky protokolu SAML jednotného přihlašování Azure AD](../develop/single-sign-on-saml-protocol.md)

Dodavatel aplikace by měl ověřit, jestli podporují implementaci Azure AD SAML pro jednotné přihlašování.

## <a name="misconfigured-application"></a>Chybně nakonfigurovaná aplikace
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**Možná příčina**

`Issuer`Atribut odeslaný z aplikace do služby Azure AD v žádosti SAML se neshoduje s hodnotou identifikátoru nakonfigurovanou pro aplikaci ve službě Azure AD.

**Řešení**

Zajistěte, aby se `Issuer` atribut v požadavku SAML shodoval s hodnotou identifikátoru nakonfigurovanou v Azure AD. 

Ověřte, zda se hodnota v textovém poli identifikátoru shoduje s hodnotou identifikátoru zobrazeného v chybě.

## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíč není nakonfigurovaný.
`Error AADSTS50003: No signing key configured.`

**Možná příčina**

Objekt aplikace je poškozený a Azure AD nerozpozná certifikát nakonfigurovaný pro aplikaci.

**Řešení**

Chcete-li odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:
1. Na obrazovce konfigurace jednotného přihlašování založené na SAML vyberte v části **podpisový certifikát SAML** možnost **vytvořit nový certifikát** .
1. Vyberte datum vypršení platnosti a pak klikněte na **Uložit**.
1. Pokud chcete aktivní certifikát přepsat, zkontrolujte, **že je aktivní nový certifikát** . Pak v horní části podokna klikněte na **Uložit** a přijměte aktivaci certifikátu výměny.
1. V části **podpisový certifikát SAML** kliknutím na **Odebrat** odeberte **nepoužitý** certifikát.

## <a name="saml-request-not-present-in-the-request"></a>Požadavek SAML není v žádosti přítomen.
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**Možná příčina**

Služba Azure AD nemohla identifikovat požadavek SAML v rámci parametrů adresy URL v požadavku HTTP. K tomu může dojít, když aplikace při odesílání požadavku SAML do Azure AD nepoužívá vazbu přesměrování HTTP.

**Řešení**

Aplikace potřebuje odeslat požadavek SAML zakódovaný do hlavičky umístění pomocí vazby přesměrování HTTP. Další informace o způsobu implementace najdete v části věnované vazbě HTTP Redirect v [dokumentu specifikace protokolu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD posílá token do nesprávného koncového bodu.
**Možná příčina**

Pokud v rámci jednotného přihlašování požadavek na přihlášení neobsahuje adresu URL explicitní odpovědi (adresa URL služby potvrzení), služba Azure AD vybere kteroukoli z nakonfigurovaných adres URL odpovědí pro danou aplikaci. I když má aplikace nakonfigurovanou adresu URL pro explicitní odpověď, může být uživatel přesměrován https://127.0.0.1:444 . 

Při přidávání aplikace jako aplikace mimo galerii služba Azure Active Directory vytvořila tuto adresu URL pro odpověď jako výchozí hodnotu. Toto chování se změnilo a Azure Active Directory už tuto adresu URL ve výchozím nastavení nepřidává. 

**Řešení**

Odstraní nepoužívané adresy URL odpovědí nakonfigurované pro aplikaci.

Na stránce konfigurace jednotného přihlašování založeného na SAML v části **Adresa URL odpovědi (adresa URL služby vyhodnocení zákazníka)** Odstraňte nepoužívané nebo výchozí adresy URL odpovědí vytvořené systémem. Například, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobení deklarací SAML odeslaných do aplikace
Informace o tom, jak přizpůsobit deklarace atributů SAML odeslané do vaší aplikace, najdete [v tématu Mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="errors-related-to-misconfigured-apps"></a>Chyby související s nesprávně nakonfigurovanými aplikacemi
Ověřte, jak se obě konfigurace na portálu shodují s tím, co máte ve své aplikaci. Konkrétně Porovnejte ID klienta/aplikace, adresy URL odpovědi, tajné klíče klienta a klíče a identifikátor URI ID aplikace.

Porovnejte prostředek, ke kterému požadujete přístup, v kódu s nakonfigurovanými oprávněními na kartě **požadované prostředky** , abyste se ujistili, že budete požadovat jenom prostředky, které jste nakonfigurovali.

## <a name="next-steps"></a>Další kroky
- [Série rychlý Start při správě aplikací](add-application-portal-assign-users.md)
- [Jak ladit jednotné přihlašování založené na SAML pro aplikace v Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
- [Požadavky protokolu SAML jednotného přihlašování Azure AD](../develop/active-directory-single-sign-on-protocol-reference.md)
