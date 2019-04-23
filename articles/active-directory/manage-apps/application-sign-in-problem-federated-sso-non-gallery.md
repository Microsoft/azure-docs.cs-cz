---
title: Potíže při přihlašování k aplikaci mimo Galerii konfigurované pro federované jednotné přihlašování | Dokumentace Microsoftu
description: Pokyny pro specifické problémy mohou nastat při přihlašování k aplikaci konfigurované pro založené na SAML federované jednotné přihlašování s Azure AD
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
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cb0139227012be610977df3240f74a03f53be7e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440194"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Potíže při přihlašování k aplikaci mimo Galerii konfigurované pro federované jednotné přihlašování

Řešení potíží přihlášení, doporučujeme, abyste že postupujte podle těchto návrhů pro získání lepší diagnostiku a automatizaci kroků pro řešení:

- Nainstalujte [Moje aplikace zabezpečené rozšíření prohlížeče](access-panel-extension-problem-installing.md) usnadní Azure Active Directory (Azure AD) kvůli lepší diagnostiku a řešení při použití testování prostředí na webu Azure Portal.
- Pomocí testovacích prostředí na stránce konfigurace aplikace na webu Azure Portal chybu reprodukujte. Další informace najdete na [ladění SAML aplikace založené na jednotné přihlašování](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři

*Chyba AADSTS70001: Aplikace s identifikátorem `https://contoso.com` nebyl nalezen v adresáři*.

**Možná příčina**

Atribut odesílá z aplikace do služby Azure AD v požadavku SAML vystavitele neodpovídá hodnotu Identifier nakonfigurovanou v aplikaci Azure AD.

**Řešení**

Ujistěte se, `Issuer` atribut v požadavku SAML odpovídá hodnotu Identifier nakonfigurovanou ve službě Azure AD. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky.

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2. Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8. Po načtení aplikace, otevřete **konfigurace základní SAML**. Ověřte, jestli hodnota v textovém poli Identifikátor odpovídá hodnotě pro hodnotu identifier zobrazují v chybě.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adresa pro odpovědi neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci. 

*Chyba AADSTS50011: Adresa pro odpovědi `https://contoso.com` neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci* 

**Možná příčina** 

Hodnota AssertionConsumerServiceURL v požadavku SAML neodpovídá hodnotu adresy URL odpovědi nebo vzor nakonfigurované ve službě Azure AD. Hodnota AssertionConsumerServiceURL v požadavku SAML je adresa URL se zobrazí v chybě. 

**Řešení** 

Ujistěte se, `Issuer` atribut v požadavku SAML odpovídá hodnotu Identifier nakonfigurovanou ve službě Azure AD. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky.
 
1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.** 

2. Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo. 

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky. 

4. Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory. 

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací. 

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**
  
6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování

7. Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8. Po načtení aplikace, otevřete **konfigurace základní SAML**. Ověřit nebo aktualizujte hodnotu v textovém poli Adresa URL odpovědi tak, aby odpovídaly `AssertionConsumerServiceURL` hodnota v požadavku SAML.    
    
Poté, co jste aktualizovali hodnotu adresy URL odpovědi ve službě Azure AD a odpovídá hodnotě, kterou aplikace odeslala, v požadavku SAML, by měl být schopni se přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Uživatel není přiřazený k roli

*Chyba AADSTS50105: Přihlášený uživatel `brian\@contoso.com` není přiřazen k roli pro aplikaci*

**Možná příčina**

Uživateli nebyl udělen přístup k aplikaci ve službě Azure AD.

**Řešení**

Jeden nebo více uživatelů přiřadit přímo k aplikaci, použijte následující postup. Pokud používáte [testování prostředí](../develop/howto-v1-debug-saml-sso-issues.md) webu Azure Portal a pomocí rozšíření prohlížeče zabezpečení Moje aplikace, není nutné ručně provést následující kroky.

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7. Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8. Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9. Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="not-a-valid-saml-request"></a>Není platný SAML požadavek

*Chyba AADSTS75005: Žádost není platná zpráva protokolu typu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Mezi běžné problémy patří:

-   V požadavku SAML chybí povinná pole.

-   Kódovaná metoda požadavku SAML

**Řešení**

1.  Zaznamenejte požadavek SAML. postupujte podle kurzu [ladění založené na SAML jednotného přihlašování k aplikacím ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) informace o zaznamenání požadavku SAML.

2.  Obraťte se na dodavatele aplikace a sdílené složky:

    -   Požadavek SAML

    -   [Protokol požadavky služby Azure AD Single Sign-on SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

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

Chyba AADSTS50003: Nakonfigurovaný žádný podpisový klíč.

**Možná příčina**

Poškozený objekt aplikace a služby Azure AD nerozpoznal certifikátu nakonfigurovanému pro aplikaci.

**Řešení**

Pokud chcete odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2. Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8. Klikněte na tlačítko **vytvořit nový certifikát** pod **podpisového certifikátu SAML** oddílu.

9. Vyberte datum vypršení platnosti. Potom klikněte na **uložit.**

10. Zkontrolujte **nastavit nový certifikát jako aktivní** přepsat aktivní certifikát. Potom klikněte na **Uložit** v horní části podokna a přijměte aktivaci certifikátu výměny.

11. V části **podpisový certifikát SAML** klikněte na tlačítko **odebrat** odebrat **nepoužitý** certifikátu.

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

Informace o přizpůsobení deklarací identity atributu SAML odeslán do vaší aplikace najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Protokol požadavky služby Azure AD Single Sign-on SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
