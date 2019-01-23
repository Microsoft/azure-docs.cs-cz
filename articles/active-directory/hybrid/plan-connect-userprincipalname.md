---
title: Naplnění Azure AD UserPrincipalName
description: Následující dokument popisuje, jak je vyplněný atribut UserPrincipalName.
author: billmath
ms.component: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: daveba
ms.openlocfilehash: da2ae0262ef8380f31f37bfbbe5ddca45c72ebd1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468093"
---
# <a name="azure-ad-userprincipalname-population"></a>Naplnění Azure AD UserPrincipalName

Tento článek popisuje, jak je vyplněný atribut UserPrincipalName v Azure Active Directory (Azure AD).
Hodnota atributu UserPrincipalName je uživatelské jméno Azure AD pro uživatelské účty.

## <a name="upn-terminology"></a>Terminologie hlavní název uživatele
V tomto článku se používá následující terminologií:

|Označení|Popis|
|-----|-----|
|Počáteční doména|Výchozí doména (onmicrosoft.com) v Tenantovi Azure AD. Například contoso.onmicrosoft.com.|
|Microsoft e-mailu Online směrování adres (MOERA)|Azure AD vypočítá MOERA z atributu Azure AD MailNickName a Azure AD jako počáteční doména &lt;MailNickName&gt;&#64;&lt;počáteční doména&gt;.|
|Atributu mailNickName v místním|Atribut v Active Directory, jehož hodnota představuje alias uživatele v organizaci Exchange.|
|Atribut mail v místním|Atribut v Active Directory, jehož hodnota představuje e-mailovou adresu uživatele|
|Primární adresa SMTP|Primární e-mailovou adresu příjemce objektu Exchange. Například SMTP:user\@contoso.com.|
|Alternativním přihlašovacím ID|Místní atributu než UserPrincipalName, například atribut e-mailu, použitý k přihlášení.|

## <a name="what-is-userprincipalname"></a>Co je UserPrincipalName?
Atribut UserPrincipalName je atribut, který je na Internetu přihlašovací jméno pro uživatele, založené na standardu Internet [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Formát hlavního názvu uživatele
Hlavní název uživatele se skládá z předpony hlavní název uživatele (uživatelské jméno účtu) a přípona UPN (název domény DNS). Předpona, která je spojen s použitím příponu "\@" symbol. Například "někdo\@example.com". Hlavní název uživatele musí být jedinečný mezi všechny instanční objekty zabezpečení v rámci doménové struktury adresáře. 

## <a name="upn-in-azure-ad"></a>Hlavní název uživatele ve službě Azure AD 
Hlavní název uživatele se používá Azure AD umožňuje uživatelům přihlášení.  Hlavní název uživatele, který může uživatel používat, závisí na Určuje, jestli se doména neověří.  Pokud doména se ověřila, pak uživatel s touto příponou se bude moct přihlásit ke službě Azure AD.  

Atribut se synchronizuje pomocí služby Azure AD Connect.  Během instalace můžete zobrazit doménách, které byly ověřeny a, které ještě nebyly předpon.

   ![Neověřené domény](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativním přihlašovacím ID
V některých prostředích koncoví uživatelé mohou pouze znát e-mailová adresa a není jejich hlavní název uživatele.  Použití e-mailová adresa může být způsobeno podnikové zásady nebo závislost v místním – obchodní aplikace.

Alternativní přihlašovací ID umožňuje konfigurovat přihlašovací prostředí kde uživatelé můžou přihlásit s atributem než jejich hlavní název uživatele, jako jsou e-mailu.

Pokud chcete povolit alternativního přihlašovacího ID s Azure AD, jsou potřeba žádné další konfigurace, které kroky při použití služby Azure AD Connect. Alternativní ID se dá přímo z průvodce. Podívejte se konfigurace přihlašování Azure AD pro uživatele v části synchronizace. V části **hlavní název uživatele** rozevíracího seznamu, vyberte atribut alternativního přihlašovacího ID.

![Neověřené domény](./media/plan-connect-userprincipalname/altloginid.png)  

Další informace najdete v tématu [konfigurace alternativního přihlašovacího ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) a [konfigurace přihlášení k Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Přípona UPN neověřených
Pokud v místním UserPrincipalName atributu/alternativní přihlašovací ID přípona není ověřená s Tenantem Azure AD, hodnota atributu Azure AD UserPrincipalName nastavená na MOERA. Azure AD vypočítá MOERA z atributu Azure AD MailNickName a Azure AD jako počáteční doména &lt;MailNickName&gt;&#64;&lt;počáteční doména&gt;.

## <a name="verified-upn-suffix"></a>Ověřené přípona UPN
Pokud atribut UserPrincipalName v místním nebo alternativní přihlašovací ID příponu pak bude hodnota atributu Azure AD UserPrincipalName stejná jako hodnota místní atribut UserPrincipalName atributu/alternativní přihlašovací ID s Tenantem Azure AD, ověření.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Při výpočtu hodnoty atributu Azure AD MailNickName
Hodnota atributu Azure AD UserPrincipalName může být nastaven na MOERA, proto je důležité pochopit, jak se počítá hodnota atributu Azure AD MailNickName, což je předpona MOERA.

Při synchronizaci objektu uživatele Tenanta služby Azure AD pro první služby Azure AD ověří následující položky v uvedeném pořadí a nastaví hodnota atributu MailNickName první existující:

- Atributu mailNickName v místním
- Předpona primární adresa SMTP
- Předpona atributu e-mailu v místním
- Předpona místní atribut userPrincipalName atributu/alternativní přihlašovací ID
- Předpona sekundární adresa smtp

Při synchronizaci aktualizací na objekt uživatele do Tenanta Azure AD, Azure AD aktualizuje hodnota atributu MailNickName pouze v případě, že aktualizace, aby hodnota atributu mailNickName v místním.

>[!IMPORTANT]
>Azure AD přepočítá hodnoty atributu UserPrincipalName pouze v případě, že aktualizace na místní UserPrincipalName atributu/alternativní přihlašovací ID hodnotu synchronizovaný do Tenanta služby Azure AD. 
>
>Pokaždé, když se Azure AD přepočítá atribut UserPrincipalName, také přepočítá MOERA. 

## <a name="upn-scenarios"></a>Scénáře hlavní název uživatele
Následují příklady o tom, jak hlavní název uživatele se počítají na základě této situaci.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scénář 1: Ověřit bez přípona UPN – počáteční synchronizaci.

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Místní objekt uživatele:
- mailNickName: &lt;Nenastaveno&gt;
- proxyAddresses        : {SMTP:us1@contoso.com}
- e-mailu: us2@contoso.com
- userPrincipalName : us3@contoso.com`

Objekt uživatele do Tenanta služby Azure AD synchronizovat poprvé
- Nastavte atribut Azure AD MailNickName předpona primární adresy SMTP.
- Nastavte MOERA na &lt;MailNickName&gt;&#64;&lt;počáteční doména&gt;.
- Nastavte atribut Azure AD UserPrincipalName MOERA.

Objekt uživatele Azure AD Tenanta:
- MailNickName      : us1           
- UserPrincipalName : us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scénář 2: Přípona UPN ověřit bez – sada místní atributu mailNickName

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Místní objekt uživatele:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- e-mailu: us2@contoso.com
- userPrincipalName : us3@contoso.com

Synchronizovat aktualizace u atributu mailNickName v místním do Tenanta služby Azure AD
- Pomocí atributu mailNickName v místním aktualizace atributu Azure AD MailNickName.
- Protože neexistuje žádná aktualizace místní atribut userPrincipalName, není žádná změna do atributu Azure AD UserPrincipalName.

Objekt uživatele Azure AD Tenanta:
- mailNickName: us4
- UserPrincipalName : us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scénář 3: Přípona UPN ověřit bez – aktualizovat místní atribut userPrincipalName

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Místní objekt uživatele:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us1@contoso.com}
- e-mailu: us2@contoso.com
- userPrincipalName : us5@contoso.com

Synchronizovat aktualizace v místní atribut userPrincipalName do Tenanta služby Azure AD
- Aktualizace na místní atribut userPrincipalName aktivuje přepočet atributu MOERA a Azure AD UserPrincipalName.
- Nastavte MOERA na &lt;MailNickName&gt;&#64;&lt;počáteční doména&gt;.
- Nastavte atribut Azure AD UserPrincipalName MOERA.

Objekt uživatele Azure AD Tenanta:
- mailNickName: us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scénář 4: Ověřit bez přípona UPN – aktualizace primární adresu SMTP a místní atribut e-mailů.

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Místní objekt uživatele:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- e-mailu: us7@contoso.com
- userPrincipalName : us5@contoso.com

Synchronizovat aktualizace v místní atribut e-mailu a primární adresu SMTP do Tenanta služby Azure AD
- Po dokončení počáteční synchronizace objektu uživatele aktualizace na místní atribut e-mailů a primární adresu SMTP nebude mít vliv na Azure AD MailNickName nebo atribut UserPrincipalName.

Objekt uživatele Azure AD Tenanta:
- mailNickName: us4
- UserPrincipalName : us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scénář 5: Přípona UPN ověřené – aktualizovat místní přípona atribut userPrincipalName

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Místní objekt uživatele:
- mailNickName      : us4
- proxyAddresses        : {SMTP:us6@contoso.com}
- e-mailu: us7@contoso.com
- userPrincipalName : us5@verified.contoso.com

Synchronizovat aktualizace v místní atribut userPrincipalName do Tenanta Azure AD
- Aktualizace místních userPrincipalName atribut triggery přepočet atributu Azure AD UserPrincipalName.
- Nastavení atributu Azure AD UserPrincipalName pro místní atribut userPrincipalName jako přípona UPN je ověřený s Tenantem Azure AD.

Objekt uživatele Azure AD Tenanta:
- mailNickName: us4     
- UserPrincipalName : us5@verified.contoso.com

## <a name="next-steps"></a>Další kroky
- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
