---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793447"
---
Chcete-li odhadnout přesnější náklady na spotřebu, vezměte v úvahu možný počet zpráv nebo událostí, které mohou být doručeny v jednom dni, nikoli na základě výpočtů pouze v intervalu dotazování. Když událost nebo zpráva splňuje kritéria triggeru, mnoho triggerů se okamžitě pokusí přečíst všechny ostatní čekající události nebo zprávy, které splňují kritéria. To znamená, že i když vyberete delší interval dotazování, Trigger se aktivuje na základě počtu čekajících událostí nebo zpráv, které jsou způsobilé pro spouštění pracovních postupů. Mezi triggery, které následují toto chování, patří Azure Service Bus a Azure Event hub.

Předpokládejme například, že jste nastavili aktivační událost, která každý den kontroluje koncový bod. Když aktivační událost zkontroluje koncový bod a najde 15 událostí, které splňují kritéria, Trigger se aktivuje a spustí odpovídající Workflow 15 krát. Logic Apps měřiče měření všech akcí, které provádí tyto 15 pracovních postupů, včetně požadavků na triggery. Pokud chcete vypočítat možné náklady, vyzkoušejte [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/).