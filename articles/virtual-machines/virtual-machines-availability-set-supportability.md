---
title: Podpora přidávání virtuálních počítačů Azure do existující skupiny dostupnosti | Microsoft Docs
description: Tento článek poskytuje matrici podpory, o které řady virtuálních počítačů můžete kombinovat ve stejné skupině dostupnosti.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cb0034f2b353284e94d6f1508541b31040a5b076
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76028420"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Podpora přidávání virtuálních počítačů Azure do existující skupiny dostupnosti

Při přidávání nových virtuálních počítačů do existující skupiny dostupnosti může občas docházet k omezením. Následující graf podrobně popisuje řadu virtuálních počítačů, které můžete kombinovat ve stejné skupině dostupnosti.

Tady je matice podpory ke smíchání různých typů virtuálních počítačů:

Skupina dostupnosti & řady|Druhý virtuální počítač|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|První virtuální počítač|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Všechny ostatní řady nemůžou být ve stejné skupině dostupnosti, protože vyžadují konkrétní hardware.

Velikost virtuálního počítače A8/A8 se nedá kombinovat kvůli požadavku na vyhrazenou síť back-endu RDMA.
