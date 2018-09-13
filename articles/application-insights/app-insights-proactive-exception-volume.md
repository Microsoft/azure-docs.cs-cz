---
title: Inteligentní zjišťování – neobvyklý nárůst počtu výjimek ve službě Azure Application Insights | Dokumentace Microsoftu
description: Monitorování výjimky aplikace pomocí Azure Application Insights pro neobvyklé vzory počtu výjimek.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: mbullwin
ms.openlocfilehash: 898cc0935051f65cb0f2977c7d90e998ec32cdd3
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642924"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Neobvyklý nárůst počtu výjimek (preview)

Application Insights automaticky analyzuje výjimky vyvolané ve vaší aplikaci a může vás upozorní na neobvyklé vzory ve vaší telemetrie výjimek.

Tato funkce vyžaduje žádné speciální instalační program, než [konfigurace generování sestav výjimky](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-exceptions#set-up-exception-reporting) pro vaši aplikaci. Je aktivní, když vaše aplikace generuje dostatek telemetrie výjimek.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Při obdržení tohoto typu upozornění inteligentního zjišťování
Tento typ upozornění se mohou zobrazovat, pokud vaše aplikace je vykazujících neobvykle zvýší počet výjimek určitého typu během dne, ve srovnání s směrný plán počítá v průběhu posledních sedmi dnů.
Algoritmy strojového učení se používají pro zjišťování nárůst počtu výjimek přihlédnutím přirozené nárůst využití aplikace.

## <a name="does-my-app-definitely-have-a-problem"></a>Moje aplikace jednoznačně má problém?
Ne, oznámení neznamená, že vaše aplikace jednoznačně došlo k problému. I když nadměrný počet výjimek obvykle znamená problém s aplikací, tyto výjimky může být neškodné a zpracováván správně vaší aplikace.

## <a name="how-do-i-fix-it"></a>Jak ho mám opravit?
Oznámení zahrnují diagnostické informace pro podporu v procesu diagnostiky:
1. **Třídění.** Oznámení se dozvíte, kolik uživatelů nebo kolik žádostí se to týká. Můžete přiřadit prioritu problému.
2. **Obor.** Tento problém ovlivňuje veškerý provoz, nebo jen některé operace? Tyto informace můžete získat z oznámení.
3. **Diagnostika.** Detekce obsahuje informace o metodě, ze kterého byla vyvolána výjimka, a také typ výjimky. Můžete použít také související položky a sestavy propojení podpůrné informace, abychom vám další diagnostice problému.