---
title: Účty platformy identit Microsoftu & profily klientů v systému Android | Azure
description: Přehled účtů platformy identit Microsoftu pro Android
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: d0497ad68e7b29e6d8c83dd860ba8f509e229579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611890"
---
# <a name="accounts--tenant-profiles-android"></a>Účty a profily tenantů (Android)

Tento článek obsahuje přehled `account` toho, co je v platformě identit microsoftu.

Rozhraní MICROSOFT Authentication Library (MSAL) API nahradí termín *uživatele* termínem *účet*. Jedním z důvodů je, že uživatel (lidský nebo softwarový agent) může mít nebo může používat více účtů. Tyto účty mohou být ve vlastní organizaci uživatele nebo v jiných organizacích, které uživatel je členem.

Účet na platformě identit microsoftu se skládá z:

- Jedinečný identifikátor.  
- Jedno nebo více pověření používaných k prokázání vlastnictví/řízení účtu.
- Jeden nebo více profilů skládajících se z atributů, jako jsou:
  - Obrázek, Křestní jméno, Název rodiny, Název, Umístění kanceláře
- Účet má zdroj autority nebo systém záznamu. Toto je systém, kde je účet vytvořen a kde jsou uložena pověření přidružená k tomuto účtu. V systémech s více klienty, jako je platforma `tenant` identit microsoftu, je systém záznamu místem, kde byl účet vytvořen. Tento tenant je také `home tenant`označován jako .
- Účty na platformě microsoft identit mají následující systémy záznamu:
  - Azure Active Directory, včetně Služby Azure Active Directory B2C.
  - Účet Microsoft (Live).
- Účty ze systémů záznamu mimo platformu microsoft identit jsou reprezentovány v rámci platformy identit společnosti Microsoft, včetně:
  - identity z připojených místních adresářů (Windows Server Active Directory)
  - externí identity z LinkedIn, GitHub a tak dále.
  V těchto případech má účet systém původu záznamu i systém záznamu v rámci platformy identit společnosti Microsoft.
- Platforma identit Microsoftu umožňuje použití jednoho účtu pro přístup k prostředkům patřícím do více organizací (tenanty Služby Azure Active Directory).
  - Chcete-li zaznamenat, že účet z jednoho systému záznamu (AAD tenant A) má přístup k prostředku v jiném systému záznamu (Klient AAD B), musí být účet reprezentován v tenantovi, kde je prostředek definován. To se provádí vytvořením místního záznamu účtu ze systému A v systému B.
  - Tento místní záznam, tedy reprezentace obchodního vztahu, je vázán na původní účet.
  - MSAL zpřístupňuje tento `Tenant Profile`místní záznam jako .
  - Profil klienta může mít různé atributy, které jsou vhodné pro místní kontext, jako je například název úlohy, umístění kanceláře, kontaktní informace atd.
- Vzhledem k tomu, že účet může být k dispozici v jednom nebo více klientů, účet může mít více než jeden profil.

> [!NOTE]
> MSAL považuje systém účtů Microsoft (Live, MSA) za jiného klienta v rámci platformy identit microsoftu. ID klienta klienta účtu Microsoft je:`9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram přehledu účtů

![Diagram přehledu účtů](./media/accounts-overview/accounts-overview.svg)

Ve výše uvedeném diagramu:

- Účet `bob@contoso.com` je vytvořen v místním systému Windows Server Active Directory (původní místní systém záznamu).
- Účet `tom@live.com` se vytvoří v tenantovi účtu Microsoft.
- `bob@contoso.com`má přístup alespoň k jednomu prostředku v následujících klientech služby Azure Active Directory:
  - contoso.com (cloudový systém záznamů - napojený na místní systém záznamů)
  - fabrikam.com
  - woodgrovebank.com
  - Profil klienta `bob@contoso.com` pro existuje v každém z těchto klientů.
- `tom@live.com`má přístup k prostředkům v následujících klientech Microsoftu:
  - contoso.com
  - fabrikam.com
  - Profil klienta `tom@live.com` pro existuje v každém z těchto klientů.
- Informace o Tomovi a Bobovi v jiných nájemcích se mohou lišit od informací v systému záznamů. Mohou se lišit podle atributů, jako je funkce, umístění kanceláře a tak dále. Mohou být členy skupin nebo rolí v rámci každé organizace (Tenant Služby Azure Active Directory). Tyto informace označujeme bob@contoso.com jako profil klienta.

V diagramu bob@contoso.com tom@live.com a mít přístup k prostředkům v různých tenantů Služby Azure Active Directory. Další informace najdete v tématu [Přidání uživatelů spolupráce Služby Azure Active Directory B2B na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

## <a name="accounts-and-single-sign-on-sso"></a>Účty a jednotné přihlašování (SSO)

Mezipaměť tokenu MSAL ukládá *jeden obnovovací token* na účet. Tento obnovovací token lze použít k bezobslužné žádosti o přístupové tokeny od více klientů platformy identit y Microsoft. Když je makléř nainstalován na zařízení, účet je spravován zprostředkovatelem a jednotné přihlášení pro celé zařízení bude možné.

> [!IMPORTANT]
> Účet business to consumer (B2C) a chování tokenů aktualizace se liší od zbytku platformy identit Microsoftu. Další informace naleznete [v tématu Zásady & účty b2c](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identifikátory účtů

ID účtu MSAL není ID objektu účtu. Není určen k analýzě a / nebo se spoléhal na sdělit něco jiného než jedinečnost v rámci platformy identity Microsoft.

Z důvodu kompatibility s knihovnou ověřování Azure AD (ADAL) a usnadnění migrace z ADAL do MSAL může služba MSAL vyhledat účty pomocí libovolného platného identifikátoru pro účet dostupný v mezipaměti MSAL.  Například následující bude vždy načíst stejný tom@live.com objekt účtu, protože každý z identifikátorů je platný:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Přístup k deklaracím o účtu

Kromě vyžádání přístupového tokenu msal také vždy požaduje id token od každého klienta. Činí tak tím, že vždy požaduje následující obory:

- Openid
- profil

Token ID obsahuje seznam deklarací identity. `Claims`jsou dvojice název/hodnota týkající se účtu a slouží k požadavku.

Jak již bylo zmíněno dříve, každý klient, kde účet existuje, může ukládat různé informace o účtu, včetně, ale bez omezení na atributy, jako jsou: název pracovní pozice, umístění kanceláře a tak dále.

Zatímco účet může být členem nebo hostem ve více organizacích, Služba MSAL nedotazuje službu, aby získala seznam klientů, jehož je účet členem. Místo toho MSAL vytvoří seznam klientů, které je k dispozici v účtu, v důsledku požadavků tokenu, které byly provedeny.

Deklarace vystavené na objektu účtu jsou vždy deklarace z 'domácí nájemce' /{autorita} pro účet. Pokud tento účet nebyl použit k vyžádání tokenu pro svého domovského tenanta, MSAL nemůže poskytnout deklarace prostřednictvím objektu účtu.  Například:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Chcete-li zobrazit seznam deklarací dostupných z objektu účtu, podívejte se na [deklarace identity v id_token](https://docs.microsoft.com/azure/active-directory/develop/id-tokens#claims-in-an-id_token)

> [!TIP]
> Pokud chcete do id_token zahrnout další deklarace identity, přečtěte si dokumentaci k volitelným deklaracím identity v části [Postup: Poskytnutí volitelných deklarací aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

### <a name="access-tenant-profile-claims"></a>Přístup k deklaracím profilu klienta

Chcete-li získat přístup k deklaracím o účtu tak, jak `IMultiTenantAccount`se zobrazují v jiných klientech, musíte nejprve přetypovat objekt účtu na aplikaci . Všechny účty mohou být víceklientské, ale počet profilů klientů, které jsou k dispozici prostřednictvím služby MSAL, je založen na tom, kteří klienti, které jste požádali o tokeny, používají císy aktuálně.  Například:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Zásady B2C & účty

Aktualizovat tokeny pro účet nejsou sdíleny v rámci zásad B2C. V důsledku toho jednotné přihlašování pomocí tokenů není možné. To neznamená, že jednotné přihlašování není možné. Znamená to, že jednotné přihlašování musí používat interaktivní prostředí, ve kterém je soubor cookie k dispozici, aby bylo možné je přihlašovat.

To také znamená, že v případě MSAL, pokud získáte tokeny pomocí různých zásad B2C, pak jsou tyto považovány za samostatné účty - každý s vlastním identifikátorem. Pokud chcete použít účet k vyžádání `acquireTokenSilent`tokenu pomocí , pak budete muset vybrat účet ze seznamu účtů, které odpovídají zásady, které používáte s žádostí o token. Například:

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```
