---
title: Nastavit možnosti podpory pro přidání k existující skupině dostupnosti virtuálních počítačů Azure | Dokumentace Microsoftu
description: Možnosti podpory pro přidání virtuálních počítačů Azure do stávající sady dostupnosti.
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 13448f4b335d84264d4141cb4fb8c3eadcf0303e
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35901625"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>Možnosti podpory pro přidání virtuálních počítačů Azure do stávající sady dostupnosti

Někdy můžete setkat s omezením při přidávání nových virtuálních počítačů (VM) do existující skupiny dostupnosti. Následující graf zobrazuje podrobnosti o které řadu virtuálních počítačů je možné kombinovat ve stejné sadě dostupnosti.

Tady je Přehled podpory kombinovat různé typy virtuálních počítačů:

Série a dostupnosti|Druhý virtuální počítač|A|Av2|D|Řada Dv2|Dv3|
|---|---|---|---|---|---|---|
|První virtuální počítač|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Řada Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

Všechny ostatní řady nebylo možné ve stejné skupině dostupnosti, nastavit, protože vyžadují konkrétní hardware.

Velikosti a8/A9 virtuálního počítače nejde směšovat kvůli requirment na vyhrazenou síť back-end RDMA.
