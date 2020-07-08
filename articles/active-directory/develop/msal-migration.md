---
title: Migrace do knihovny Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Přečtěte si o rozdílech mezi Microsoft Authentication Library (MSAL) a Azure AD Authentication Library (ADAL) a postupem migrace na MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 52a4a7131c85231107a2a23a1916016776b219fd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367423"
---
# <a name="migrate-applications-to-microsoft-authentication-library-msal"></a>Migrace aplikací do knihovny Microsoft Authentication Library (MSAL)

Mnoho vývojářů sestavilo a nasadilo aplikace pomocí knihovny Azure Active Directory Authentication Library (ADAL). Pro ověřování a autorizaci entit Azure AD teď doporučujeme použít Microsoft Authentication Library (MSAL).

Pomocí MSAL místo ADAL:

- Můžete ověřit širší sadu identit:
  - Identity Azure AD
  - Účty Microsoft
  - Sociální a místní účty pomocí Azure AD B2C
- Vaši uživatelé získají nejlepší možnosti jednotného přihlašování.
- Vaše aplikace může povolit přírůstkový souhlas.
- Podpora podmíněného přístupu je jednodušší.
- Přináší vám výhody inovace. Vzhledem k tomu, že všechny snahy Microsoftu jsou teď zaměřené na MSAL, v ADAL se neimplementují žádné nové funkce.

**MSAL je teď doporučenou knihovnou ověřování pro použití s platformou Microsoft Identity**.

## <a name="migration-guidance"></a>Pokyny k migraci

Následující články vám pomůžou s migrací na MSAL:

- [Migrace na MSAL.Android](migrate-android-adal-msal.md)
- [Migrace na MSAL.iOS/macOS](migrate-objc-adal-msal.md)
- [Migrace na MSAL v Javě](migrate-adal-msal-java.md)
- [Migrace na MSAL.js](msal-compare-msal-js-and-adal-js.md)
- [Migrace na MSAL.NET](msal-net-migration.md)
- [Migrace na MSAL pro Python](migrate-python-adal-msal.md)
- [Migrace aplikací pro Xamarin pomocí zprostředkovatelů do MSAL.NET](msal-net-migration-ios-broker.md)

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

__Otázka: je ADAL zastaralá?__  
Odpověď: Ano. Od 30. června 2020 už nebudeme přidávat nové funkce do ADAL. Do 30. června 2022 budeme dál přidávat důležité opravy zabezpečení do knihovny ADAL.

__Otázka: Návody víte, které moje aplikace používají ADAL?__  
O: Pokud máte zdrojový kód aplikace, můžete odkazovat na výše uvedené příručky migrace, které vám pomůžou určit, kterou knihovnu aplikace používá a jak ji migrovat do MSAL. Pokud nemáte přístup ke zdrojovému kódu vaší aplikace, můžete [otevřít žádost o podporu](developer-support-help-options.md#open-a-support-request) a získat tak seznam registrovaných aplikací a knihovny, které aplikace používá.

__Otázka: budou mé stávající aplikace ADAL nadále fungovat?__  
Odpověď: vaše existující aplikace budou i nadále fungovat bez úprav. Pokud plánujete, že byste je měli po dobu od 30. června 2022, měli byste zvážit jejich aktualizaci na MSAL, aby byly zabezpečené, ale migrace na MSAL není nutná k zachování stávajících funkcí.

__Otázka: Proč investovat do MSAL?__  
Odpověď: MSAL obsahuje nové funkce, které nejsou v ADAL, včetně přírůstkového souhlasu, jednotné přihlašování a správy mezipaměti tokenů. I na rozdíl od ADAL bude MSAL nadále získávat opravy zabezpečení i po 30. června 2022. [Další informace](msal-overview.md).

__Otázka: budete vydávat nástroj, který mi pomůže přesunout moje aplikace z ADAL do MSAL?__  
Odpověď: Ne. Rozdíly mezi knihovnami by vyžadovaly vyhradit prostředky pro vývoj a údržbu nástroje, který by jinak strávil vylepšení MSAL. Představme si ale předchozí sadu průvodců migrací, které vám pomůžou udělat požadované změny v aplikaci.

__Otázka: jak MSAL pracuje s AD FS?__  
Odpověď: MSAL.NET podporuje určité scénáře ověřování proti AD FS 2019. Pokud vaše aplikace potřebuje získat tokeny přímo z dřívější verze AD FS, měli byste zůstat na ADAL. [Další informace](msal-net-adfs-support.md).

__Otázka: Návody získat pomoc při migraci své aplikace?__  
Odpověď: viz část [pokyny k migraci](#migration-guidance) v tomto článku. Pokud po přečtení příručky pro platformu vaší aplikace máte další otázky, můžete na Stack Overflow odeslat značku `[adal-deprecation]` nebo otevřít problém v úložišti GitHub knihovny. Odkazy na úložiště každé knihovny najdete v části [jazyky a architektury](msal-overview.md#languages-and-frameworks) v článku MSAL Overview.

## <a name="next-steps"></a>Další kroky

- [Aktualizujte své aplikace tak, aby používaly knihovnu Microsoft Authentication Library a Microsoft Graph API.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
- [Další informace o platformě Microsoft Identity Platform (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [Projděte si ukázky kódu MSAL](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code)
