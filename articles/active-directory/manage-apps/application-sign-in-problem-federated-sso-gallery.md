---
title: Potíže při přihlašování k aplikaci galerii konfigurované pro federované jednotné přihlašování | Dokumentace Microsoftu
description: Pokyny pro konkrétní chyby při přihlašování do aplikace, kterou jste nakonfigurovali pro založené na SAML federované jednotné přihlašování s Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: celested
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 623d684f701df8b1a7c4b84a2bd3840f039ad174
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292210"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Potíže při přihlašování k aplikaci galerii konfigurované pro federované jednotné přihlašování

Řešení potíží přihlášení, doporučujeme, abyste že postupujte podle těchto návrhů pro získání lepší diagnostiku a automatizaci kroků pro řešení:

- Nainstalujte [Moje aplikace zabezpečené rozšíření prohlížeče](access-panel-extension-problem-installing.md) usnadní Azure Active Directory (Azure AD) kvůli lepší diagnostiku a řešení při použití testování prostředí na webu Azure Portal.
- Pomocí testovacích prostředí na stránce konfigurace aplikace na webu Azure Portal chybu reprodukujte. Další informace najdete na [ladění SAML aplikace založené na jednotné přihlašování](../develop/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři

*Chyba AADSTS70001: Aplikace s identifikátorem "https:\//contoso.com' nebyl nalezen v adresáři*.

**Možná příčina**

`Issuer` Atribut odeslaný z aplikace do služby Azure AD v požadavku SAML neodpovídá hodnotě identifikátor, který je nakonfigurovaný pro aplikaci ve službě Azure AD.

**Řešení**

Ujistěte se, `Issuer` atribut v požadavku SAML odpovídá hodnotu Identifier nakonfigurovanou ve službě Azure AD. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky.

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

1.  Otevřít **rozšíření Azure Active Directory** tak, že vyberete **všechny služby** v horní části hlavní navigační nabídce vlevo.

1.  Typ **"Azure Active Directory"** do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

1.  Vyberte **podnikové aplikace** levé navigační nabídce Azure Active Directory.

1.  Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

1.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace, otevřete **konfigurace základní SAML**. Ověřte, jestli hodnota v textovém poli Identifikátor odpovídá hodnotě pro hodnotu identifier zobrazují v chybě.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Tato adresa pro odpovědi neodpovídá adresám pro odpovědi nakonfigurovaným pro danou aplikaci.

*Chyba AADSTS50011: Adresa pro odpovědi ' https:\//contoso.com' neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci*

**Možná příčina**

`AssertionConsumerServiceURL` Hodnota v požadavku SAML se neshoduje adresa URL odpovědi hodnotou nebo vzorem nakonfigurované ve službě Azure AD. `AssertionConsumerServiceURL` Hodnota v požadavku SAML je adresa URL se zobrazí v chybě.

**Řešení**

Ujistěte se, `AssertionConsumerServiceURL` hodnota v požadavku SAML shoduje s hodnotou adresy URL odpovědi nakonfigurované ve službě Azure AD. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky.

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

1.  Otevřít **rozšíření Azure Active Directory** tak, že vyberete **všechny služby** v horní části hlavní navigační nabídce vlevo.

1.  Typ **"Azure Active Directory"** do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

1.  Vyberte **podnikové aplikace** levé navigační nabídce Azure Active Directory.

1.  Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

1.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace, otevřete **konfigurace základní SAML**. Ověřit nebo aktualizujte hodnotu v textovém poli Adresa URL odpovědi tak, aby odpovídaly `AssertionConsumerServiceURL` hodnota v požadavku SAML.    
    
Poté, co jste aktualizovali hodnotu adresy URL odpovědi ve službě Azure AD a odpovídá hodnotě, kterou aplikace odeslala, v požadavku SAML, by měl být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživatel není přiřazený k roli

*Chyba AADSTS50105: Přihlášený uživatel "brian\@contoso.com" není přiřazen k roli pro aplikaci*.

**Možná příčina**

Uživateli nebyl udělen přístup k aplikaci ve službě Azure AD.

**Řešení**

Jeden nebo více uživatelů přiřadit přímo k aplikaci, použijte následující postup. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky.

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce**.

1.  Otevřít **rozšíření Azure Active Directory** tak, že vyberete **všechny služby** v horní části hlavní navigační nabídce vlevo.

1.  Typ **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

1.  Vyberte **podnikové aplikace** levé navigační nabídce Azure Active Directory.

1.  Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

1.  V seznamu aplikací vyberte ten, který chcete přiřadit uživatele k.

1.  Po načtení aplikace, vyberte **uživatelů a skupin** levé navigační nabídce aplikace.

1.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

1.  Vyberte **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

1. V **hledat podle jména nebo e-mailové adresy** pole vyhledávání zadejte úplný název nebo e-mailová adresa uživatele, který chcete přidat.

1. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele, aby **vybrané** seznamu.

1. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**zadejte jiné jméno nebo e-mailovou adresu do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním uživatele **vybrané**  seznamu.

1. Jakmile budete hotovi, vyberte uživatele, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

1. **Volitelné:** Klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

1. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátkou dobu budou moci spouštět tyto aplikace pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="not-a-valid-saml-request"></a>Není platný požadavek SAML

*Chyba AADSTS75005: Žádost není platná zpráva protokolu typu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML, kterou aplikace odeslala, pro jednotné přihlašování. Mezi běžné problémy patří:

-   V požadavku SAML chybí povinná pole.
-   Kódovaná metoda požadavku SAML

**Řešení**

1. Zaznamenejte požadavek SAML. postupujte podle kurzu [ladění založené na SAML jednotného přihlašování k aplikacím ve službě Azure AD](../develop/howto-v1-debug-saml-sso-issues.md) informace o zaznamenání požadavku SAML.

1. Obraťte se na dodavatele aplikace a sdílet tyto údaje:

   -   Požadavek SAML

   -   [Protokol požadavky služby Azure AD Single Sign-on SAML](../develop/single-sign-on-saml-protocol.md)

Na dodavatele aplikace by měl ověřit, že podporují implementace Azure AD SAML pro jednotné přihlašování.

## <a name="misconfigured-application"></a>Nesprávně nakonfigurované aplikace

*Chyba AADSTS650056: Nesprávně nakonfigurované aplikace. Může to být způsobené jedním z následujících důvodů: Klienta není uvedená žádná oprávnění pro "AAD Graph" požadovaných oprávnění v registraci klienta aplikace. Nebo správce nevyjádřil v tenantovi. Nebo zkontrolujte identifikátor aplikace do požadavku Ujistěte se, že odpovídá identifikátor konfigurovaného klienta aplikace. Obraťte se prosím na svého správce a opravte konfiguraci nebo udělit souhlas jménem klienta.* .

**Možná příčina**

`Issuer` Atribut odeslaný z aplikace do služby Azure AD v požadavku SAML neodpovídá hodnotu Identifier nakonfigurovanou pro aplikaci ve službě Azure AD.

**Řešení**

Ujistěte se, `Issuer` atribut v požadavku SAML odpovídá hodnotu Identifier nakonfigurovanou ve službě Azure AD. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

1.  Otevřít **rozšíření Azure Active Directory** tak, že vyberete **všechny služby** v horní části hlavní navigační nabídce vlevo.

1.  Typ **"Azure Active Directory"** do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

1.  Vyberte **podnikové aplikace** levé navigační nabídce Azure Active Directory.

1.  Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

1.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

1.  Po načtení aplikace, otevřete **konfigurace základní SAML**. Ověřte, jestli hodnota v textovém poli Identifikátor odpovídá hodnotě pro hodnotu identifier zobrazují v chybě.


## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíč není nakonfigurováno

*Chyba AADSTS50003: Nakonfigurovaný žádný podpisový klíč.*

**Možná příčina**

Poškozený objekt aplikace a služby Azure AD nerozpoznal certifikátu nakonfigurovanému pro aplikaci.

**Řešení**

Pokud chcete odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

1. Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

1. Typ **"Azure Active Directory"** do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

1. Vyberte **podnikové aplikace** levé navigační nabídce Azure Active Directory.

1. Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

1. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování

1. Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

1. Vyberte **vytvořit nový certifikát** pod **podpisového certifikátu SAML** oddílu.

1. Vyberte datum vypršení platnosti a pak klikněte na tlačítko **Uložit**.

1. Zkontrolujte **nastavit nový certifikát jako aktivní** přepsat aktivní certifikát. Potom klikněte na **Uložit** v horní části podokna a přijměte aktivaci certifikátu výměny.

1. V části **podpisový certifikát SAML** klikněte na tlačítko **odebrat** odebrat **nepoužitý** certifikátu.

## <a name="saml-request-not-present-in-the-request"></a>Požadavek SAML, není k dispozici v požadavku

*Chyba AADSTS750054: SAMLRequest nebo SAMLResponse musí být k dispozici jako parametry řetězce v požadavku HTTP pro SAML přesměrování vazby dotazu.*

**Možná příčina**

Azure AD nebyl schopen identifikovat si požadavek SAML v rámci parametrů adresy URL v požadavku HTTP. To může nastat, pokud se aplikace nepoužívá přesměrování protokolu HTTP vazby při odesílání požadavku SAML do služby Azure AD.

**Řešení**

Aplikace potřebuje k odeslání požadavku SAML překóduje se na hlavičku location, pomocí protokolu HTTP přesměrovat vazby. Další informace o tom, jak implementovat, najdete v části přesměrování vazby protokolu HTTP v [dokument specifikace protokolu SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD odesílá do nesprávné koncový bod tokenu

**Možná příčina**

Během jednotného přihlašování Pokud žádost o přihlášení neobsahuje adresu URL odpovědi explicitní (adresa URL služby příjemce kontrolního výrazu) a Azure AD bude vyberte některou z nakonfigurované spolehněte adresy URL pro tuto aplikaci. I v případě, že má explicitní odpovědi není nakonfigurována adresa URL aplikace, může být uživatel přesměrován https://127.0.0.1:444. 

Při přidávání aplikace jako aplikace mimo galerii služba Azure Active Directory vytvořila tuto adresu URL pro odpověď jako výchozí hodnotu. Toto chování se změnilo a Azure Active Directory už tuto adresu URL ve výchozím nastavení nepřidává. 

**Řešení**

Odstraňte nepoužívané odpovědních adres URL nakonfigurované pro aplikaci.

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

2.  Otevřít **rozšíření Azure Active Directory** tak, že vyberete **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Typ **"Azure Active Directory"** do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Vyberte **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Vyberte **všechny aplikace** zobrazíte seznam všech aplikací.

    Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace**.

6.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7.  Po načtení aplikace, otevřete **konfigurace základní SAML**. V **adresy URL odpovědi (adresa URL služby příjemce kontrolního výrazu)**, odstraňte nepoužívané nebo výchozí adresy URL odpovědí vytvořených systémem. Například, `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobování deklarací identity SAML, odesílá se do aplikace

Informace o přizpůsobení deklarací identity atributu SAML odeslán do vaší aplikace najdete v tématu [mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Další postup

[Ladění založené na SAML jednotného přihlašování k aplikacím ve službě Azure AD](../develop/howto-v1-debug-saml-sso-issues.md)
