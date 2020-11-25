---
title: Zajištění odolnosti pomocí osvědčených postupů pro vývojáře pomocí Azure AD B2C | Microsoft Docs
description: Zajištění odolnosti pomocí osvědčených postupů pro vývojáře v rámci zákaznických identit a přístupu pomocí Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919670"
---
# <a name="resilience-through-developer-best-practices"></a>Zajištění odolnosti pomocí osvědčených postupů pro vývojáře

V tomto článku sdílíme několik kurzů, které jsou založené na našich zkušenostech při práci s velkými zákazníky. Tato doporučení můžete zvážit v návrhu a implementaci vašich služeb.

![Obrázek ukazuje komponenty prostředí pro vývojáře](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Použití knihovny Microsoft Authentication Library (MSAL)

Knihovna [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) a [Microsoft Identity web Authentication Library pro ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries) zjednodušuje získávání, správu, ukládání do mezipaměti a aktualizaci tokenů, které aplikace vyžaduje. Tyto knihovny jsou optimalizované speciálně pro podporu identity Microsoftu včetně funkcí, které zlepšují odolnost aplikací.

Vývojáři by měli přijmout nejnovější verze MSAL a stále aktuální. Podívejte [se, jak zvýšit odolnost ověřování a autorizace](resilience-app-development-overview.md) ve vašich aplikacích. Pokud je to možné, vyhněte se implementaci vlastního zásobníku pro ověřování a použití dobře zavedených knihoven.

## <a name="optimize-directory-reads-and-writes"></a>Optimalizace čtení a zápisu adresářů

Služba Microsoft Azure AD B2C Directory podporuje miliardy ověřování za den. Je navržený pro vysokou míru čtení za sekundu. Optimalizujte zápisy, abyste minimalizovali závislosti a zvýšili odolnost.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Optimalizace čtení a zápisu adresářů

- **Vyhněte se funkcím zápisu do adresáře při přihlašování**: Nikdy nespouštějte zápis při přihlášení bez předběžných podmínek (if klauzule) ve vlastních zásadách. Jeden případ použití, který vyžaduje zápis při přihlašování, je [migrace uživatelských hesel za běhu](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Vyhněte se jakémukoli scénáři, který vyžaduje zápis při každém přihlášení.

  - [Předpoklady](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys) v cestě uživatele budou vypadat takto:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Odolnost proti sestavování na přihlášení řízené robotem [integrací s CAPTCHA systémem](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - K simulaci registrace a přihlašování použijte [ukázku zátěžového testování](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) . 

- **Pochopení omezení**: adresář implementuje pravidla omezení aplikace i klienta. Existují další omezení přenosové rychlosti pro operace čtení/získání, zápisu/odeslání, aktualizace, vložení a odstranění/odstranění a každá operace má jiné limity.

  - Zápis v době přihlášení bude zařazen do příspěvku pro nové uživatele nebo pro stávající uživatele.

  - Vlastní zásada, která při každém přihlášení vytvoří nebo aktualizuje uživatele, může potenciálně narazit na úroveň žádosti PUT nebo POST. Stejné limity platí při aktualizaci objektů adresáře přes Azure AD nebo Microsoft Graph. Podobně Projděte si čtení a sledujte počet čtení při každém přihlášení k minimálnímu.

  - Odhad zátěže ve špičce pro předpověď četnosti zápisů do adresáře a zamezení omezení. Špičkové odhady provozu by měly zahrnovat odhady pro akce, jako je například registrace, přihlašování a ověřování MFA (Multi-Factor Authentication). Ujistěte se, že jste otestovali Azure AD B2C systém i aplikaci pro provoz ve špičce. Je možné, že Azure AD B2C může zpracovat zatížení bez omezení, když aplikace nebo služby pro příjem dat nebudou.

  - Pochopte a naplánujte časovou osu migrace. Při plánování migrace uživatelů na Azure AD B2C pomocí Microsoft Graph zvažte omezení aplikace a tenanta a vypočítejte dobu potřebnou k dokončení migrace uživatelů. Pokud rozdělíte úlohu vytvoření uživatele nebo skript pomocí dvou aplikací, můžete použít limit pro jednotlivé aplikace. Pořád musí zůstat pod prahovou hodnotou pro každého tenanta.

  - Pochopení dopadů úlohy migrace na jiné aplikace. Vezměte v úvahu živý provoz poskytovaný jinými přijímajícími aplikacemi, abyste se ujistili, že nebudete mít na úrovni tenanta omezení a vyčerpání prostředků pro vaši živou aplikaci. Další informace najdete v tématu [pokyny k omezení Microsoft Graph](https://docs.microsoft.com/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Prodloužení životnosti tokenů

V nepravděpodobném případě, když služba Azure AD B2C Authentication nemůže dokončit nové registrace a přihlášení, můžete stále poskytovat zmírnění pro uživatele, kteří jsou přihlášení. Pomocí [Konfigurace](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)můžete uživatelům, kteří jsou již přihlášeni, pokračovat v používání aplikace bez pozorovaného přerušení, dokud se uživatel z aplikace nebo vypršení platnosti [relace](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) nestane z důvodu nečinnosti.

Vaše podnikové požadavky a požadované prostředí pro koncové uživatele budou určovat četnost obnovení tokenu pro webové i jednostránkové aplikace (jednostránkové).

### <a name="how-to-extend-token-lifetimes"></a>Jak rozšířím životnosti tokenů

- **Webové aplikace**: u webových aplikací, kde se ověřovací token ověřuje na začátku přihlašování, aplikace závisí na souboru cookie relace, aby bylo možné pokračovat v rozšiřování platnosti relace.

  - Umožněte uživatelům zůstat přihlášeni pomocí implementace kumulovaných časů relací, které budou dále obnovovat relace na základě aktivity uživatelů. Pokud dojde k výpadku dlouhodobého vystavování tokenu, je možné tyto časy relace dále zvýšit jako jednorázová konfiguraci aplikace. Zachovejte dobu života relace do maximálního povoleného počtu.

- **Jednostránkové**: zabezpečené ověřování na základě přístupových tokenů pro volání rozhraní API může záviset. SPA tradičně používá implicitní tok, který nevede k aktualizačnímu tokenu. SPA může pomocí skrytého prvku IFRAME provádět nové žádosti o tokeny proti koncovému bodu autorizace, pokud má prohlížeč stále aktivní relaci s Azure AD B2C. Pro jednostránkové je k dispozici několik možností, které uživateli umožňují pokračovat v používání aplikace.

  - Prodlužte dobu platnosti přístupového tokenu tak, aby splňovala vaše podnikové požadavky.

  - Sestavte aplikaci tak, aby jako proxy server používala bránu API. V této konfiguraci se SPA načítá bez ověřování a volání rozhraní API se provádí v bráně API. Brána API odesílá uživatele prostřednictvím procesu přihlášení pomocí [udělení autorizačního kódu](https://oauth.net/2/grant-types/authorization-code/) na základě zásad a ověří uživatele. Následně se relace ověřování mezi bránou API a klientem udržuje pomocí ověřovacího souboru cookie. Rozhraní API se obsluhují z brány API pomocí tokenu, který je získaný bránou API, nebo nějaké jiné metody přímé ověřování, jako jsou certifikáty, přihlašovací údaje klienta nebo klíče rozhraní API.

  - [Migrujte své zabezpečené ověřování na základě implicitního udělení](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) [autorizačního kódu do toku](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application) pomocí klíčového klíče pro výměnu kódu (PKCE) a podporu sdílení prostředků mezi zdroji (CORS). Migrujte svoji aplikaci z MSAL.js 1. x na MSAL.js 2. x, abyste mohli realizovat odolnost webových aplikací.

  - U mobilních aplikací doporučujeme, abyste narozšířili dobu života aktualizačního tokenu i doby platnosti přístupového tokenu.

- **Back-endové nebo mikroslužby aplikace**: vzhledem k tomu, že back-end aplikace (démon) nejsou interaktivní a nejsou v kontextu uživatele, je u potenciálního procesu krádeže tokenů výrazně snížena platnost. Doporučujeme, abyste si vyrovnali rovnováhu mezi zabezpečením a životností a nastavili životnost dlouhého tokenu.

## <a name="configure-single-sign-on"></a>Konfigurace jednotného přihlašování

S [jednotným přihlašováním (SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)se uživatelé přihlásí jedním účtem a získají přístup k více aplikacím. Aplikace může být webová, mobilní nebo jediná stránková aplikace (SPA), bez ohledu na platformu nebo název domény. Když se uživatel poprvé přihlásí k aplikaci, Azure AD B2C trvá [relaci založenou na souborech cookie](https://docs.microsoft.com/azure/active-directory-b2c/session-overview).

Po následné žádosti o ověření Azure AD B2C přečte a ověří relaci založenou na souborech cookie a vydá přístupový token bez vyzvání uživatele k opětovnému přihlášení. Pokud je jednotné přihlašování nakonfigurováno s omezeným oborem v zásadě nebo v aplikaci, bude pozdější přístup k dalším zásadám a aplikacím vyžadovat nové ověřování.

### <a name="how-to-configure-sso"></a>Postup konfigurace jednotného přihlašování

[NAKONFIGURUJTE jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) na úrovni tenanta (výchozí), aby bylo možné ve vašem tenantovi více aplikací a uživatelských toků sdílet stejnou relaci uživatele. Konfigurace na úrovni tenanta poskytuje nejvyšší odolnost proti novému ověřování.  

## <a name="safe-deployment-practices"></a>Postupy bezpečného nasazení

Nejběžnějšími výpadky služby jsou změny kódu a konfigurace. Přijetí procesů a nástrojů nepřetržité integrace a průběžné doručování (CICD) a nástrojů pro rychlé nasazení ve velkém měřítku a snížení lidských chyb při testování a nasazování do produkčního prostředí. CICD pro snižování a efektivitu chyb a jejich konzistenci. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) je příkladem CICD.

## <a name="web-application-firewall"></a>Firewall webových aplikací

Chraňte své aplikace proti známým chybám zabezpečení, jako jsou útoky distribuovaných útoků DDoS (Distributed Denial of Service), injektáže SQL, skriptování mezi weby, vzdálené spuštění kódu a mnoho dalších, jak je popsáno v [OWASP prvních 10](https://owasp.org/www-project-top-ten/). Nasazení firewallu webových aplikací (WAF) se může chránit před běžnými zneužitím a ohrožením zabezpečení.

- Využijte Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview), která poskytuje centralizovanou ochranu proti útokům.

- Využijte WAF s Azure AD [Identity Protection a podmíněný přístup k zajištění vícevrstvé ochrany](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) při použití Azure AD B2C.  

## <a name="secrets-rotation"></a>Rotace tajných kódů

Azure AD B2C používá tajné klíče pro aplikace, rozhraní API, zásady a šifrování. Zabezpečené ověřování tajných kódů, externí interakce a úložiště. National Institute of Standards and Technology (NIST) volá časový rozsah, během kterého je konkrétní klíč autorizován pro použití legitimními entitami a cryptoperiod. Vyberte správnou délku [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) , která bude vyhovovat vašim obchodním potřebám. Vývojáři potřebují ručně nastavit vypršení platnosti a před vypršením jejich platnosti znovu otočit tajná klíč.

### <a name="how-to-implement-secret-rotation"></a>Jak implementovat rotaci tajných klíčů

- Pro podporované prostředky použijte [spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) a ověřte je u jakékoli služby, která podporuje ověřování Azure AD. Pokud používáte spravované identity, můžete prostředky spravovat automaticky, včetně rotace přihlašovacích údajů.

- Vezměte v úvahu inventarizaci všech [klíčů a certifikátů nakonfigurovaných](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) v Azure AD B2C. Tento seznam bude nejspíš zahrnovat klíče používané ve vlastních zásadách, [rozhraních API](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api), tokenu PODPISového identifikátoru a certifikátech pro SAML.

- Pomocí CICD můžete otočit tajné kódy, jejichž platnost brzy vyprší do dvou měsíců od očekávaného období špičky. Doporučené maximum cryptoperiod privátních klíčů přidružených k certifikátu je jeden rok.

- Proaktivně monitorujte a otáčejte přihlašovací údaje k rozhraní API, jako jsou hesla a certifikáty.

## <a name="test-rest-apis"></a>Testovat rozhraní REST API

V kontextu odolnosti musí testování rozhraní REST API zahrnovat ověřování – kódů HTTP, datové části odpovědi, záhlaví a výkon. Testování by nemělo zahrnovat pouze testy cest na cestách, ale také kontroluje, zda rozhraní API zpracovává scénáře problému korektně.

### <a name="how-to-test-apis"></a>Jak testovat rozhraní API

Doporučujeme, aby váš plán testování zahrnoval [komplexní testy rozhraní API](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing). Pokud plánujete, že máte v úmyslu nadcházející nárůst z důvodu propagačního nebo nedovoleného provozu, je nutné zkontrolovat zátěžové testování s novými odhady. Proveďte zátěžové testování vašich rozhraní API a Content Delivery Network (CDN) ve vývojářském prostředí, a ne v produkčním prostředí.

## <a name="next-steps"></a>Další kroky

- [Prostředky odolnosti pro vývojáře Azure AD B2C](resilience-b2c.md)
  - [Odolné prostředí koncového uživatele](resilient-end-user-experience.md)
  - [Odolná rozhraní s externími procesy](resilient-external-processes.md)
  - [Odolnost prostřednictvím monitorování a analýzy](resilience-with-monitoring-alerting.md)
- [Odolnost sestavení v infrastruktuře ověřování](resilience-in-infrastructure.md)
- [Zvýšení odolnosti při ověřování a autorizaci ve vašich aplikacích](resilience-app-development-overview.md)
