---
title: Naplnění Azure AD UserPrincipalName
description: Následující dokument popisuje, jak je vyplněn atribut UserPrincipalName.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c748df10e432e3bebbce0dc8cb39dd2101d52e2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680040"
---
# <a name="azure-ad-userprincipalname-population"></a>Naplnění Azure AD UserPrincipalName

Tento článek popisuje, jak je atribut UserPrincipalName naplněn ve službě Azure Active Directory (Azure AD).
Hodnota atributu UserPrincipalName je uživatelské jméno Azure AD pro uživatelské účty.

## <a name="upn-terminology"></a>Terminologie UPN
V tomto článku se používá následující terminologie:

|Označení|Popis|
|-----|-----|
|Počáteční doména|Výchozí doména (onmicrosoft.com) v tenantovi Azure AD. Například contoso.onmicrosoft.com.|
|E-mailová směrovací adresa společnosti Microsoft online (MOERA)|Azure AD vypočítá MOERA z atributu Azure AD MailNickName &lt;a&gt; počáteční &lt;domény&gt;Azure AD jako MailNickName&#64;počáteční doméně .|
|Místní atribut mailNickName|Atribut ve službě Active Directory, jehož hodnota představuje alias uživatele v organizaci serveru Exchange.|
|Atribut místní pošty|Atribut ve službě Active Directory, jehož hodnota představuje e-mailovou adresu uživatele|
|Primární adresa SMTP|Primární e-mailová adresa objektu příjemce serveru Exchange. Například SMTP:user\@contoso.com.|
|Alternativní přihlašovací ID|Místní atribut jiný než UserPrincipalName, například atribut mail, který se používá pro přihlášení.|

## <a name="what-is-userprincipalname"></a>Co je UserPrincipalName?
UserPrincipalName je atribut, který je přihlašovací jméno ve stylu Internetu pro uživatele založené na standardu [Sítě Internet RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Formát UPN
Hlavní název uživatele se skládá z předpony hlavního názvu uživatele (název uživatelského účtu) a přípony hlavního názvu uživatele (název domény DNS). Předpona je spojena s příponou pomocí symbolu "\@" . Například "někdo\@example.com". Hlavní název domény musí být jedinečný mezi všemi objekty zabezpečení v doménové struktuře adresáře. 

## <a name="upn-in-azure-ad"></a>Upn ve službě Azure AD 
Hlavní hlásič uživatele používá Azure AD k povolení uživatelům přihlásit.  Hlavní název uživatele, který může uživatel použít, závisí na tom, zda byla doména ověřena.  Pokud byla doména ověřena, uživatel s tou příponou se bude moci přihlásit do Azure AD.  

Atribut je synchronizován pomocí Azure AD Connect.  Během instalace můžete zobrazit domény, které byly ověřeny, a domény, které nebyly.

   ![Neověřené domény](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativní přihlašovací ID
V některých prostředích mohou koncoví uživatelé znát pouze svou e-mailovou adresu a nikoli svůj upn.  Použití e-mailové adresy může být způsobeno podnikovými zásadami nebo závislostí místních podnikových aplikací.

Alternativní přihlašovací ID umožňuje nakonfigurovat přihlašovací prostředí, kde se uživatelé mohou přihlásit pomocí jiného atributu než jejich upn, jako je například pošta.

Chcete-li povolit alternativní přihlašovací ID s Azure AD, žádné další kroky konfigurace jsou potřeba při použití Azure AD Connect. Alternativní ID lze konfigurovat přímo z průvodce. Viz Konfigurace přihlášení Azure AD pro uživatele v části Synchronizace. V rozevíracím seznamu **Hlavní jméno uživatele** vyberte atribut alternativního přihlašovacího ID.

![Neověřené domény](./media/plan-connect-userprincipalname/altloginid.png)  

Další informace najdete [v tématu Konfigurace alternativnípřihlašovací id](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) a [konfigurace přihlášení Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Neověřená přípona UPN
Pokud místní UserPrincipalName atribut/alternativní přihlašovací ID přípona není ověřena s Tenantazure AD, pak hodnota atributu Azure AD UserPrincipalName atribut je nastavena na MOERA. Azure AD vypočítá MOERA z atributu Azure AD MailNickName &lt;a počáteční&gt; &lt;domény&gt;Azure AD jako MailNickName&#64;počáteční doméně .

## <a name="verified-upn-suffix"></a>Ověřená přípona UPN
Pokud je místní přípona atributu UserPrincipalName/alternativního přihlášení ověřena pomocí tenanta Azure AD, bude hodnota atributu Azure AD UserPrincipalName stejná jako místní hodnota atributu UserPrincipalName/alternativní přihlašovací ID.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Výpočet hodnoty atributu Azure AD MailNickName
Vzhledem k tomu, že hodnota atributu Azure AD UserPrincipalName může být nastavena na MOERA, je důležité pochopit, jak se vypočítá hodnota atributu Azure AD MailNickName, což je předpona MOERA.

Když je objekt uživatele poprvé synchronizován s klientem Azure AD, Azure AD zkontroluje následující položky v daném pořadí a nastaví hodnotu atributu MailNickName na první existující:

- Místní atribut mailNickName
- Předpona primární adresy SMTP
- Předpona atributu místní pošty
- Předpona místního atributu userPrincipalName/ID přihlášení
- Předpona sekundární smtp adresy

Když jsou aktualizace objektu uživatele synchronizovány s tenantem Azure AD, Azure AD aktualizuje hodnotu atributu MailNickName pouze v případě, že je aktualizace místní hodnoty atributu mailNickName.

>[!IMPORTANT]
>Azure AD přepočítá hodnotu atributu UserPrincipalName pouze v případě, že aktualizace místní ho atributu UserPrincipalName atribut/alternativní přihlašovací ID hodnota je synchronizována s tenantem Azure AD. 
>
>Vždy, když Azure AD přepočítá Atribut UserPrincipalName, přepočítá také MOERA. 

## <a name="upn-scenarios"></a>Scénáře UPN
Následují příklady scénářů výpočtu hlavního upn na základě daného scénáře.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scénář 1: Neověřená přípona upn – počáteční synchronizace

![Scénář1](./media/plan-connect-userprincipalname/example1.png)

Místní objekt uživatele:
- mailNickName &lt;: není nastaveno&gt;
- proxyAdresy :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- Userprincipalname:us3@contoso.com

Poprvé synchronizovat objekt uživatele do klienta Azure AD
- Nastavte atribut Azure AD MailNickName na primární předponu adresy SMTP.
- Nastavte MOERA &lt;na&gt; MailNickName&#64;&lt;počáteční doméně&gt;.
- Nastavte atribut Azure AD UserPrincipalName na MOERA.

Objekt uživatele klienta Azure AD:
- MailNickName : us1           
- Userprincipalname:us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scénář 2: Neověřená přípona upn – nastavit místní atribut mailNickName

![Scénář2](./media/plan-connect-userprincipalname/example2.png)

Místní objekt uživatele:
- mailNickName : us4
- proxyAdresy :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- Userprincipalname:us3@contoso.com

Synchronizace aktualizace v místním atributu mailNickName do klienta Azure AD
- Aktualizujte atribut Azure AD MailNickName místním atributem mailNickName.
- Vzhledem k tomu, že neexistuje žádná aktualizace atributu userPrincipalName v místním prostředí, neexistuje žádná změna atributu Azure AD UserPrincipalName.

Objekt uživatele klienta Azure AD:
- MailNickJméno : us4
- Userprincipalname:us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scénář 3: Neověřená přípona hlavního názvu uživatele – aktualizace místního atributu userPrincipalName

![Scénář3](./media/plan-connect-userprincipalname/example3.png)

Místní objekt uživatele:
- mailNickName : us4
- proxyAdresy :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- Userprincipalname:us5@contoso.com

Synchronizace aktualizace v místním atributu userPrincipalName s klientem Azure AD
- Aktualizace na místní userPrincipalName atribut aktivuje přepočet MOERA a Azure AD UserPrincipalName atribut.
- Nastavte MOERA &lt;na&gt; MailNickName&#64;&lt;počáteční doméně&gt;.
- Nastavte atribut Azure AD UserPrincipalName na MOERA.

Objekt uživatele klienta Azure AD:
- MailNickJméno : us4
- Userprincipalname:us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scénář 4: Neověřená přípona hlavního název uživatele – aktualizace primární adresy SMTP a atribut místní pošty

![Scénář4](./media/plan-connect-userprincipalname/example4.png)

Místní objekt uživatele:
- mailNickName : us4
- proxyAdresy :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- Userprincipalname:us5@contoso.com

Synchronizace aktualizace v místním atributu pošty a primární adrese SMTP do klienta Azure AD
- Po počáteční synchronizaci objektu uživatele aktualizace atributu místní pošty a primární adresy SMTP neovlivní Azure AD MailNickName nebo UserPrincipalName atribut.

Objekt uživatele klienta Azure AD:
- MailNickJméno : us4
- Userprincipalname:us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scénář 5: Ověřená přípona hlavního názvu uživatele – aktualizace místní přípony atributu userPrincipalName

![Scénář5](./media/plan-connect-userprincipalname/example5.png)

Místní objekt uživatele:
- mailNickName : us4
- proxyAdresy :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- Userprincipalname:us5@verified.contoso.com

Synchronizace aktualizace v místním atributu userPrincipalName s klientem Azure AD
- Aktualizace v místním atributu userPrincipalName aktivuje přepočet atributu Azure AD UserPrincipalName.
- Nastavte atribut Azure AD UserPrincipalName na místní atribut userPrincipalName, protože přípona UPN se ověří pomocí klienta Azure AD.

Objekt uživatele klienta Azure AD:
- MailNickJméno : us4     
- Userprincipalname:us5@verified.contoso.com

## <a name="next-steps"></a>Další kroky
- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)
