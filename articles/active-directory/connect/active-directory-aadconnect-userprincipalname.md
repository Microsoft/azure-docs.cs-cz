---
title: Azure AD UserPrincipalName naplnění
description: Následující dokument popisuje, jak se naplní atribut UserPrincipalName.
author: billmath
ms.component: hybrid
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 73238b1f79e639f832499eed15ac1e4499eb6e84
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593385"
---
# <a name="azure-ad-userprincipalname-population"></a>Azure AD UserPrincipalName naplnění

Tento článek popisuje, jak se naplní atribut UserPrincipalName v Azure Active Directory (Azure AD).
Hodnota atributu UserPrincipalName je uživatelské jméno Azure AD pro uživatelské účty.

## <a name="upn-terminology"></a>Terminologie UPN
V tomto článku se používá následující terminologií:

|Označení|Popis|
|-----|-----|
|Počáteční domény|Výchozí doména (onmicrosoft.com) v klientovi Azure AD. Například contoso.onmicrosoft.com.|
|Microsoft Online e-mailů směrování adres (MOERA)|Azure AD vypočítá MOERA z atributu Azure AD MailNickName a Azure AD počáteční domény jako &lt;MailNickName&gt;&#64;&lt;počáteční domény&gt;.|
|Atribut místní mailNickName|Atribut ve službě Active Directory, jehož hodnota představuje alias uživatele v organizaci Exchange.|
|Atribut mail na místě|Představuje atribut ve službě Active Directory, jehož hodnota e-mailovou adresu uživatele|
|Primární adresa SMTP|Primární e-mailová adresa příjemce objekt Exchange. Například, SMTP:user@contoso.com.|
|Alternativním přihlašovacím ID|Místní atributu než UserPrincipalName, například atribut mail, použít pro přihlášení.|

## <a name="what-is-userprincipalname"></a>Co je UserPrincipalName?
UserPrincipalName je atribut, který je Internetu stylu přihlašovací jméno pro uživatele podle standardu Internet [RFC 822](http://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Formát UPN
UPN se skládá z předponu hlavní název uživatele (uživatelské jméno účtu) a příponu UPN (název domény DNS). Předpona, která je spojen s příponou pomocí "@" symbol. Například someone@example.com. Názvu UPN, musí být jedinečný mezi všechny hlavní objekty zabezpečení v doménové struktuře adresáře. 

## <a name="upn-in-azure-ad"></a>Hlavní název uživatele v Azure AD 
Hlavní název uživatele se používá Azure AD umožnit uživatelům přihlášení.  Názvu UPN, které může uživatel používat, závisí na tom, zda doména byla ověřena.  Pokud byla ověřena domény, uživatel s touto příponou budou mít povolený pro přihlášení ke službě Azure AD.  

Atribut se synchronizují přes Azure AD Connect.  Během instalace můžete zobrazit domén, které byly ověřeny a ty, které ještě nebyly.

   ![Neověřené domény](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativním přihlašovacím ID
V některých prostředích s kvůli podnikovými zásadami nebo místní závislosti-obchodní aplikace koncoví uživatelé mohou pouze znát e-mailové adresy a není jejich UPN.

Alternativního přihlašovacího ID umožňuje nakonfigurovat přihlašování kde mohou uživatelé přihlásit pomocí atributu než jejich UPN, jako je například e-mailu.

Povolit alternativního přihlašovacího ID s Azure AD, je potřeba žádné další konfigurace kroky při použití Azure AD Connect. Alternativní ID se dá přímo z Průvodce nakonfigurovat. V tématu Azure AD přihlášení konfigurace pro uživatele v části synchronizace. V části **hlavní název uživatele** rozevíracího seznamu, vyberte atribut alternativního přihlašovacího ID uživatele cloudu.

![Neověřené domény](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

Další informace najdete v části [konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) a [konfigurace přihlášení k Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Přípona UPN – ověření
Pokud přípona místní UserPrincipalName atribut nebo alternativní přihlašovací ID není ověřen pomocí klienta Azure AD, Azure AD UserPrincipalName hodnotu atributu nastavená na MOERA. Azure AD vypočítá MOERA z Azure AD MailNickName atribut a počáteční doménu služby Azure AD jako &lt;MailNickName&gt;&#64;&lt;počáteční domény&gt;.

## <a name="verified-upn-suffix"></a>Ověřené příponu UPN
Pokud atribut UserPrincipalName místní nebo alternativní je ověřen příponu ID přihlášení pomocí klienta Azure AD, bude hodnota atributu Azure AD UserPrincipalName stejná jako hodnota místní UserPrincipalName atribut nebo alternativní přihlašovací ID.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Při výpočtu hodnoty atributů Azure AD MailNickName
Vzhledem k tomu, že hodnota atributu Azure AD UserPrincipalName může být nastaven na MOERA, je důležité pochopit, jak se počítá hodnota atributu Azure AD MailNickName, která je předpona MOERA.

Při objekt uživatele je první synchronizaci klient služby Azure AD, Azure AD ověří následující v uvedeném pořadí a nastaví hodnotu atributu MailNickName první existující:

- Atribut místní mailNickName
- Předpona primární adresa SMTP
- Předpona atributu mail na místě
- Předpona místní userPrincipalName atribut nebo alternativním přihlašovacím ID
- Předpona sekundární adresa smtp

Při synchronizaci aktualizací na objekt uživatele se klient Azure AD, Azure AD aktualizací hodnota atributu MailNickName pouze v případě, že dojde k aktualizaci na hodnotu atributu mailNickName místně.

>[!IMPORTANT]
>Azure AD přepočítá hodnoty atributu UserPrincipalName pouze v případě, že aktualizace na místní UserPrincipalName atribut nebo alternativní přihlašovací ID hodnotu se synchronizuje s klienta Azure AD. 
>
>Vždy, když Azure AD přepočítá atribut UserPrincipalName, také přepočítá MOERA. 

## <a name="upn-scenarios"></a>Scénáře hlavní název uživatele
Následuje příklad scénáře jak UPN je vypočtena na základě v této situaci.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scénář 1: Přípona UPN neověřený – počáteční synchronizaci.

Místní objekt uživatele:
- mailNickName: &lt;není nastaven.&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mailu: us2@contoso.com
- userPrincipalName: us3@contoso.com.

Synchronizovat první objekt uživatele, který má klienta Azure AD
- Nastavte atribut Azure AD MailNickName na primární předpona adresy SMTP.
- Nastavte MOERA na &lt;MailNickName&gt;&#64;&lt;počáteční domény&gt;.
- Nastavte atribut Azure AD UserPrincipalName na MOERA.

Objekt uživatele Azure AD klienta:
- MailNickName: us1           
- UserPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scénář 2: Příponu UPN neověřený – sadu místní atribut mailNickName

Místní objekt uživatele:
- MailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mailu: us2@contoso.com
- userPrincipalName: us3@contoso.com

Synchronizovat aktualizace na místní mailNickName atribut klienta Azure AD
- Aktualizujte atribut Azure AD MailNickName s místními mailNickName atributem.
- Vzhledem k tomu, že není žádná aktualizace do atribut userPrincipalName v místě, se nezměnila do atribut Azure AD UserPrincipalName.

Objekt uživatele Azure AD klienta:
- MailNickName: us4
- UserPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scénář 3: Příponu UPN neověřený – aktualizace místní atribut userPrincipalName

Místní objekt uživatele:
- MailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mailu: us2@contoso.com
- userPrincipalName: us5@contoso.com

Synchronizovat aktualizace na atribut userPrincipalName místní klienta Azure AD
- Aktualizace na atribut userPrincipalName v místě, spustí MOERA a Azure AD UserPrincipalName atributu.
- Nastavte MOERA na &lt;MailNickName&gt;&#64;&lt;počáteční domény&gt;.
- Nastavte atribut Azure AD UserPrincipalName na MOERA.

Objekt uživatele Azure AD klienta:
- MailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scénář 4: Příponu UPN neověřený – primární adresa SMTP aktualizace a místní poštovní atribut

Místní objekt uživatele:
- MailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-mailu: us7@contoso.com
- userPrincipalName: us5@contoso.com

Synchronizovat aktualizace na atribut mail v místě a primární adresa SMTP klienta Azure AD
- Po počáteční synchronizaci objektu uživatele, aktualizuje místní poštovní atribut a neovlivní primární adresa SMTP atributu Azure AD MailNickName ani UserPrincipalName.

Objekt uživatele Azure AD klienta:
- MailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scénář 5: Příponu UPN ověřené – aktualizace místní příponu atribut userPrincipalName

Místní objekt uživatele:
- MailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-mailu: us7@contoso.com
- serPrincipalName: us5@verified.contoso.com

Synchronizovat aktualizace na atribut userPrincipalName místně klientovi Azure AD
- Aktualizace místní userPrincipalName atribut aktivační události přepočítání atributu Azure AD UserPrincipalName.
- Nastavte atribut Azure AD UserPrincipalName do atribut userPrincipalName místní jako přípona UPN je ověření u klienta Azure AD.

Objekt uživatele Azure AD klienta:
- MailNickName: us4     
- UserPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>Další kroky
- [Integrace místních adresářů do služby Azure Active Directory](active-directory-aadconnect.md)
- [Vlastní instalace služby Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
