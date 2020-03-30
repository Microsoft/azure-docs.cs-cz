---
title: Problémy s přihlášením k federované aplikaci galerie jednotného přihlášení | Dokumenty společnosti Microsoft
description: Pokyny pro konkrétní chyby při přihlašování k aplikaci, kterou jste nakonfigurovali pro federované jednotné přihlašování založené na SAML pomocí Azure AD
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
ms.openlocfilehash: 874d273e26a728afc0a1dc1a16852016797067ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367896"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problémy s přihlášením k aplikaci galerie nakonfigurované pro federované jednotné přihlašování

Chcete-li vyřešit problémy s přihlášením níže, doporučujeme postupovat podle těchto návrhů, abyste získali lepší diagnózu a automatizovali kroky řešení:

- Nainstalujte [rozšíření Zabezpečený prohlížeč Moje aplikace,](access-panel-extension-problem-installing.md) které vám pomůže azure active directory (Azure AD) poskytnout lepší diagnostiku a řešení při použití testování prostředí na webu Azure Portal.
- Reprodukovat chybu pomocí testování prostředí na stránce konfigurace aplikace na webu Azure Portal. Další informace o [aplikacích jednotného přihlašování založených na ladění SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Aplikace nebyla v adresáři nalezena.

*Chyba AADSTS70001: Aplikace s\/identifikátorem https: /contoso.com' nebyla v adresáři nalezena*.

**Možná příčina**

Atribut `Issuer` odeslaný z aplikace do služby Azure AD v požadavku SAML neodpovídá hodnotě identifikátoru, která je nakonfigurovaná pro aplikaci ve službě Azure AD.

**Rozlišení**

Ujistěte `Issuer` se, že atribut v požadavku SAML odpovídá hodnotě identifikátoru nakonfigurované ve službě Azure AD. Pokud používáte [testování prostředí](../azuread-dev/howto-v1-debug-saml-sso-issues.md) na webu Azure portal s my apps zabezpečené rozšíření prohlížeče, nemusíte ručně postupujte podle těchto kroků.

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** výběrem Všechny **služby** v horní části hlavní nabídky navigace vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

1.  V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace.**

1.  Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte, zda hodnota v textovém poli Identifikátor odpovídá hodnotě hodnoty identifikátoru zobrazené v chybě.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adresa odpovědi neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci.

*Chyba AADSTS50011: Adresa odpovědi\/https: /contoso.com neodpovídá adresám odpovědí nakonfigurovaným pro aplikaci*

**Možná příčina**

Hodnota `AssertionConsumerServiceURL` v požadavku SAML neodpovídá hodnotě adresy URL odpovědi nebo vzoru nakonfigurovanému ve službě Azure AD. Hodnota `AssertionConsumerServiceURL` v požadavku SAML je adresa URL, která se zobrazí v chybě.

**Rozlišení**

Ujistěte `AssertionConsumerServiceURL` se, že hodnota v požadavku SAML odpovídá hodnotě adresy URL odpovědi nakonfigurované ve službě Azure AD. Pokud používáte [testování prostředí](../azuread-dev/howto-v1-debug-saml-sso-issues.md) na webu Azure portal s my apps zabezpečené rozšíření prohlížeče, nemusíte ručně postupujte podle těchto kroků.

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** výběrem Všechny **služby** v horní části hlavní nabídky navigace vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

1.  V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace.**

1.  Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte nebo aktualizujte hodnotu v `AssertionConsumerServiceURL` textovém poli Adresa URL odpovědi tak, aby odpovídala hodnotě v požadavku SAML.    
    
Po aktualizaci hodnoty url odpovědi ve službě Azure AD a odpovídá hodnotě odeslané aplikací v požadavku SAML, měli byste být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživateli není přiřazena role.

*Chyba AADSTS50105: Přihlášený uživatel\@'brian contoso.com' není přiřazen k roli pro aplikaci*.

**Možná příčina**

Uživateli se v Azure AD neudělil přístup k aplikaci.​

**Rozlišení**

Chcete-li k aplikaci přiřadit jednoho nebo více uživatelů přímo, postupujte podle následujících kroků. Pokud používáte [testování prostředí](../azuread-dev/howto-v1-debug-saml-sso-issues.md) na webu Azure portal s my apps zabezpečené rozšíření prohlížeče, nemusíte ručně postupujte podle těchto kroků.

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce**.

1.  Otevřete **rozšíření Azure Active Directory** výběrem Všechny **služby** v horní části hlavní nabídky navigace vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

1.  V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace.**

1.  Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

1.  Ze seznamu aplikací vyberte ten, ke kterému chcete přiřadit uživatele.

1.  Po načtení aplikace vyberte **možnost Uživatelé a skupiny** z levé navigační nabídky aplikace.

1.  Kliknutím na tlačítko **Přidat** v horní části seznamu **Uživatelé a skupiny** otevřete podokno **Přidat přiřazení.**

1.  V podokně **Přidat přiřazení** vyberte volič **i uživatelé a skupiny.**

1. Do vyhledávacího pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a příjmení nebo e-mailovou adresu uživatele, kterého chcete přidat.

1. Najeďte na **uživatele** v seznamu a zvedejte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotky nebo loga uživatele přidejte uživatele do seznamu **Vybrané.**

1. **Nepovinné:** Pokud chcete **přidat více uživatelů**, zadejte do vyhledávacího pole Hledat podle jména nebo **e-mailové adresy** jiné celé jméno nebo e-mailovou adresu a kliknutím na toto políčko přidejte uživatele do seznamu **Vybrané.**

1. Po dokončení výběru uživatelů je kliknutím na tlačítko **Vybrat** přidáte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

1. **Nepovinné:** Klepnutím na výběr **voliče rolí** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

1. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným uživatelům.

Po krátké době budou moci vybraní uživatelé spustit tyto aplikace pomocí metod popsaných v části popis řešení.

## <a name="not-a-valid-saml-request"></a>Není platný požadavek SAML

*Chyba AADSTS75005: Požadavek není platnou zprávou protokolu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Mezi běžné problémy patří:

-   Chybějící požadovaná pole v požadavku SAML
-   Kódovaná metoda požadavku SAML

**Rozlišení**

1. Zachyťte požadavek SAML. Postupujte podle kurzu [Jak ladit jednotné přihlašování na saml k aplikacím v Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) se dozvíte, jak zachytit požadavek SAML.

1. Obraťte se na dodavatele aplikace a podělte se s ním o následující informace:

   -   Požadavek SAML

   -   [Požadavky na protokol SAML jednotného přihlášení Azure AD](../develop/single-sign-on-saml-protocol.md)

Dodavatel aplikace by měl ověřit, že podporují implementaci Azure AD SAML pro jednotné přihlašování.

## <a name="misconfigured-application"></a>Chybně nakonfigurovaná aplikace

*Chyba AADSTS650056: Chybně nakonfigurovaná aplikace. To může být způsobeno jedním z následujících: Klient neuvedl žádná oprávnění v požadovaných oprávněních v registraci aplikace klienta. Nebo admin neschválila v tenantovi. Nebo zkontrolujte identifikátor aplikace v požadavku a ujistěte se, že odpovídá nakonfigurovaný identifikátor klientské aplikace. Obraťte se na správce a opravte konfiguraci nebo souhlas jménem klienta.*. .

**Možná příčina**

Atribut `Issuer` odeslaný z aplikace do služby Azure AD v požadavku SAML neodpovídá hodnotě identifikátoru nakonfigurované pro aplikaci ve službě Azure AD.

**Rozlišení**

Ujistěte `Issuer` se, že atribut v požadavku SAML odpovídá hodnotě identifikátoru nakonfigurované ve službě Azure AD. Pokud používáte [testovací prostředí](../azuread-dev/howto-v1-debug-saml-sso-issues.md) na webu Azure Portal s rozšířením Zabezpečený prohlížeč Moje aplikace, nemusíte ručně postupovat takto:

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce**.

1.  Otevřete **rozšíření Azure Active Directory** výběrem Všechny **služby** v horní části hlavní nabídky navigace vlevo.

1.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

1.  V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace.**

1.  Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

1.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace otevřete **základní konfiguraci SAML**. Ověřte, zda hodnota v textovém poli Identifikátor odpovídá hodnotě hodnoty identifikátoru zobrazené v chybě.


## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíč není nakonfigurován

*Chyba AADSTS50003: Není nakonfigurován žádný podpisový klíč.*

**Možná příčina**

Objekt aplikace je poškozený a Azure AD nerozpozná certifikát nakonfigurovaný pro aplikaci.

**Rozlišení**

Chcete-li odstranit a vytvořit nový certifikát, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce**.

1. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

1. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

1. V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace.**

1. Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

1. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

1. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

1. V části **Podpisový certifikát SAML** vyberte **Vytvořit nový certifikát.**

1. Vyberte Datum vypršení platnosti a klepněte na tlačítko **Uložit**.

1. Zaškrtněte **políčko Pověřit** aktivním certifikátem nový certifikát. Pak v horní části podokna klikněte na **Uložit** a přijměte aktivaci certifikátu výměny.

1. V části **Podpisový certifikát SAML** klikněte na **odebrat** a odeberte **nepoužitý** certifikát.

## <a name="saml-request-not-present-in-the-request"></a>Požadavek SAML není v požadavku přítomen.

*Chyba AADSTS750054: SAMLRequest nebo SAMLResponse musí být k dispozici jako parametry řetězce dotazu v požadavku HTTP pro vazbu přesměrování SAML.*

**Možná příčina**

Azure AD nebyl schopen identifikovat požadavek SAML v rámci parametrů adresy URL v požadavku HTTP. K tomu může dojít, pokud aplikace nepoužívá vazbu přesměrování HTTP při odesílání požadavku SAML do služby Azure AD.

**Rozlišení**

Aplikace musí odeslat požadavek SAML kódovaný do hlavičky umístění pomocí vazby přesměrování HTTP. Další informace o způsobu implementace najdete v části věnované vazbě HTTP Redirect v [dokumentu specifikace protokolu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD odesílá token do nesprávného koncového bodu

**Možná příčina**

Pokud během jednotného přihlašování požadavek na přihlášení neobsahuje adresu URL explicitní odpovědi (adresa URL služby Assertion Consumer Service), vybere služba Azure AD pro tuto aplikaci libovolnou z nakonfigurovaných adres URL spoléhání. I v případě, že má aplikace nakonfigurovanou adresu URL explicitní odpovědi, může být uživatel přesměrován https://127.0.0.1:444. 

Při přidávání aplikace jako aplikace mimo galerii služba Azure Active Directory vytvořila tuto adresu URL pro odpověď jako výchozí hodnotu. Toto chování se změnilo a Azure Active Directory už tuto adresu URL ve výchozím nastavení nepřidává. 

**Rozlišení**

Odstraňte nepoužívané adresy URL odpovědí nakonfigurované pro aplikaci.

1.  Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** výběrem Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory vyberte **Podnikové aplikace.**

5.  Výběrem **možnosti Všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace**.

6.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7.  Po načtení aplikace otevřete **základní konfiguraci SAML**. V **adrese URL pro odpověď (Adresa URL služby Assertion Consumer Service)** odstraňte nepoužívané nebo výchozí adresy URL odpovědí vytvořené systémem. Například, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobení deklarací SAML odeslaných do aplikace

Informace o tom, jak přizpůsobit deklarace atributů SAML odeslané do vaší aplikace, naleznete [v tématu Mapování deklarací ve službě Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Další kroky

[Jak ladit jednotné přihlašování na saml k aplikacím ve službě Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
