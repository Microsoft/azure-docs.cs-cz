---
title: Abnormální zvýšení objemu výjimky – Azure Application Insights
description: Monitorování výjimek aplikací pomocí inteligentní detekce v Azure Application Insights pro neobvyklé vzorce ve svazku výjimek.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499422"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Neobvyklé zvýšení objemu výjimek (Preview)

Application Insights automaticky analyzuje výjimky vyvolané ve vaší aplikaci a může upozorňovat na neobvyklé vzory v telemetrii výjimek.

Tato funkce nevyžaduje žádné speciální nastavení, kromě [Konfigurace generování sestav výjimek](./asp-net-exceptions.md#set-up-exception-reporting) pro vaši aplikaci. Je aktivní, když vaše aplikace vygeneruje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kdy získám tento typ oznámení inteligentního zjišťování?
Tento typ oznámení se může zobrazit, pokud se v aplikaci v porovnání se směrným plánem vypočítaným během posledních sedmi dnů objeví neobvyklé zvýšení počtu výjimek konkrétního typu.
Algoritmy strojového učení se používají k detekci nárůstu počtu výjimek a přitom berou v úvahu přirozený nárůst využití aplikace.

## <a name="does-my-app-definitely-have-a-problem"></a>Má moje aplikace konečně nějaký problém?
Ne, oznámení neznamená, že vaše aplikace má jednoznačně problém. I když nadměrný počet výjimek obvykle označuje problém s aplikací, tyto výjimky mohou být neškodné a správně zpracovány vaší aplikací.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu procesu diagnostiky:
1. **Třídění.** V tomto oznámení se dozvíte, kolik uživatelů nebo kolik požadavků je ovlivněno. To vám může přispět k přiřazení priority k problému.
2. **Oboru.** Má tento problém vliv na veškerý provoz nebo jenom nějaké operace? Tyto informace lze získat z oznámení.
3. **Diagnóz.** Detekce obsahuje informace o metodě, ze které byla vyvolána výjimka, a také o typu výjimky. Můžete také použít související položky a sestavy odkazující na podpůrné informace, které vám pomohou s dalším diagnostikou problému.
