---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227370"
---
V tomto kroku testu naslouchacího procesu skupiny dostupnosti s využitím klientské aplikace, na kterém běží ve stejné síti.

Připojení klienta má následující požadavky:

* Připojení klienta k naslouchacímu procesu musí pocházet z počítačů, které se nacházejí v jinou cloudovou službu než ten, který je hostitelem repliky dostupnosti Always On.
* Pokud jsou repliky Always On v různých podsítích, budou muset klienti zadat *MultisubnetFailover = True* v připojovacím řetězci. Tato podmínka způsobí pokusy o paralelní připojení repliky v různých podsítích. Tento scénář obsahuje mezi různými oblastmi vždy na dostupnost skupiny nasazení.

Jedním z příkladů je pro připojení k naslouchacímu procesu z jednoho z virtuálních počítačů ve stejné virtuální síti Azure (ale ne ten, který je hostitelem repliky). Snadný způsob, jak dokončit tento test se pokusí připojit k naslouchacího procesu skupiny dostupnosti SQL Server Management Studio. Jednoduché se spustit [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), následujícím způsobem:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Pokud je hodnota EndpointPort *1433*, není nutné ho zadat při volání metody. Předchozí volání také předpokládá, že klientský počítač připojený ke stejné doméně a aby volající byla udělena oprávnění v databázi s použitím ověřování Windows.
> 
> 

Při testování naslouchací proces, ujistěte se, že jste převzetí služeb při selhání skupiny dostupnosti, abyste měli jistotu, že klienti můžou připojit k naslouchacímu procesu napříč převzetí služeb při selhání.

