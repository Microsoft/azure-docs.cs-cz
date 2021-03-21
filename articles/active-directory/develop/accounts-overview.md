---
title: Účty Microsoft Identity Platform & profily klientů v Androidu | Azure
description: Přehled účtů Microsoft Identity Platform pro Android
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
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755963"
---
# <a name="accounts--tenant-profiles-android"></a>Účty a profily tenantů (Android)

Tento článek poskytuje přehled toho, co `account` je na platformě Microsoft identity.

Rozhraní API knihovny Microsoft Authentication Library (MSAL) nahrazuje pojem *uživatele* termínem *účet*. Jedním z důvodů je, že uživatel (lidský nebo softwarový agent) může mít nebo může používat víc účtů. Tyto účty mohou být v vlastní organizaci uživatele a/nebo v jiných organizacích, kterých je uživatel členem.

Účet na platformě Microsoft identity se skládá z těchto součástí:

- Jedinečný identifikátor.  
- Jedna nebo více přihlašovacích údajů sloužících k demonstrování vlastnictví a řízení účtu.
- Jeden nebo více profilů sestávající z atributů, jako například:
  - Obrázek, křestní jméno, název rodiny, název, umístění kanceláře
- Účet má zdroj autority nebo systému záznamu. Toto je systém, ve kterém je účet vytvořen a kde jsou uloženy přihlašovací údaje přidružené k tomuto účtu. V systémech s více klienty, jako je platforma Microsoft identity, je systém záznamů, `tenant` kde byl účet vytvořen. Tento tenant se také označuje jako `home tenant` .
- Účty na platformě Microsoft identity mají následující systémy záznamů:
  - Azure Active Directory, včetně Azure Active Directory B2C.
  - Účet Microsoft (živé).
- Účty ze systémů záznamů mimo platformu Microsoft identity jsou reprezentovány v rámci platformy Microsoft identity, včetně těchto:
  - identity z připojených místních adresářů (Windows Server Active Directory)
  - externí identity z LinkedInu, GitHubu a tak dále.
  V těchto případech má účet jak zdrojový systém záznamu, tak systém záznamu v rámci platformy Microsoft identity.
- Platforma Microsoft Identity umožňuje používat jeden účet pro přístup k prostředkům patřícím do více organizací (Azure Active Directory tenantů).
  - Pokud chcete zaznamenat, že účet z jednoho systému záznamu (tenant AAD A) má přístup k prostředku v jiném systému záznamu (AAD tenant B), musí být tento účet reprezentovaný v tenantovi, kde je prostředek definovaný. To se provádí vytvořením místního záznamu účtu ze systému A v systému B.
  - Tento místní záznam, který je reprezentace účtu, je svázán s původním účtem.
  - MSAL zpřístupňuje tento místní záznam jako `Tenant Profile` .
  - Profil tenanta může mít různé atributy, které jsou vhodné pro místní kontext, jako je například název úlohy, umístění kanceláře, kontaktní údaje atd.
- Vzhledem k tomu, že účet může být přítomen v jednom nebo více klientech, může mít účet více než jeden profil.

> [!NOTE]
> MSAL považuje systém účet Microsoft (Live, MSA) za jiného tenanta v rámci platformy Microsoft identity. ID tenanta klienta účet Microsoft: `9188040d-6c67-4c5b-b112-36a304b66dad`

## <a name="account-overview-diagram"></a>Diagram přehledu účtu

![Diagram přehledu účtu](./media/accounts-overview/accounts-overview.svg)

Ve výše uvedeném diagramu:

- Účet `bob@contoso.com` se vytvoří v místní službě Windows Server Active Directory (původ místního systému záznamu).
- Účet `tom@live.com` se vytvoří v tenantovi účet Microsoft.
- `bob@contoso.com` má přístup k alespoň jednomu prostředku v následujících Azure Active Directory klientech:
  - contoso.com (cloudový systém záznamu propojený s místním systémem záznamů)
  - fabrikam.com
  - woodgrovebank.com
  - `bob@contoso.com`V každém z těchto tenantů se nachází profil tenanta.
- `tom@live.com` má přístup k prostředkům v následujících klientech Microsoftu:
  - contoso.com
  - fabrikam.com
  - `tom@live.com`V každém z těchto tenantů se nachází profil tenanta.
- Informace o tom, kdo a Bob v jiných klientech, se můžou lišit od záznamů v systému. Můžou se lišit podle atributů, jako je název úlohy, umístění kanceláře a tak dále. Můžou být členy skupin nebo rolí v rámci každé organizace (Azure Active Directory tenant). Tyto informace odkazujeme jako bob@contoso.com profil tenanta.

V diagramu bob@contoso.com a tom@live.com mít přístup k prostředkům v různých Azure Active Directory klientech. Další informace najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../external-identities/add-users-administrator.md).

## <a name="accounts-and-single-sign-on-sso"></a>Účty a jednotné přihlašování (SSO)

Mezipaměť tokenu MSAL ukládá *jeden obnovovací token* na účet. Tento aktualizační token se dá použít k tichému vyžádání přístupových tokenů z několika tenantů Microsoft Identity Platform. Když je na zařízení nainstalovaný zprostředkovatel, účet se spravuje pomocí zprostředkovatele a může se používat jednotné přihlašování v rámci zařízení.

> [!IMPORTANT]
> Účet business to Consumer (B2C) a chování aktualizačního tokenu se liší od zbytku platformy Microsoft identity. Další informace najdete v tématu [zásady B2C & účty](#b2c-policies--accounts).

## <a name="account-identifiers"></a>Identifikátory účtů

ID účtu MSAL není ID objektu účtu. Nedoporučuje se analyzovat ani nespoléhat na to, aby v rámci platformy Microsoft identity byly jakékoli jiné než jedinečné.

Z důvodu kompatibility s knihovnou Azure AD Authentication Library (ADAL) a k usnadnění migrace z ADAL na MSAL může MSAL vyhledat účty pomocí libovolného platného identifikátoru účtu, který je k dispozici v mezipaměti MSAL.  Například následující příkaz vždy načte stejný objekt účtu pro, tom@live.com protože každý z identifikátorů je platný:

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>Přístup k deklaracím účtu

Kromě vyžádání přístupového tokenu MSAL také vždy vyžádá token ID od každého tenanta. Provede to tím, že vždy požaduje následující rozsahy:

- OpenID
- profil

Token ID obsahuje seznam deklarací identity. `Claims` jsou páry název-hodnota týkající se účtu a slouží k vytvoření žádosti.

Jak už bylo zmíněno dříve, každý tenant, ve kterém existuje účet, může ukládat různé informace o účtu, včetně, ale ne omezení na atributy, jako je například pracovní pozice, umístění kanceláře a tak dále.

I když může být účet členem nebo hostem ve více organizacích, služba MSAL nedotazuje službu, aby získala seznam tenantů, kterých je účet členem. Místo toho MSAL sestaví seznam tenantů, ve kterých se účet nachází, v důsledku požadavků na tokeny, které byly provedeny.

Deklarace identity vystavené u objektu účtu jsou vždycky deklarace identity z/{Authority} ' Home tenant ' pro účet. Pokud se tento účet nepoužil k vyžádání tokenu pro svého domovského tenanta, MSAL nemůže poskytnout deklarace prostřednictvím objektu Account.  Například:

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> Pokud chcete zobrazit seznam deklarací identity dostupných z objektu účtu, přečtěte si [deklarace identity ve id_token](./id-tokens.md#claims-in-an-id_token) .

> [!TIP]
> Pokud chcete do svého id_token zahrnout další deklarace identity, přečtěte si volitelnou dokumentaci k deklaracím identity v tématu [Postupy: poskytnutí volitelných deklarací identity vaší aplikaci Azure AD.](./active-directory-optional-claims.md)

### <a name="access-tenant-profile-claims"></a>Přístup k deklaracím profilů tenanta

Chcete-li získat přístup k deklaracím účtu, jak se zobrazují v jiných klientech, musíte nejprve přetypovat objekt Account na `IMultiTenantAccount` . Všechny účty můžou být víceklientské, ale počet profilů klientů dostupných prostřednictvím MSAL je založený na tom, na kterých klientech jste požádali o tokeny pomocí aktuálního účtu.  Například:

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>Zásady B2C & účty

Aktualizace tokenů pro účet se nesdílí mezi B2C zásadami. V důsledku toho není možné použít tokeny jednotného přihlašování. Neznamená to, že jednotné přihlašování není možné. To znamená, že jednotné přihlašování musí používat interaktivní prostředí, ve kterém je soubor cookie k dispozici pro povolení jednotného přihlašování.

To také znamená, že pokud získáte tokeny pomocí různých zásad B2C, považují se za samostatné účty – každý s vlastním identifikátorem. Pokud chcete použít účet k vyžádání tokenu pomocí `acquireTokenSilent` , pak musíte vybrat účet ze seznamu účtů, které odpovídají zásadám, které používáte s požadavkem na token. Příklad:

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