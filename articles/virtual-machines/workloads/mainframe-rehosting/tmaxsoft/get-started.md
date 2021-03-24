---
title: Začínáme s TmaxSoft OpenFrame na Azure Virtual Machines
description: Znovu hostovat úlohy sálového počítače IBM z/OS pomocí prostředí TmaxSoft OpenFrame na Azure Virtual Machines (virtuálních počítačů).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
ms.openlocfilehash: 346c331596f984cbb068c03110997c2478499488
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950667"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Začínáme s TmaxSoft OpenFrame v Azure

Převezměte své stávající sálové prostředky a přesuňte je do Microsoft Azure pomocí OpenFrameu TmaxSoft. Toto oblíbené řešení pro opětovné hostování vytvoří prostředí pro emulaci v Azure, které vám umožní rychle migrovat aplikace. Není vyžadováno žádné přeformátování.

## <a name="openframe-rehosting-environment"></a>Prostředí pro opětovné hostování OpenFrame

Nastavte OpenFrame prostředí v Azure pro vývoj, ukázky, testování nebo produkční úlohy. Jak ukazuje následující obrázek, OpenFrame obsahuje několik komponent, které vytvářejí prostředí emulace sálového počítače v Azure. OpenFrame online služby například nahrazuje middleware sálového počítače, jako je například systém CICS (Customer Information Control System) společnosti IBM. OpenFrame Batch s jeho komponentou TJES nahrazuje podsystém položek úloh v rámci sálového počítače IBM (JES). 

![Proces opětovného hostování OpenFrame](media/openframe-01.png)

> [!NOTE]
> Pokud chcete spustit prostředí OpenFrame v Azure, musíte mít platnou licenci na produkt nebo zkušební licenci od TmaxSoft.

## <a name="openframe-components"></a>Součásti OpenFrame

Následující komponenty jsou součástí prostředí OpenFrame v Azure:

- **Nástroje pro migraci** , včetně OFMiner, řešení, které analyzuje prostředky sálových počítačů a pak je migruje do Azure.
- **Kompilátory**, včetně OFCOBOL, kompilátor, který interpretuje programy COBOL sálového počítače; OFPLI, která interpretuje programy PL/I tohoto sálového počítače; a OFASM, kompilátor, který interpretuje programy assembleru sálového počítače.
- Komponenty **front-end** , včetně řešení Java Enterprise User (JEUS), serveru webové aplikace, který je certifikovaný pro jazyk Java Enterprise Edition 6. OFGW a komponentou OpenFrame Gateway, která poskytuje naslouchací proces 3270.
- Prostředí **aplikace** . OpenFrame Base je middleware, který spravuje celý systém. OpenFrame Server Type C (OSC) nahrazuje middleware a IBM CICS pro sálové počítače.
- **Relační databáze**, jako je Tibero (zobrazený), Oracle Database, Microsoft SQL Server, IBM Db2 nebo MySQL. Aplikace OpenFrame používají ke komunikaci s databází protokol ODBC (Open Database Connectivity).
- **Zabezpečení** prostřednictvím TACF, modulu služby, který řídí přístup uživatelů k systémům a prostředkům. 
- **OFManager** je řešení, které poskytuje funkce pro operace a správu OpenFrame ve webovém prostředí.

![Architektura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Další kroky

- [Instalace TmaxSoft OpenFrame v Azure](./install-openframe-azure.md)
