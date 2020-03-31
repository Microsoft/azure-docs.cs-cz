---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174952"
---
V tomto kroku otestujete naslouchací proces skupiny dostupnosti pomocí klientské aplikace, která je spuštěna ve stejné síti.

Připojení klienta má následující požadavky:

* Připojení klientů k naslouchací proces musí pocházet z počítačů, které jsou umístěny v jiné cloudové služby, než ten, který je hostitelem vždy na dostupnost repliky.
* Pokud jsou repliky Always On v různých podsítích, klienti musí v připojovacím řetězci zadat *MultisubnetFailover=True.* Výsledkem této podmínky jsou pokusy o paralelní připojení repliky v různých podsítích. Tento scénář zahrnuje nasazení skupiny dostupnosti always on napříč oblastmi.

Jedním z příkladů je připojení k naslouchací proces z jednoho z virtuálních počítačů ve stejné virtuální síti Azure (ale ne ten, který hostuje repliku). Snadný způsob, jak dokončit tento test je pokusit se připojit SQL Server Management Studio k naslouchací proces skupiny dostupnosti. Další jednoduchou metodou je spuštění [souboru SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)takto:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Pokud je hodnota EndpointPort *1433*, není nutné ji zadat ve volání. Předchozí volání také předpokládá, že klientský počítač je připojen ke stejné doméně a že volajícímu byla udělena oprávnění k databázi pomocí ověřování systému Windows.
> 
> 

Při testování naslouchací proces, ujistěte se, že převzetí služeb při selhání skupiny dostupnosti a ujistěte se, že klienti mohou připojit k naslouchací proces přes převzetí služeb při selhání.

