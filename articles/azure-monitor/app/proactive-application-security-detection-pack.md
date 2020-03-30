---
title: Balíček pro zjišťování zabezpečení s přehledy aplikací Azure
description: Sledujte aplikace pomocí Azure Application Insights a Smart Detection, pokud hledáte potenciální problémy se zabezpečením.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: b23ada6fe4596a2eee242cc9145789521caf697f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669722"
---
# <a name="application-security-detection-pack-preview"></a>Balíček pro detekci zabezpečení aplikací (preview)

Application Insights automaticky analyzuje telemetrická data generovaná vaší aplikací a detekuje potenciální problémy se zabezpečením. Tato funkce umožňuje identifikovat potenciální problémy se zabezpečením a zpracovat je opravou aplikace nebo přijetím nezbytných bezpečnostních opatření.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [konfigurace aplikace pro odesílání telemetrie](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy dostanu tento typ inteligentního oznámení o detekci?
Jsou zjištěny tři typy problémů se zabezpečením:
1. Nezabezpečený přístup k adrese URL: adresa URL v aplikaci je přístupná prostřednictvím protokolu HTTP i HTTPS. Adresa URL, která přijímá požadavky HTTPS, by obvykle neměla přijímat požadavky HTTP. To může znamenat chybu nebo problém se zabezpečením ve vaší aplikaci.
2. Nezabezpečená forma: formulář (nebo jiný požadavek "POST") v aplikaci používá http místo PROTOKOLU HTTPS. Použití protokolu HTTP může ohrozit uživatelská data odeslaná formulářem.
3. Podezřelá aktivita uživatele: aplikace je přístupná z více zemí nebo oblastí stejným uživatelem přibližně ve stejnou dobu. Například stejný uživatel přistupoval k aplikaci ze Španělska a Spojených států ve stejné hodině. Toto zjištění označuje potenciálně škodlivý pokus o přístup k vaší aplikaci.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Má moje aplikace určitě bezpečnostní problém?
Ne, oznámení neznamená, že vaše aplikace má určitě problém se zabezpečením. Zjištění některého z výše uvedených scénářů může v mnoha případech znamenat problém se zabezpečením. Detekce však může mít přirozené obchodní odůvodnění a může být ignorována.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Jak opravím zjišťování "Nezabezpečený přístup k adrese URL"?
1. **Třídění.** Oznámení poskytuje počet uživatelů, kteří přistupovali k nezabezpečeným adresám URL, a adresu URL, která byla nejvíce ovlivněna nezabezpečeným přístupem. To vám může pomoci přiřadit prioritu problému.
2. **Rozsah.** Jaké procento uživatelů přistupovalo k nezabezpečeným adresám URL? Kolik adres URL bylo ovlivněno? Tyto informace lze získat z oznámení.
3. **Diagnostikovat.** Zjišťování poskytuje seznam nezabezpečených požadavků a seznamy adres URL a uživatelů, kterých se to týká, které vám pomohou problém dále diagnostikovat.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Jak lze opravit detekci nezabezpečeného formuláře?
1. **Třídění.** Oznámení poskytuje počet nezabezpečených formulářů a počet uživatelů, jejichž data byla potenciálně ohrožena. To vám může pomoci přiřadit prioritu problému.
2. **Rozsah.** Která forma byla zapojena do největšího počtu nezabezpečených přenosů a jaká je distribuce nezabezpečených přenosů v průběhu času? Tyto informace lze získat z oznámení.
3. **Diagnostikovat.** Detekce poskytuje seznam nezabezpečených formulářů a rozpis počtu nezabezpečených přenosů pro každý formulář, které vám pomohou dále diagnostikovat problém.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Jak opravím detekci "Podezřelá aktivita uživatele"?
1. **Třídění.** Oznámení poskytuje počet různých uživatelů, kteří vykazovali podezřelé chování. To vám může pomoci přiřadit prioritu problému.
2. **Rozsah.** Ze kterých zemí nebo oblastí podezřelé žádosti pocházejí? Který uživatel byl nejvíce podezřelý? Tyto informace lze získat z oznámení.
3. **Diagnostikovat.** Zjišťování poskytuje seznam podezřelých uživatelů a seznam zemí nebo oblastí pro každého uživatele, které vám pomohou dále diagnostikovat problém.
