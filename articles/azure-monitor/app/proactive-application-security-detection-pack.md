---
title: Sada Security Detection Pack s Azure Application Insights
description: Monitorujte aplikaci s využitím Azure Application Insights a inteligentní detekci potenciálních problémů se zabezpečením.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 1ed304e903bb50591e61e294b6701f8268f9d8e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87081623"
---
# <a name="application-security-detection-pack-preview"></a>Sada rozpoznávání zabezpečení aplikace (Preview)

Application Insights automaticky analyzuje telemetrii vygenerovanou vaší aplikací a detekuje potenciální problémy se zabezpečením. Tato funkce vám umožňuje identifikovat potenciální problémy zabezpečení a zpracovat je pomocí opravy aplikace nebo provedením potřebných bezpečnostních opatření.

Tato funkce nevyžaduje žádné speciální nastavení, jiné než [Konfigurace vaší aplikace pro posílání telemetrie](./usage-overview.md).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Byly zjištěny tři typy problémů se zabezpečením:
1. Nezabezpečený přístup k adrese URL: adresa URL v aplikaci se přistupuje přes HTTP i HTTPS. Adresa URL, která přijímá požadavky HTTPS, by obvykle neměla přijímat požadavky HTTP. To může znamenat chybu nebo chybu zabezpečení v aplikaci.
2. Nezabezpečený formulář: formulář (nebo jiná žádost "POST") v aplikaci používá protokol HTTP místo protokolu HTTPS. Použití protokolu HTTP může ohrozit uživatelská data odesílaná formulářem.
3. Podezřelá aktivita uživatele: aplikace je v současné době dostupná z více zemí nebo oblastí stejným uživatelem. Například stejný uživatel navštívil aplikaci ze Španělska a USA do stejné hodiny. Toto zjištění indikuje potenciálně škodlivý pokus o přístup k vaší aplikaci.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Má moje aplikace konečně problémy se zabezpečením?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problémy se zabezpečením. Detekce kteréhokoli z výše uvedených scénářů může v mnoha případech znamenat problém se zabezpečením. Detekce ale může mít přirozený důvod na podnikání a může se ignorovat.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Návody opravit detekci nezabezpečeného přístupu URL?
1. **Třídění.** Oznámení poskytuje počet uživatelů, kteří získali nezabezpečené adresy URL, a adresu URL, která byla ovlivněna nezabezpečeným přístupem. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Jaké procento uživatelů získalo nezabezpečené adresy URL? Kolik adres URL bylo ovlivněno? Tyto informace lze získat z oznámení.
3. **Diagnóz.** V této části najdete seznam nezabezpečených požadavků a seznam adres URL a uživatelů, které byly zasaženy, abyste mohli problém dále diagnostikovat.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Návody opravit rozpoznávání "nezabezpečeného tvaru"?
1. **Třídění.** Oznámení poskytuje počet nezabezpečených formulářů a počet uživatelů, jejichž data byla potenciálně ohrožena. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Který formulář byl zahrnut do nejvyššího počtu nezabezpečených přenosů a jaká je distribuce nezabezpečených přenosů v čase? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Rozpoznávání poskytuje seznam nezabezpečených formulářů a rozpis počtu nezabezpečených přenosů pro jednotlivé formuláře, které vám pomůžou s další diagnostikou problému.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Návody opravit detekci podezřelých aktivit uživatelů?
1. **Třídění.** Oznámení poskytuje počet různých uživatelů, kteří vykazují podezřelé chování. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Ze kterých zemí nebo oblastí vznikly podezřelé požadavky? Který uživatel byl nejvíc podezřelý? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Rozpoznávání poskytuje seznam podezřelých uživatelů a seznam zemí/oblastí pro každého uživatele, které vám pomohou s dalším diagnostikou problému.
