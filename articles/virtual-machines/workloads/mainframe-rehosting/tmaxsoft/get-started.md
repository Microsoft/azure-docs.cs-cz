---
title: Začínáme s TmaxSoft OpenFrame na virtuálních počítačích Azure
description: Znovu hostujte úlohy sálových počítačů IBM z/OS pomocí prostředí TmaxSoft OpenFrame na virtuálních počítačích Azure.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485364"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Začínáme s TmaxSoft OpenFrame v Azure

Vezměte si stávající prostředky sálového počítače a přesuňte je do Microsoft Azure pomocí TmaxSoft OpenFrame. Toto oblíbené rehostingové řešení vytváří prostředí emulace v Azure, což vám umožní rychle migrovat aplikace. Není nutné přeformátování.

## <a name="openframe-rehosting-environment"></a>Prostředí pro rehosting OpenFrame

Nastavte prostředí OpenFrame v Azure pro vývoj, ukázky, testování nebo produkční úlohy. Jak ukazuje následující obrázek, OpenFrame obsahuje více součástí, které vytvářejí prostředí emulace sálových počítačů v Azure. Online služby OpenFrame například nahrazují middleware sálových počítačů, jako je IBM Customer Information Control System (CICS). OpenFrame Batch se svou komponentou TJES nahrazuje dílčí systém dílčího projektu (JES) mainframů IBM. 

![Proces rehostingu OpenFrame](media/openframe-01.png)

> [!NOTE]
> Chcete-li spustit prostředí OpenFrame v Azure, musíte mít platnou licenci produktu nebo zkušební licenci od společnosti TmaxSoft.

## <a name="openframe-components"></a>Komponenty OpenFrame

Následující součásti jsou součástí prostředí OpenFrame v Azure:

- **Nástroje pro migraci,** včetně OFMiner, řešení, které analyzuje prostředky sálových počítačů a pak je migruje do Azure.
- **Kompilátory**, včetně OFCOBOL, kompilátor, který interpretuje mainframe cobol programy; OFPLI, který interpretuje mainframe pl / i programy; a OFASM, kompilátor, který interpretuje mainframe assembler programy.
- **Součásti front-endu,** včetně java podnikového uživatelského řešení (JEUS), webového aplikačního serveru, který je certifikován pro Java Enterprise Edition 6.OFGW, a komponenty brány OpenFrame, která poskytuje naslouchací proces 3270.
- **Aplikační** prostředí. OpenFrame Base je middleware, který spravuje celý systém. OpenFrame Server type C (OSC) nahrazuje middleware sálového počítače a IBM CICS.
- **Relační databáze**, například Tibero (zobrazeno), Oracle Database, Microsoft SQL Server, IBM Db2 nebo MySQL. Aplikace OpenFrame používají ke komunikaci s databází protokol OdBC (Open Database Connectivity).
- **Zabezpečení** prostřednictvím TACF, servisního modulu, který řídí přístup uživatelů k systémům a prostředkům. 
- **OFManager** je řešení, které poskytuje funkce pro provoz a správu OpenFrame ve webovém prostředí.

![Architektura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Další kroky

- [Instalace TmaxSoft OpenFrame do Azure](./install-openframe-azure.md)
