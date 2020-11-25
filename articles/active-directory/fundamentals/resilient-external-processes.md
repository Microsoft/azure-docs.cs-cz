---
title: Odolná rozhraní s externími procesy pomocí Azure AD B2C | Microsoft Docs
description: Metody pro vytváření odolných rozhraní s externími procesy
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
ms.openlocfilehash: c96856c988cae891e64ddf460d61851102e4666c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919397"
---
# <a name="resilient-interfaces-with-external-processes"></a>Odolná rozhraní s externími procesy

V tomto článku vám poskytneme pokyny k plánování a implementaci rozhraní RESTFul API v cestě uživatele a zajištění odolnosti aplikace proti selhání rozhraní API.

![Image zobrazuje rozhraní s komponentami externího procesu.](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Zajištění správného umístění rozhraní API

Zásady architektury IEF (identity Experience Framework) umožňují volat externí systém pomocí [technického profilu rozhraní RESTful API](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile). Externí systémy nejsou ovládány prostředím runtime IEF a jsou potenciálním bodem selhání.

### <a name="how-to-manage-external-systems-using-apis"></a>Jak spravovat externí systémy pomocí rozhraní API

- Při volání rozhraní pro přístup k určitým datům ověřte, zda se data budou řídit rozhodnutím ověřování. Vyhodnoťte, zda jsou informace zásadní pro základní funkce aplikace. Například elektronické obchodování vs. sekundární funkce, jako je například Správa. Pokud nejsou informace požadovány pro ověřování a jsou požadovány pouze pro sekundární scénáře, zvažte přesunutí volání do aplikační logiky.

- Pokud jsou data potřebná pro ověřování relativně statická a malá a nemají žádný jiný obchodní důvod pro externě z adresáře, zvažte, že byste měli v adresáři.

- Pokud je to možné, odeberte volání rozhraní API z předběžně ověřené cesty. Pokud nemůžete, pak musíte umístit přísné ochrany pro útoky DoS (Denial of Service) a DDoS (Distributed Restricting of Service) před vašimi rozhraními API. Útočníci mohou načíst přihlašovací stránku a snažit se vaše rozhraní API zahlcením útoky DoS a Cripple vaší aplikace. Pokud například používáte CAPTCHA, můžete se přihlásit pomocí služby Flow.

- Pomocí [konektorů rozhraní API integrovaných uživatelských toků přihlášení](https://docs.microsoft.com/azure/active-directory-b2c/api-connectors-overview) , kdykoli je to možné, můžete integrovat s webovými rozhraními API, a to buď po přihlášení pomocí zprostředkovatele identity, nebo před vytvořením uživatele. Vzhledem k tomu, že toky uživatelů jsou již rozsáhle testovány, je pravděpodobně nutné provádět testování na úrovni toku uživatele, výkon nebo škálování. Stále potřebujete testovat aplikace pro funkce, výkon a škálování.

- [Technické profily](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) rozhraní API služby Azure AD RESTFul neposkytují žádné chování při ukládání do mezipaměti. Místo toho profil rozhraní API RESTFul implementuje logiku opakování a zabudovaný časový limit v zásadách.

- V případě rozhraní API, která potřebují zapisovat data, zařadit do fronty úlohu, aby byly takové úkoly spouštěny pracovním procesem na pozadí. Služby, jako jsou [fronty Azure](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction) , se dají použít. Díky tomu bude rozhraní API efektivně zvyšovat výkon spuštění zásad.  

## <a name="api-error-handling"></a>Zpracování chyb rozhraní API

V případě, že jsou rozhraní API živá mimo Azure AD B2C systém, je nutné mít v rámci technického profilu správné zpracování chyb. Ujistěte se, že je koncový uživatel správně informován a že aplikace může řešit chyby bez problémů.

### <a name="how-to-gracefully-handle-api-errors"></a>Postup řádného zpracování chyb rozhraní API

- Rozhraní API může z různých důvodů neúspěšné, aby vaše aplikace byla odolná vůči takovým selháním. [Vrátí chybovou zprávu 4XX http](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#returning-validation-error-message) , pokud rozhraní API nemůže požadavek dokončit. V zásadách Azure AD B2C se pokuste řádně zpracovat nedostupnost rozhraní API a pravděpodobně vykreslit omezené prostředí.

- [Řádné zpracování přechodných chyb](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#error-handling). Profil rozhraní API RESTFul umožňuje konfigurovat chybové zprávy pro různé [přepínací moduly okruhů](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker).

- Proaktivně Sledujte a používejte průběžnou integraci/průběžné doručování (CICD), otočte přihlašovací údaje k rozhraní API, jako jsou hesla a certifikáty používané [modulem Technical Profile](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile).

## <a name="api-management---best-practices"></a>API Management – osvědčené postupy

Když nasadíte rozhraní REST API a nakonfigurujete technický profil RESTful, pomůžou vám Doporučené osvědčené postupy při neprovádění běžných chyb a věcí, které jsou v dobrém důsledku.

### <a name="how-to-manage-apis"></a>Správa rozhraní API

- API Management (APIM) publikuje, spravuje a analyzuje vaše rozhraní API. APIM také zpracovává ověřování, aby poskytovala zabezpečený přístup k back-end službám a mikroslužbám. Pomocí brány rozhraní API můžete škálovat nasazení rozhraní API, ukládání do mezipaměti a vyrovnávání zatížení.

- Doporučení je získání správného tokenu na začátku cesty uživatele místo volání několikrát pro každé rozhraní API a [zabezpečení rozhraní API služby Azure APIM](https://docs.microsoft.com/azure/active-directory-b2c/secure-api-management?tabs=app-reg-ga).

## <a name="next-steps"></a>Další kroky

- [Prostředky odolnosti pro vývojáře Azure AD B2C](resilience-b2c.md)
  - [Odolné prostředí koncového uživatele](resilient-end-user-experience.md)
  - [Zajištění odolnosti pomocí osvědčených postupů pro vývojáře](resilience-b2c-developer-best-practices.md)
  - [Odolnost prostřednictvím monitorování a analýzy](resilience-with-monitoring-alerting.md)
- [Odolnost sestavení v infrastruktuře ověřování](resilience-in-infrastructure.md)
- [Zvýšení odolnosti při ověřování a autorizaci ve vašich aplikacích](resilience-app-development-overview.md)
