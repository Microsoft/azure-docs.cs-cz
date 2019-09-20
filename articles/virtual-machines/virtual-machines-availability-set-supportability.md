---
title: Podpora přidávání virtuálních počítačů Azure do existující skupiny dostupnosti | Microsoft Docs
description: Podpora přidávání virtuálních počítačů Azure do existující skupiny dostupnosti.
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
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155447"
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
