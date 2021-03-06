---
title: Omezení služby
titleSuffix: Azure Digital Twins
description: Graf znázorňující omezení služby digitálních vláken Azure
author: baanders
ms.author: baanders
ms.date: 04/08/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 651922837b2193f7a8387c4dec6a1e20b84a41a5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728013"
---
# <a name="azure-digital-twins-service-limits"></a>Omezení služby digitálních vláken Azure

Toto jsou omezení služby digitálních vláken Azure.

> [!NOTE]
> Některé oblasti této služby mají nastavitelná omezení. To je znázorněno v tabulkách níže a *upravitelný sloupec?* . Pokud je možné limit upravit, je hodnota *seřiditelná?* *Ano*.
>
> Pokud vaše firma vyžaduje zvýšení limitu nebo kvóty nad rámec výchozího omezení, můžete požádat o další prostředky [otevřením lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-by-type"></a>Omezení podle typu

[!INCLUDE [Azure Digital Twins limits](../../includes/digital-twins-limits.md)]

## <a name="working-with-limits"></a>Práce s omezeními

Po dosažení limitu služba omezuje další požadavky. Výsledkem bude chybná odpověď 429 od těchto požadavků.

Pokud to chcete spravovat, tady je několik doporučení pro práci s omezeními.
* **Použijte logiku opakování.** Sady [SDK digitálních vláken v Azure](how-to-use-apis-sdks.md) implementují logiku opakování pro neúspěšné žádosti, takže pokud pracujete s poskytnutou sadou SDK, je tato operace už integrovaná. V opačném případě zvažte implementaci logiky opakování ve vaší vlastní aplikaci. Služba pošle zpět `Retry-After` hlavičku v reakci na selhání, kterou můžete použít k určení, jak dlouho se má čekat před opakováním.
* **Používejte prahové hodnoty a oznámení pro upozornění na omezení přístupu.** Některá omezení služby pro digitální vlákna Azure mají odpovídající [metriky](troubleshoot-metrics.md) , které lze použít ke sledování využití v těchto oblastech. Chcete-li nakonfigurovat prahové hodnoty a nastavit výstrahu u jakékoli metriky při přístupu k prahové hodnotě, přečtěte si pokyny v tématu [*řešení potíží: nastavení výstrah*](troubleshoot-alerts.md). Chcete-li nastavit oznámení pro jiná omezení, kde nejsou k dispozici metriky, zvažte implementaci této logiky ve vlastním kódu aplikace.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o aktuálním vydání digitálních vláken Azure v přehledu služby:
* [*Přehled: co je Azure Digital revláken?*](overview.md)
