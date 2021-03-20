---
title: Zabránění automatické akceleraci přihlašování ve službě Azure AD pomocí zásad zjišťování domovské sféry
description: Naučte se, jak zabránit domain_hint automatické akceleraci pro federované zprostředkovatelů identity.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 53dfdfaf37695059d6d52428c2ba109970d9f7f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589374"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Zakázat automatickou akceleraci pro federované IDP během přihlašování uživatelů pomocí zásad zjišťování domovské sféry

[Zásady zjišťování domovské sféry](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) nabízí správcům několik způsobů, jak určit, jak a kde se jejich uživatelé ověřují. `domainHintPolicy`Oddíl zásad hrd se používá k tomu, aby vám pomohla migrovat federované uživatele na cloudové přihlašovací údaje, jako je [Fido](../authentication/howto-authentication-passwordless-security-key.md), tím, že zajistí, aby vždycky navštívili přihlašovací stránku Azure AD a neautomaticky se urychlila na federované IDP kvůli parametrům domény.

Tato zásada je nutná v situacích, kdy aplikace, které správce nemůže během přihlašování řídit nebo aktualizovat pomocné parametry domény.  Například `outlook.com/contoso.com` odesílá uživatele na přihlašovací stránku s přidaným `&domain_hint=contoso.com` parametrem, aby automaticky urychlila uživatele přímo pro federované IDP pro `contoso.com` doménu. Uživatelé se spravovanými přihlašovacími údaji odeslanými do federovaného IDP se nemůžou přihlásit pomocí svých spravovaných přihlašovacích údajů, snížit zabezpečení a frustrující uživatele s náhodným přihlašováním. Správci, kteří používají spravovaná pověření, [by měli také nastavit tuto zásadu](#suggested-use-within-a-tenant) , aby měli jistotu, že uživatelé můžou vždycky používat svoje spravované přihlašovací údaje.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy podrobnosti

Oddíl DomainHintPolicy zásad HRD je objekt JSON, který umožňuje správci odhlásit určité domény a aplikace z použití pomocného parametru domény.  Tato funkce říká přihlašovací stránce služby Azure AD, aby se chovala, jako kdyby v `domain_hint` žádosti o přihlášení neexistoval parametr.

### <a name="the-respect-and-ignore-policy-sections"></a>Oddíly zásad dodržování a ignorování

|Sekce | Význam | Hodnoty |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Pokud se tato Nápověda k doméně pošle v žádosti, ignorujte ji. |Pole adres domény (například `contoso.com` ). Podporuje také `all_domains`|
|`RespectDomainHintForDomains`| Pokud se tato Nápověda k doméně pošle v žádosti, `IgnoreDomainHintForApps` zajistěte, aby byla i v případě, že aplikace v požadavku nemá automatickou akceleraci. To se používá k zpomalení zavedení využívaných pomocnéch parametrů domény v rámci vaší sítě – můžete určit, že by měly být některé domény stále urychlené. | Pole adres domény (například `contoso.com` ). Podporuje také `all_domains`|
|`IgnoreDomainHintForApps`| Pokud žádost z této aplikace přichází s pomocným parametrem domény, ignorujte ji. | Pole ID aplikací (GUID) Podporuje také `all_apps`|
|`RespectDomainHintForApps` |Pokud žádost z této aplikace přichází s parametrem domény, doplatí se i v případě `IgnoreDomainHintForDomains` , že tato doména zahrnuje. Používá se k zajištění toho, že některé aplikace budou fungovat i v případě, že se budou rušit bez použití doporučení domény. | Pole ID aplikací (GUID) Podporuje také `all_apps`|

### <a name="policy-evaluation"></a>Vyhodnocení zásad

Logika DomainHintPolicy se spouští u všech příchozích požadavků, které obsahují pomocný parametr domény, a zrychluje na základě dvou částí dat v žádosti – doména v pomocném parametru domény a ID klienta (aplikace). Krátké – "dodržování" pro doménu nebo aplikaci má přednost před instrukcí "Ignorovat" nápovědu k doméně pro danou doménu nebo aplikaci.

1. Pokud neexistují žádné zásady pro doporučení k doméně, nebo pokud žádný ze 4 sekcí neodkazuje na danou aplikaci nebo doporučení domény, [vyhodnotí se zbytek zásady hrd](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Pokud `RespectDomainHintForApps` `RespectDomainHintForDomains` v žádosti nebo v některém oddílu obsahuje nápovědu k aplikaci nebo doméně, bude se tento uživatel automaticky zrychluje na federované IDP, jak je požadováno.
1. Pokud v žádosti buď jedna (nebo obě), `IgnoreDomainHintsForApps` nebo `IgnoreDomainHintsForDomains` odkazuje na aplikaci nebo nápovědu k doméně, na které se neodkazují oddíly "dodržování", požadavek se automaticky nezrychluje a uživatel zůstane na přihlašovací stránce Azure AD, aby vám poskytl uživatelské jméno.

Po zadání uživatelského jména na přihlašovací stránce můžou uživatelé použít svoje spravované přihlašovací údaje, pokud mají nějaké zaregistrované.  Pokud se rozhodnete, že nepoužívají spravované přihlašovací údaje, nebo nejsou zaregistrované, budou se přicházet ke své federované IDP pro zadání přihlašovacích údajů jako obvykle.

## <a name="suggested-use-within-a-tenant"></a>Navrhované použití v rámci tenanta

Správci federovaných domén by měli nastavit tuto část zásad HRD ve čtvrtém plánu. Cílem tohoto plánu je nakonec, aby všichni uživatelé v tenantovi měli možnost používat své spravované přihlašovací údaje bez ohledu na doménu nebo aplikaci. tyto aplikace můžete ukládat, které mají pevné závislosti na `domain_hint` využití.  Tento plán pomáhá správcům najít tyto aplikace, vyloučí je z nových zásad a pokračovat ve vracení změn do zbytku tenanta.

1. Vyberte doménu, na kterou se má nejdřív Tato změna vrátit.  To bude vaše testovací doména, takže vyberte jednu z nich, která může být pro změny v uživatelském rozhraní podrobnější (tj. zobrazení jiné přihlašovací stránky).  Tato akce ignoruje všechny pomocné parametry domény ze všech aplikací, které používají tento název domény. [Nastavte](#configuring-policy-through-graph-explorer) tuto zásadu ve vašem tenantovi – výchozí zásada hrd:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Získejte zpětnou vazbu od uživatelů testovacích domén. Shromažďovat podrobnosti o aplikacích, které se podařilo přerušit v důsledku této změny – mají závislost na použití pomocného parametru domény a měly by se aktualizovat. Prozatím je přidejte do `RespectDomainHintForApps` oddílu:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Pokračování v rozšiřování zavedení zásady na nové domény a shromažďování dalších informací o zpětné vazbě.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Dokončete zavedení všech domén cíle a vylučte ty, které by měly být dále urychlené:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Až krok 4 dokončí všechny uživatele, kromě těch v `guestHandlingDomain.com` , může se přihlásit na přihlašovací stránce Azure AD i v případě, že by doporučení k doméně jinak způsobila automatické urychlení federovaného IDP.  Výjimkou je to, že aplikace požadující přihlášení je jednou z vyloučených aplikací – pro tyto aplikace budou všechna doporučení k doméně stále přijata.

## <a name="configuring-policy-through-graph-explorer"></a>Konfigurace zásad pomocí Graph Exploreru

Nastavte [zásady hrd](/graph/api/resources/homeRealmDiscoveryPolicy) jako obvykle pomocí Microsoft Graph.  

1. Udělte oprávnění Policy. ApplicationConfiguration v [Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer).  
1. Použít adresu URL `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. PUBLIKOVAT nové zásady na tuto adresu URL nebo do ní opravit, `/policies/homerealmdiscoveryPolicies/{policyID}` Pokud přepisujete stávající.

Obsah příspěvku nebo opravy:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

Nezapomeňte použít lomítka k úniku `Definition` oddílu JSON při použití grafu.  

`isOrganizationDefault` musí mít hodnotu true, ale zobrazovaný název a definice se mohou změnit.

## <a name="next-steps"></a>Další kroky

* [Povolit přihlašování k bezpečnostnímu klíči s nezabezpečenými hesly](../authentication/howto-authentication-passwordless-security-key.md)
* [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator](../authentication/howto-authentication-passwordless-phone.md)