---
title: Tabulka kompatibility operačního systému pro SAP HANA (velké instance) | Microsoft Docs
description: Matice kompatibility představuje kompatibilitu různých verzí operačního systému s různými typy hardwaru (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fbc6c7b8811f3cf46b4f31387c2181c8d085e39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83684875"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Kompatibilní operační systémy pro velké instance HANA

## <a name="hana-large-instance-type-i"></a>Typ velké instance HANA I     
  | Operační systém | Dostupnost        | Skladové položky                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Už se nenabízí | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Trvalé SKU paměti
  | Operační systém | Dostupnost | Skladové položky                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | K dispozici    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Velká instance HANA – typ II     
  |  Operační systém       | Dostupnost        | Skladové položky                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | Už se nenabízí | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP4             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP5             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Související dokumenty

- Další informace o [dostupných SKU](hana-available-skus.md)
- Informace o [upgradu operačního systému](os-upgrade-hana-large-instance.md)
  

  
  
