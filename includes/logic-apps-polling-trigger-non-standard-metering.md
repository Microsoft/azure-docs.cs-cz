---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74793447"
---
Chcete-li odhadnout přesnější náklady na spotřebu, zvažte možný počet zpráv nebo událostí, které by mohly být doručeny v daný den, a nezaložte výpočty pouze na intervalu dotazování. Pokud událost nebo zpráva splňuje kritéria aktivační události, mnoho aktivačních událostí se okamžitě pokusí přečíst všechny a všechny ostatní čekající události nebo zprávy, které splňují kritéria. Toto chování znamená, že i když vyberete delší interval dotazování, aktivační událost se aktivuje na základě počtu čekajících událostí nebo zpráv, které jsou způsobilé pro spuštění pracovních postupů. Aktivační události, které následují toto chování patří Azure Service Bus a Azure Event Hub.

Předpokládejme například, že nastavíte aktivační událost, která kontroluje koncový bod každý den. Když aktivační událost zkontroluje koncový bod a najde 15 událostí, které splňují kritéria, aktivační událost spustí a spustí odpovídající pracovní postup 15 krát. Aplikace logiky měří všechny akce, které tyto 15 pracovních postupů provádět, včetně požadavků na aktivační události. Potenciální náklady spočítejte tak, že se vám [započítává cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/).