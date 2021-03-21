---
title: Odolné prostředí koncového uživatele pomocí Azure AD B2C | Microsoft Docs
description: Metody pro sestavování odolnosti při činnostech koncového uživatele pomocí Azure AD B2C
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
ms.openlocfilehash: f6896a812ec173994a1299a28ff2e99a0f351391
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724437"
---
# <a name="resilient-end-user-experience"></a>Odolné prostředí koncového uživatele

Prostředí pro registraci a přihlášení koncového uživatele se skládá z následujících prvků:

- Rozhraní, se kterými uživatel pracuje – například CSS, HTML a JavaScript

- Uživatelské toky a vlastní zásady, které vytvoříte – například registrace, přihlašování a úpravy profilu

- Zprostředkovatelé identity (zprostředkovatelů identity) pro vaši aplikaci – například uživatelské jméno/heslo místního účtu, Outlook, Facebook a Google

![Obrázek ukazuje komponenty prostředí koncových uživatelů](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Výběr mezi uživatelským tokem a vlastní zásadou  

Při nastavování nejběžnějších úloh identity vám Azure AD B2C poskytuje předdefinované konfigurovatelné [uživatelské toky](../../active-directory-b2c/user-flow-overview.md). Můžete si také vytvořit vlastní [zásady](../../active-directory-b2c/custom-policy-overview.md), které vám nabídnou maximální flexibilitu. Doporučuje se ale používat vlastní zásady jenom pro řešení složitých scénářů.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Jak se rozhodnout mezi uživatelským tokem a vlastní zásadou

Vyberte předdefinované toky uživatelů, pokud je vaše obchodní požadavky může splnit. Vzhledem k tomu, že je společnost Microsoft rozsáhle testována, můžete minimalizovat testování potřebné k ověření funkčnosti, výkonu nebo škálování těchto uživatelských toků identity na úrovni zásad. Stále potřebujete testovat aplikace pro funkce, výkon a škálování.

Měli byste [zvolit vlastní zásady](../../active-directory-b2c/custom-policy-get-started.md) z důvodu požadavků vaší firmy. Ujistěte se, že provádíte testování na úrovni zásad pro funkční, výkon nebo škálování kromě testování na úrovni aplikace.

Přečtěte si článek, který [porovnává uživatelské toky a vlastní zásady](../../active-directory-b2c/custom-policy-overview.md#comparing-user-flows-and-custom-policies) , které vám pomůžou při rozhodování.

## <a name="choose-multiple-idps"></a>Zvolit několik zprostředkovatelů identity

Při použití [externího poskytovatele identity](../../active-directory-b2c/technical-overview.md#external-identity-providers) , jako je Facebook, se ujistěte, že máte záložní plán pro případ, že externí poskytovatel nebude k dispozici.

### <a name="how-to-set-up-multiple-idps"></a>Jak nastavit více zprostředkovatelů identity

Jako součást procesu registrace externího zprostředkovatele identity zahrňte ověřenou deklaraci identity, jako je mobilní číslo nebo e-mailová adresa uživatele. Potvrďte ověřené deklarace identity do příslušné instance Azure AD B2C adresáře. Pokud externí poskytovatel není k dispozici, vraťte se k ověřené deklaraci identity a vraťte se na telefonní číslo jako metodu ověřování. Další možností je odeslat uživateli jednorázové heslo, které umožní uživateli přihlásit se.

 Při [sestavování alternativních ověřovacích cest](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter)použijte následující postup:

 1. Nakonfigurujte zásady registrace tak, aby povolovaly registraci pomocí místního účtu a externích zprostředkovatelů identity.

 2. Nakonfigurujte zásady profilu tak, aby uživatelům umožnily [propojit jinou identitu se svým účtem](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) po přihlášení.

 3. Upozorněte a Umožněte uživatelům [Přejít na alternativní IDP](../../active-directory-b2c/customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) během výpadku.

## <a name="availability-of-multi-factor-authentication"></a>Dostupnost aplikace Multi-Factor Authentication

Pokud používáte [telefonní službu pro vícefaktorové ověřování (MFA)](../../active-directory-b2c/phone-authentication.md), nezapomeňte zvážit alternativního poskytovatele služeb. Místní výpovědi nebo poskytovatel telefonních služeb může ve své službě dojít k přerušení.

### <a name="how-to-choose-an-alternate-mfa"></a>Jak zvolit alternativní vícefaktorové ověřování  

Služba Azure AD B2C používá integrovanýho poskytovatele MFA založeného na telefonu, aby mohla poskytovat jednorázová hesla založená na čase (OTPs). Má formu hlasového hovoru a textové zprávy pro předem registrované telefonní číslo uživatele. Pro různé scénáře jsou k dispozici následující alternativní metody:

Při použití **toků uživatelů** existují dvě metody pro sestavování odolnosti:

- **Změnit konfiguraci toku uživatele**: při zjištění výpadku při DORUČOVÁNí jednorázového hesla změňte metodu doručení jednorázovým heslem z telefonu na e-mail a znovu nasaďte tok uživatelů a aplikace zůstane beze změny.

![snímek obrazovky zobrazující registraci přihlášení](media/resilient-end-user-experiences/create-sign-in.png)

- **Změnit aplikace**: pro každou úlohu identity, jako je registrace a přihlášení, definujte dvě sady uživatelských toků. Nakonfigurujte nejdřív nastavení na používání telefonického ověřování jednorázovým heslem a druhý na e-mailové jednorázové heslo. Při zjišťování výpadku při doručování jednorázovým heslem se aplikace mění a znovu nasadí, aby se přepnuly od první sady uživatelských toků k druhému, takže uživatelé nezměnili toky.  

Při použití **vlastních zásad** existují čtyři metody pro sestavování odolnosti. Následující seznam je v pořadí složitosti a bude nutné znovu nasadit aktualizované zásady.

- **Povolení výběru uživatele na základě telefonického nebo e-mailu pomocí jednorázového** hesla: vystavení obou možností uživatelům a možnost, aby uživatelé mohli sami vybrat jednu z možností. Není nutné provádět změny v zásadách ani v aplikacích.

- **Dynamické přepínání jednorázového hesla a jednorázového hesla pomocí e-mailu**: při registraci shromažďovat informace o telefonu i e-mailu. Předem definujte vlastní zásady, aby se podmíněně přepnuly při přerušení telefonického přenosu, od telefonu přes e-mailové doručení pomocí jednorázového hesla. Není nutné provádět změny v zásadách ani v aplikacích.

- **Použití ověřovací aplikace**: aktualizujte vlastní zásady, aby používaly [ověřovací aplikaci](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Pokud je vaše normální MFA založené na telefonickém nebo e-mailu, pak vlastní zásady znovu nasaďte a přepněte na používání ověřovací aplikace.

>[!Note]
>Uživatelé musí při registraci nakonfigurovat integraci aplikace ověřovatele.

- **Použití bezpečnostních otázek**: Pokud žádná z výše uvedených metod není platná, implementujte bezpečnostní otázky jako zálohu. Nastavte bezpečnostní otázky pro uživatele během připojování nebo úprav profilu a uložte odpovědi do jiné databáze mimo adresář. Tato metoda nesplňuje požadavek MFA "něco, co máte", například telefon, ale nabízí sekundární "něco, co znáte".

## <a name="use-a-content-delivery-network"></a>Použití sítě Content Delivery Network

Sítě pro doručování obsahu (sítě CDN) jsou lépe výkonné a méně nákladné než úložiště objektů BLOB pro ukládání vlastního uživatelského rozhraní uživatelského toku. Obsah webové stránky se doručí rychleji z geograficky distribuované sítě vysoce dostupných serverů.  

Pravidelně otestujte dostupnost sítě CDN a výkon distribuce obsahu prostřednictvím uceleného scénáře a zátěžového testování. Pokud plánujete, že máte v úmyslu nadcházející nárůst z důvodu propagačního nebo nedovoleného provozu, pročtěte si odhady zátěžového testování.
  
## <a name="next-steps"></a>Další kroky

- [Prostředky odolnosti pro vývojáře Azure AD B2C](resilience-b2c.md)
  
  - [Odolná rozhraní s externími procesy](resilient-external-processes.md)
  - [Zajištění odolnosti pomocí osvědčených postupů pro vývojáře](resilience-b2c-developer-best-practices.md)
  - [Odolnost prostřednictvím monitorování a analýzy](resilience-with-monitoring-alerting.md)
- [Odolnost sestavení v infrastruktuře ověřování](resilience-in-infrastructure.md)
- [Zvýšení odolnosti při ověřování a autorizaci ve vašich aplikacích](resilience-app-development-overview.md)