---
title: Naplnění Azure AD UserPrincipalName
description: Následující dokument popisuje, jak je naplněn atribut UserPrincipalName.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: e105d4909705622a931c51bcb7cf0a9db4179525
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279597"
---
# <a name="azure-ad-userprincipalname-population"></a>Naplnění Azure AD UserPrincipalName

Tento článek popisuje, jak se v Azure Active Directory (Azure AD) naplní atribut UserPrincipalName.
Hodnota atributu UserPrincipalName je uživatelské jméno Azure AD pro uživatelské účty.

## <a name="upn-terminology"></a>Terminologie hlavního názvu uživatele
V tomto článku se používá následující terminologie:

|Období|Popis|
|-----|-----|
|Počáteční doména|Výchozí doména (onmicrosoft.com) v Tenantovi služby Azure AD. Například contoso.onmicrosoft.com.|
|Adresa pro směrování e-mailů Microsoft Online (MOERA)|Azure AD vypočítá MOERA z atributu Azure AD MailNickName a počáteční doménu Azure AD jako &lt; MailNickName &gt;&#64;&lt; počáteční doménu &gt; .|
|Místní atribut mailNickName|Atribut ve službě Active Directory, jehož hodnota představuje alias uživatele v organizaci Exchange.|
|Atribut místních e-mailů|Atribut ve službě Active Directory, jehož hodnota představuje e-mailovou adresu uživatele|
|Primární adresa SMTP|Primární e-mailová adresa objektu příjemce Exchange. Například SMTP: User \@ contoso.com.|
|Alternativní ID přihlášení|Místní atribut jiný než UserPrincipalName, jako je například atribut mail, který se používá pro přihlášení.|

## <a name="what-is-userprincipalname"></a>Co je UserPrincipalName?
UserPrincipalName je atribut, který je přihlašovacím jménem pro uživatele v Internetu na základě standardu [RFC 822](https://www.ietf.org/rfc/rfc0822.txt)pro Internet. 

### <a name="upn-format"></a>Formát UPN
Hlavní název uživatele se skládá z předpony hlavního názvu uživatele (UPN) (název uživatelského účtu) a přípony UPN (název domény DNS). Předpona je připojena k příponě pomocí symbolu " \@ ". Například "někdo \@ example.com". Hlavní název uživatele (UPN) musí být jedinečný mezi všemi objekty zabezpečení v rámci doménové struktury adresáře. 

## <a name="upn-in-azure-ad"></a>Hlavní název uživatele (UPN) ve službě Azure AD 
Služba Azure AD používá hlavní název uživatele (UPN), který uživatelům umožňuje přihlásit se.  Hlavní název uživatele (UPN), který může uživatel použít, závisí na tom, jestli byla doména ověřená.  Pokud byla doména ověřená, může se uživatel s touto příponou přihlásit do Azure AD.  

Atribut je synchronizován pomocí Azure AD Connect.  Během instalace můžete zobrazit ověřené domény a ty, které ne.

   ![Neověřené domény](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternativní ID přihlášení
V některých prostředích můžou koncoví uživatelé znát jenom svou e-mailovou adresu a ne jejich hlavní název uživatele (UPN).  Použití e-mailové adresy může být způsobeno podnikovou zásadou nebo místní závislostí obchodní aplikace.

Alternativní ID přihlášení vám umožní nakonfigurovat přihlašovací prostředí, kde se uživatelé můžou přihlašovat pomocí atributu, který je jiný než jeho hlavní název uživatele (UPN), jako je například pošta.

Pokud chcete povolit alternativní přihlašovací ID k Azure AD, při použití Azure AD Connect se nevyžadují žádné další kroky konfigurace. Alternativní ID lze nakonfigurovat přímo z průvodce. Viz Konfigurace přihlášení ke službě Azure AD pro vaše uživatele v části synchronizace. V rozevíracím seznamu **hlavní název uživatele** vyberte atribut pro alternativní ID přihlášení.

![Neověřené domény](./media/plan-connect-userprincipalname/altloginid.png)  

Další informace najdete v tématu [Konfigurace alternativního přihlašovacího ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) a [Konfigurace přihlášení ke službě Azure AD](how-to-connect-install-custom.md#azure-ad-sign-in-configuration) .

## <a name="non-verified-upn-suffix"></a>Neověřená přípona UPN
Pokud není u tenanta Azure AD ověřená přípona místního atributu UserPrincipalName nebo alternativní ID přihlášení, je hodnota atributu UserPrincipalName služby Azure AD nastavená na MOERA. Azure AD vypočítá MOERA z atributu Azure AD MailNickName a počáteční doménu Azure AD jako &lt; MailNickName &gt;&#64;&lt; počáteční doménu &gt; .

## <a name="verified-upn-suffix"></a>Ověřená přípona UPN
Pokud je u tenanta Azure AD ověřená přípona místního nebo alternativního přihlašovacího ID, bude hodnota atributu UserPrincipalName služby Azure AD stejná jako u atributu místní hodnoty UserPrincipalName nebo alternativní přihlašovací ID.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Výpočet hodnoty atributu Azure AD MailNickName
Vzhledem k tomu, že hodnota atributu UserPrincipalName Azure AD může být nastavená na MOERA, je důležité pochopit, jak se počítá hodnota atributu Azure AD MailNickName, což je předpona MOERA.

Když je objekt uživatele synchronizován do tenanta služby Azure AD poprvé, služba Azure AD zkontroluje v daném pořadí následující položky a nastaví hodnotu atributu MailNickName na první existující položku:

- Místní atribut mailNickName
- Předpona primární adresy SMTP
- Předpona atributů místních e-mailů
- Předpona místního atributu userPrincipalName nebo alternativního přihlašovacího ID
- Předpona sekundární adresy SMTP

Pokud jsou aktualizace objektu uživatele synchronizovány s klientem služby Azure AD, služba Azure AD aktualizuje hodnotu atributu MailNickName pouze v případě, že je k dispozici aktualizace hodnoty atributu on-premises mailNickName.

>[!IMPORTANT]
>Azure AD přepočítá hodnotu atributu UserPrincipalName jenom v případě, že se k Tenantovi služby Azure AD synchronizuje aktualizace pro místní atribut UserPrincipalName nebo hodnota alternativního přihlašovacího ID. 
>
>Kdykoli služba Azure AD přepočítá atribut UserPrincipalName, přepočítá také MOERA. 

## <a name="upn-scenarios"></a>Scénáře hlavního názvu uživatele (UPN)
Níže jsou uvedené příklady scénářů, jak se hlavní název uživatele vypočítá na základě daného scénáře.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Scénář 1: neověřená přípona UPN – počáteční synchronizace

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

Místní uživatelský objekt:
- mailNickName: &lt; nenastavené&gt;
- proxyAddresses: { SMTP:us1@contoso.com }
- modul us2@contoso.com
- Třídy us3@contoso.com

Při prvním synchronizování objektu uživatele do tenanta Azure AD
- Nastavte atribut MailNickName Azure AD na primární předponu adresy SMTP.
- Nastavte MOERA na  &lt; MailNickName &gt;&#64;&lt; počáteční doménu &gt; .
- Nastavte atribut Azure AD UserPrincipalName na MOERA.

Objekt uživatele tenanta Azure AD:
- MailNickName: US1           
- Třídy us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Scénář 2: neověřená přípona UPN – nastavení místního atributu mailNickName

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

Místní uživatelský objekt:
- mailNickName: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- modul us2@contoso.com
- Třídy us3@contoso.com

Synchronizace aktualizace na místním atributu mailNickName do tenanta Azure AD
- Aktualizuje atribut MailNickName Azure AD pomocí atributu místní mailNickName.
- Vzhledem k tomu, že není k dispozici žádná aktualizace pro místní atribut userPrincipalName, nedošlo k žádné změně atributu UserPrincipalName služby Azure AD.

Objekt uživatele tenanta Azure AD:
- MailNickName: US4
- Třídy us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Scénář 3: neověřená přípona UPN – aktualizace atributu on-premises userPrincipalName

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

Místní uživatelský objekt:
- mailNickName: US4
- proxyAddresses: { SMTP:us1@contoso.com }
- modul us2@contoso.com
- Třídy us5@contoso.com

Synchronizace aktualizace pro místní atribut userPrincipalName pro tenanta Azure AD
- Aktualizace pro místní atribut userPrincipalName aktivuje přepočítání atributu MOERA a Azure AD UserPrincipalName.
- Nastavte MOERA na &lt; MailNickName &gt;&#64;&lt; počáteční doménu &gt; .
- Nastavte atribut Azure AD UserPrincipalName na MOERA.

Objekt uživatele tenanta Azure AD:
- MailNickName: US4
- Třídy us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>Scénář 4: neověřená přípona UPN – aktualizace primární adresy SMTP a místního atributu pošty

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

Místní uživatelský objekt:
- mailNickName: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- modul us7@contoso.com
- Třídy us5@contoso.com

Synchronizovat aktualizaci u atributů místních e-mailů a primárních adres SMTP pro tenanta Azure AD
- Po počáteční synchronizaci objektu uživatele nebudou aktualizace atributu on-premises mail a primární adresy SMTP nijak ovlivněny atributem Azure AD MailNickName nebo UserPrincipalName.

Objekt uživatele tenanta Azure AD:
- MailNickName: US4
- Třídy us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Scénář 5: ověřená přípona UPN – aktualizace místní přípony atributu userPrincipalName

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

Místní uživatelský objekt:
- mailNickName: US4
- proxyAddresses: { SMTP:us6@contoso.com }
- modul us7@contoso.com
- Třídy us5@verified.contoso.com

Synchronizace aktualizace pro místní atribut userPrincipalName pro tenanta Azure AD
- Aktualizace pro místní atribut userPrincipalName aktivuje přepočítání atributu Azure AD UserPrincipalName.
- Nastavte atribut pro Azure AD UserPrincipalName na místní atribut userPrincipalName, protože pro tenanta Azure AD se ověřuje přípona hlavního názvu uživatele (UPN).

Objekt uživatele tenanta Azure AD:
- MailNickName: US4     
- Třídy us5@verified.contoso.com

## <a name="next-steps"></a>Další kroky
- [Integrace místních adresářů do služby Azure Active Directory](whatis-hybrid-identity.md)
- [Vlastní instalace služby Azure AD Connect](how-to-connect-install-custom.md)