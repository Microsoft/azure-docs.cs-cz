---
title: 'Nejčastější dotazy: SRE a kódování | Microsoft Docs'
titleSuffix: Azure
description: 'Nejčastější dotazy: princip vztahu mezi SRE a kódováním'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: b8865fdd53f4947b17a3621a128fc83f3d93d3e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089067"
---
# <a name="frequently-asked-questions-do-i-need-to-know-how-to-code-to-get-involved-with-sre"></a>Nejčastější dotazy: Potřebuji vědět, jak se má kód zapojit do SRE?

Vzhledem k tomu, že se jednotlivci domnívají, že jsou zapojeni do SRE a týmy se domnívají o zavedení v SREch postupech, často se jedná o společný dotaz, který se zobrazí. potřebujete znát způsob používání kódu? "

Krátká odpověď: Ano. 

Ale úplná odpověď je trochu větší odlišit. Pojďme se podívat na tři místa, kde se kódování přihlašuje v rámci spolehlivého vývoje v lokalitě a úroveň odbornosti kódování požadovaná pro každý z nich. Tento seznam není úplný, ale tyto scénáře jsou některé z nejběžnějších případů použití.

## <a name="scenario-1-removing-toil-through-automation"></a>Scénář 1: odebrání toil prostřednictvím automatizace

Technici pro spolehlivost webů a ostatní, kteří používají SRE postupy, kdykoli je to možné, k odebrání toil. "Toil" znamená specifickou věc v SRE. Toil odkazuje na provozní práci prováděnou člověkem, který má určité charakteristiky. Lopota nepřináší dlouhodobou hodnotu, která by přinášela uspokojení. Žádným smysluplným způsobem neposouvá službu vpřed. Často je to repetitivní a převážně manuální činnost (i když by mohla být automatizovaná). S tím, jak služba nebo systémy časem rostou, zvýší se pravděpodobně poměrně s tím také počet požadavků na daný systém a budou vyžadovat ještě více manuální práce.

Pokud například služba vyžaduje, aby tým SRE resetoval každý týden, nebo aby se znovu zřídily nové účty a místo na disku, nebo se opakovaně restartuje ručně – jedná se o provozní zatížení, které je toil. Provádění těchto úkonů službu dlouhodobě trvale nijak nezlepšuje. Tyto úkony budou pravděpodobně muset být opakovány stále dokola.

SRE nesnáší lopotu. Usiluje o její eliminaci, kdykoli je to možné a vhodné. Jde o jedna z oblastí, kde v SRE vstupuje do hry automatizace. Pokud je možné tyto požadavky zpracovat automaticky, uvolňuje tým, aby fungoval na více faktech a ovlivněných věcí.

*Odbornosti kódování*: automatizace vyžaduje určité znalosti kódování, ale nemusí vyžadovat úplné dovednosti v softwarovém inženýrství. Pokud můžete psát malé skripty (třeba v PowerShellu nebo prostředí Bourne), nebo i když vytvoříte [https://docs.microsoft.com/azure/logic-apps/logic-apps-overview](Azure Logic app) s zlomek libovolným kódem, tato aplikace může i nadále eliminovat toil.

## <a name="scenario-2-control-through-apisdomain-specific-languages-dslstemplates"></a>Scénář 2: řízení prostřednictvím rozhraní API/DSL (Domain Specific Languages)/Templates

I když to není bezpodmínečně nutné pro práci SRE, je možné řídit prostředí prostřednictvím rozhraní API, DSL a šablon (hlavně cloudová prostředí) umožňují SREs škálovat svou práci. Zřizování a rušení zřizování infrastruktury, konfigurace monitorování a integrace několika služeb bude mnohem efektivnější prostřednictvím kódování.

*Zkušenosti s kódováním*: jako v předchozím scénáři je potřeba některé odbornosti kódu, ale nemusí vyžadovat úplné dovednosti v softwarovém inženýrství. Kromě skriptů a aplikací logiky, které jsou zmíněné dřív, se [https://docs.microsoft.com/azure/azure-resource-manager/templates/overview](Azure Resource Manager Templates) dají použít i s minimálním prostředím pro kódování.

## <a name="scenario-3-fixing-the-code"></a>Scénář 3: Oprava kódu

Technici pro spolehlivost sítě hledají lepší spolehlivost systému. Tento cíl někdy vyžaduje prozkoumá do zdrojového kódu systému, určení problému a často přispívá k opravě zpět do základu kódu. I když se úroveň sofistikovanější této práce může výrazně lišit v závislosti na situaci, odbornost v kódování je v těchto případech v takovém případě přesně splněná.

*Zkušenosti s kódováním*: v tomto scénáři se často vyžadují kompletní odborné znalosti k softwarovému inženýrství.


## <a name="next-steps"></a>Další kroky

Uvažujete o tom, jak se dozvědět víc o spolehlivosti webu a práci s nízkým kódem? Podívejte se na náš [technický portál pro spolehlivost](../index.yml)a dokumentaci k produktu.
