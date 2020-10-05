---
title: Přehled klientské knihovny chatu pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o klientské knihovně konverzace ke službě Azure Communications Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3b66c962758afc28da3c6be938c13d768c4f2572
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91666139"
---
# <a name="chat-client-library-overview"></a>Přehled klientských knihoven pro chat

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Knihovny klienta služby Azure Communication Services slouží k přidávání bohatých konverzací v reálném čase do vašich aplikací.

## <a name="chat-client-library-capabilities"></a>Chat – možnosti klientské knihovny

Následující seznam obsahuje sadu funkcí, které jsou aktuálně k dispozici v klientských knihovnách služby Communications.

| Skupina funkcí | Schopnost                                                                                                          | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | --- | ----- | ---- | -----  |
| Základní funkce | Vytvoření konverzačního vlákna mezi 2 nebo více uživateli (až 250 uživatelů)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Aktualizace tématu konverzačního vlákna                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |
|                   | Přidání nebo odebrání členů z konverzačního vlákna                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Vyberte, jestli se má sdílet historie zpráv chatu s nově přidanými členy – *vše/nic/až po určitou dobu* . | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Získá seznam všech vláken členů chatu.                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Odstraní vlákno chatu.                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Získat seznam členství uživatelského vlákna v konverzaci                                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Získat informace o konkrétním vlákně chatu                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Posílání a přijímání zpráv ve vlákně chatu                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |
|                   | Úprava obsahu zprávy po jejím odeslání                                                                   | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Odstranění zprávy                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |
|                   | Označení zprávy s prioritou normální nebo vysoká v době odeslání                                               | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Odesílání a příjem oznámení o přečtení pro zprávy, které byly přečteny členy <br/> *Není k dispozici, pokud je v konverzačním vlákně více než 20 členů.*    | ✔️   | ✔️  | ✔️    | ✔️   |
|                   | Posílání a přijímání oznámení o psaní, když člen aktivně zapisuje zprávu do konverzačního vlákna <br/> *Není k dispozici, pokud je v konverzačním vlákně více než 20 členů.*      | ✔️   | ✔️   | ✔️    | ✔️    |
|                   | Získá všechny zprávy ve vlákně chatu. <br/> *Podporovaná sada Unicode Emoji*                                                  | ✔️   | ✔️  | ✔️    | ✔️  |
|                   | Odeslat emoji jako součást obsahu zprávy                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |
|Signalizace v reálném čase (povoleno pomocí proprietárního balíčku pro signalizaci)| Oznámení o tom, že uživatel obdrží novou zprávu ve vlákně chatu, které je členem                                     | ✔️   | ❌    | ❌  | ❌  |
|                    | Oznámení o tom, že byla zpráva upravována jiným členem ve vlákně chatu, které je členem                | ✔️   | ❌    | ❌    | ❌  |
|                    | Oznámení o odstranění zprávy jiným členem v konverzačním vlákně, které je členem                | ✔️   | ❌    | ❌    | ❌  |
|                    | Dostávat oznámení, když zadá jiný člen konverzačního vlákna                                                             | ✔️   | ❌    | ❌    | ❌  |
|                    | Dostávat oznámení, když jiný člen přečte zprávu (stvrzenka pro čtení) ve vlákně chatu                               | ✔️   | ❌    | ❌    | ❌  |
| Události             | Použití Event Grid k přihlášení k odběru aktivity uživatelů při konverzaci v vláknech a integraci vlastních služeb oznámení nebo obchodní logiky     | ✔️   | ✔️  | ✔️    | ✔️  |
| Monitorování        | Monitorování využití z doručených zpráv                                                                               | ✔️   | ✔️  | ✔️    | ✔️  |
|                    | Monitorování kvality a stavu požadavků rozhraní API provedených vaší aplikací a konfigurace výstrah prostřednictvím portálu                                                          | ✔️   | ✔️  | ✔️    | ✔️  |
|Další funkce | Pomocí [rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) společně s klientskou knihovnou chatu můžete povolit inteligentní funkce *překladu jazyka & mínění analýzy příchozích zpráv v klientovi, konverzi řeči na text a vytvoření zprávy, když člen mluví atd.*                                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s chatem](../../quickstarts/chat/get-started.md)

Následující dokumenty můžou být zajímavé:

- Seznamte se s [Koncepty chatu](../chat/concepts.md)
