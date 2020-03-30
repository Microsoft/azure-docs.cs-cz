---
title: Abnormální nárůst objemu výjimek – Azure Application Insights
description: Monitorujte výjimky aplikací pomocí inteligentního zjišťování v Azure Application Insights pro neobvyklé vzory ve svazku výjimek.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: a08fae4774a8afb9959f55ea3196cd1a45c33439
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671762"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Abnormální nárůst objemu výjimek (náhled)

Application Insights automaticky analyzuje výjimky vyzývané ve vaší aplikaci a může vás varovat před neobvyklé vzory v telemetrii výjimek.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [konfigurace hlášení výjimek](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) pro vaši aplikaci. Je aktivní, když vaše aplikace generuje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy dostanu tento typ inteligentního oznámení o detekci?
Tento typ oznámení můžete získat, pokud vaše aplikace vykazuje abnormální nárůst počtu výjimek určitého typu během dne ve srovnání se směrným plánem vypočítaným za předchozích sedm dní.
Algoritmy strojového učení se používají k detekci nárůstu počtu výjimek, přičemž se bere v úvahu přirozený nárůst využití aplikace.

## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace určitě nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má určitě problém. Přestože nadměrný počet výjimek obvykle označuje problém s aplikací, tyto výjimky mohou být neškodné a správně zpracovány vaší aplikací.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení obsahují diagnostické informace pro podporu v procesu diagnostiky:
1. **Třídění.** Oznámení ukazuje, kolik uživatelů nebo kolik požadavků jsou ovlivněny. To vám může pomoci přiřadit prioritu problému.
2. **Rozsah.** Je problém ovlivňuje veškerý provoz, nebo jen nějaký provoz? Tyto informace lze získat z oznámení.
3. **Diagnostikovat.** Detekce obsahuje informace o metodě, ze které byla vyvolána výjimka, stejně jako typ výjimky. Můžete také použít související položky a sestavy, které odkazují na podpůrné informace, které vám pomohou problém dále diagnostikovat.
