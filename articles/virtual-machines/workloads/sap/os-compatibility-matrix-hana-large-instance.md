---
title: Tabulka kompatibility operačního systému pro SAP HANA (velké instance) | Microsoft Docs
description: Matice kompatibility představuje kompatibilitu různých verzí operačního systému s různými typy hardwaru (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/21/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6d9ae1f7b8741da1116c6591ee672e4073cfc5af
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955544"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Kompatibilní operační systémy pro velké instance HANA

## <a name="hana-large-instance-type-i"></a>Typ velké instance HANA I     
  | Operační systém | Dostupnost        | Skladové položky                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | Už se nenabízí | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 12 SP5      | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | SLES 15 SP1      | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  | RHEL 7,6         | K dispozici           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |

  
### <a name="persistent-memory-skus"></a>Trvalé SKU paměti
  | Operační systém | Dostupnost | Skladové položky                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | K dispozici    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Velká instance HANA – typ II     
  |  Operační systém       | Dostupnost        | Skladové položky                                                                     |
  |-------------------------|---------------------|--------------------------------------------------------------------------|
  | SLES 12 SP2             | Už se nenabízí | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP3             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP4             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m        |
  | SLES 12 SP5             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | SLES 15 SP1             | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |
  | RHEL 7,6                | K dispozici           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S896m, S960m |

## <a name="related-documents"></a>Související dokumenty

- Další informace o [dostupných SKU](hana-available-skus.md)
- Informace o [upgradu operačního systému](os-upgrade-hana-large-instance.md)
  

  
  
