---
title: Místní načtení a aktualizace spolehlivých záloh kolekcí
description: Pomocí Průzkumníka zálohování v Azure Service Fabric si můžete přečíst a aktualizovat zálohování místních spolehlivých kolekcí.
author: athinanthny
ms.topic: conceptual
ms.date: 07/01/2020
ms.author: atsenthi
ms.openlocfilehash: 1da70c37c8a6ed93e7abe1b5d329e808c592e43a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86034884"
---
# <a name="read-and-update-a-reliable-collections-backup-by-using-backup-explorer"></a>Čtení a aktualizace zálohy spolehlivé kolekce pomocí Průzkumníka zálohování

Průzkumník služby Azure Service Fabric Backup pomáhá s opravou dat v případě, že jsou data v Service Fabric spolehlivých kolekcí poškozená. Aktuální stav dat může být poškozený jakoukoli chybou, která je zavedená v aplikaci, nebo případnými nesprávnými položkami provedenými v živém clusteru.

Pomocí Průzkumníka zálohování můžete provádět následující úlohy:
-   Dotaz na metadata kolekce
-   Zobrazit aktuální stav a jeho položky v kolekci zálohy, která je načtena.
-   Vypíše transakce provedené od posledního kontrolního bodu.
-   Aktualizujte kolekci přidáním, aktualizací nebo odstraněním položek v kolekci.
-   Proveďte novou zálohu pomocí aktualizovaného stavu.

> [!NOTE]
> Průzkumník zálohování Service Fabric aktuálně podporuje jenom prohlížení a úpravy spolehlivých kolekcí v rámci zálohování.
>

## <a name="access-the-backup"></a>Přístup ke službě Backup

Průzkumníka služby Service Fabric Backup lze využít libovolným z následujících způsobů zobrazení nebo aktualizace spolehlivých kolekcí v rámci zálohování:
-   **Binární**: k zobrazení a změně spolehlivých kolekcí použijte balíček NuGet.
-   **Http/REST**: k zobrazení a změně spolehlivých kolekcí použijte server REST založený na protokolu HTTP.
-   **bkpctl**: k zobrazení a změně zálohy spolehlivé kolekce použijte rozhraní příkazového řádku (CLI) aplikace Service Fabric Backup.

Průzkumník zálohování má knihovnu C# pro pokročilé uživatele. Knihovnu v aplikaci můžete použít přímo pro práci s Reliable Collections podobně jako zákazníci, kteří pracují se správcem stavu ve svých stávajících stavových službách. V případě základních uživatelů a v případě základního případu použití má Průzkumník také samostatný server REST, který zpřístupňuje rozhraní API pro kontrolu záloh. Nástroj CLI bkpctl funguje nad rozhraními REST API a je založený na Pythonu. Nástroj rozhraní příkazového řádku můžete použít ke čtení a aktualizaci záloh a k provedení nových záloh pomocí příkazového řádku.

Další informace najdete v části Service Fabric úložiště GitHub v [Průzkumníkovi zálohování](https://github.com/microsoft/service-fabric-backup-explorer) . Úložiště obsahuje informace o zdroji a verzi a pokyny k instalaci.

Úložiště můžete také vytvořit místně a pracovat na zálohování.
 
NuGet pro Průzkumníka zálohování (Microsoft. ServiceFabric. ReliableCollectionBackup. Parser) bude k dispozici v [NuGet.org](https://www.nuget.org/). 

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [spolehlivých kolekcích ve stavových službách Azure Service Fabric](service-fabric-reliable-services-reliable-collections.md).
* Projděte si [Service Fabric osvědčené postupy](service-fabric-best-practices-overview.md).
