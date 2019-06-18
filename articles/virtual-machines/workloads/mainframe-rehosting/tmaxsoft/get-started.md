---
title: Začínáme s TmaxSoft OpenFrame ve službě Azure Virtual Machines
description: Změna hostitele úloh mainframových IBM z/OS pomocí TmaxSoft OpenFrame prostředí v Azure Virtual Machines (VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485364"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Začínáme s TmaxSoft OpenFrame v Azure

Využijte stávající prostředky mainframových a přesuňte je do Microsoft Azure s využitím TmaxSoft OpenFrame. Tato oblíbená řešení rehosting vytvoří emulace prostředí v Azure, takže se můžete rychle migrovat aplikace. Vyžaduje se žádné formátování.

## <a name="openframe-rehosting-environment"></a>Změna hostování prostředí OpenFrame

Nastavte OpenFrame prostředí v Azure pro vývoj ukázky, testování a produkční úlohy. Jak ukazuje následující obrázek, obsahuje OpenFrame několik komponent, které vytvoření prostředí emulace sálové počítače v Azure. Sálové počítače middlewaru například IBM zákazníka informace ovládacího prvku systému (CICS) nahraďte třeba OpenFrame služeb online services. OpenFrame Batch se jeho součástí TJES nahradí sálové počítače IBM úlohy položka Subsystem (JES). 

![Změna hostování procesu OpenFrame](media/openframe-01.png)

> [!NOTE]
> Pokud chcete spustit prostředí OpenFrame v Azure, musí mít platnou licenci produktu nebo zkušební licenci z TmaxSoft.

## <a name="openframe-components"></a>OpenFrame komponenty

Následující komponenty jsou součástí OpenFrame prostředí v Azure:

- **Nástroje pro migraci** včetně OFMiner, řešení, které analyzuje prostředky sálové počítače a přenese je do Azure.
- **Kompilátory**, včetně OFCOBOL, kompilátor, který interpretuje sálovými COBOL programy. OFPLI, které interpretuje sálovými PL / mi programy; a OFASM, kompilátor, který interpretuje sálovými assembler programy.
- **Front-endu** komponent, včetně Java Enterprise uživatele řešení (JEUS), webový server, který má certifikaci pro platformu Java Enterprise Edition 6.OFGW aplikace a součásti OpenFrame brány, která poskytuje 3270 naslouchací proces.
- **Aplikace** prostředí. Základní OpenFrame je middleware, který spravuje celý systém. OpenFrame serveru typ jazyka C (OSC) nahrazuje sálové počítače middlewaru a IBM CICS.
- **Relační databáze**, jako je například Tibero (viz obrázek), Oracle Database, Microsoft SQL Server, IBM Db2 nebo MySQL. OpenFrame aplikací používají protokol připojení ODBC (Open Database) ke komunikaci s databází.
- **Zabezpečení** prostřednictvím TACF, modul služby, které řídí přístup uživatelů k systémům a prostředkům. 
- **OFManager** je řešení, které poskytuje OpenFrame pro operace a Správa funkce v prostředí webu.

![Architektura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Další postup

- [Instalace TmaxSoft OpenFrame v Azure](./install-openframe-azure.md)
