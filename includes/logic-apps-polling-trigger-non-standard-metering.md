---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66161960"
---
Pokud chcete odhadnout přesnější nákladů za využití, vezměte v úvahu možný počet zpráv nebo událostí, které může doručení na každý den, spíše než základní výpočtů na interval dotazování. Pokud události nebo zprávy splňuje kritéria aktivační události, řada triggerů okamžitě provede pokus přečíst jakákoli a veškerá další události čekání nebo zprávy, které splňují kritéria. Toto chování znamená, že i když vyberete tak delší interval dotazování se trigger aktivuje, na základě počtu událostí čekání nebo zprávy, které jsou způsobilé pro spouštění pracovních postupů. Triggery, které chovat následovně zahrnují služby Azure Service Bus a Azure Event Hubs.

Proto se například Předpokládejme, že nastavíte aktivační událost, která kontroluje koncový bod, každodenní. Když trigger zkontroluje koncový bod a zjistí 15 události, které splňují zadaná kritéria, aktivuje se a spustí odpovídající postup 15 x. Všechny akce, které provádějí tyto 15 pracovních postupů, včetně požadavků aktivační událost měření služby Logic Apps. Chcete-li vypočítat náklady na potenciální, zkuste [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/).