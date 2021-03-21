---
title: Vysvětlení restartování virtuálních počítačů
description: Vysvětlení restartování virtuálních počítačů – údržba vs – výpadek
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: af371a8f7da5ef32e95d4096b69c5d52ce3e3700
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510485"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Vysvětlení restartování virtuálních počítačů – údržba vs. výpadky
Existují tři scénáře, které mohou vést k ovlivnění virtuálních počítačů v Azure: neplánovaná údržba hardwaru, neočekávané výpadky a plánovaná údržba.

## <a name="unplanned-hardware-maintenance-event"></a>Neplánovaná událost údržby hardwaru
Neplánovaná údržba hardwaru nastane, pokud platforma Azure předpovídá, že se chystá selhání hardwaru nebo jakékoli součásti platformy přidružené k fyzickému počítači. Když platforma předpoví selhání, vydá událost neplánované údržby hardwaru, aby se snížil dopad na virtuální počítače hostované na tomto hardwaru. Azure používá technologii [migrace za provozu](./maintenance-and-updates.md) k migraci Virtual Machines z neúspěšného hardwaru na dobrý fyzický počítač. Migrace za provozu je operace zachovávající virtuální počítač, který pozastaví jenom na krátkou dobu. Paměť, otevřené soubory a síťová připojení se zachovají, ale výkon se může před událostí a/nebo po ní snížit. V případech, kdy není možné použít Migraci za provozu, dojde k neočekávanému výpadku virtuálního počítače, jak je popsáno níže.


## <a name="unexpected-downtime"></a>Neočekávané výpadky
Neočekávané výpadky se neočekávaně doplní hardware nebo fyzická infrastruktura virtuálního počítače. To může zahrnovat selhání místní sítě, selhání místního disku nebo jiné chyby na úrovni racku. Po zjištění platformy Azure automaticky migruje (zaznamená) virtuální počítač do správného fyzického počítače ve stejném datovém centru. Během opravné procedury jsou virtuální počítače odstavené (restartují se) a v některých případech dojde ke ztrátě dočasné jednotky. Připojené disky s operačním systémem a datové disky se vždy zachovají.

Virtuální počítače můžou také nacházet výpadkům v nepravděpodobném případě výpadku nebo havárie, které mají vliv na celé datové centrum, nebo dokonce na celou oblast. V těchto scénářích poskytuje Azure možnosti ochrany včetně  [zón dostupnosti](../availability-zones/az-overview.md) a [spárovaných oblastí](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Události plánované údržby
Plánované události údržby jsou pravidelné aktualizace od Microsoftu k základní platformě Azure za účelem zlepšení celkové spolehlivosti, výkonu a zabezpečení infrastruktury platformy, na které běží vaše virtuální počítače. Většina těchto aktualizací se provádí bez dopadu na Virtual Machines nebo Cloud Services (viz [Údržba, která nevyžaduje restart](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Přestože se platforma Azure pokouší použít údržbu se zachováním virtuálních počítačů kdykoli je to možné, existují výjimečné případy, kdy tyto aktualizace k aplikaci požadovaných aktualizací na základní infrastrukturu vyžadují restartování virtuálního počítače. V takovém případě můžete provést plánovanou údržbu Azure pomocí operace údržba-opětovné nasazení, která zahájí údržbu virtuálních počítačů ve vhodném časovém intervalu. Další informace najdete v tématu [Plánovaná údržba pro virtuální počítače](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Zkrátit prostoje
Pokud chcete snížit dopad výpadků kvůli jedné nebo několika takovým událostem, doporučujeme pro vaše virtuální počítače následující osvědčené postupy z hlediska vysoké dostupnosti:

* Použití [zóny dostupnosti](../availability-zones/az-overview.md) k ochraně před selháními datového centra
* Konfigurace více virtuálních počítačů ve [skupině dostupnosti](availability-set-overview.md) pro zajištění redundance
* Použijte [naplánované události pro Linux](/linux/scheduled-events.md) nebo [naplánované události pro systém Windows](/windows/scheduled-events.md) k proaktivní reakci na události dopadu na virtuální počítač.
* Konfigurace jednotlivých vrstev aplikace v samostatných skupinách dostupnosti
* Kombinování [Nástroje pro vyrovnávání zatížení](../load-balancer/load-balancer-overview.md) se zónami nebo sadami dostupnosti

## <a name="next-steps"></a>Další kroky
Další informace o možnostech dostupnosti v Azure najdete v tématu [Přehled dostupnosti](availability.md).
