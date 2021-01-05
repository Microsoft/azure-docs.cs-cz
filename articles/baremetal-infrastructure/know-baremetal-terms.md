---
title: Zjistěte, jaké jsou požadavky na infrastrukturu Azure BareMetal.
description: Poznejte pojem infrastruktura Azure BareMetal.
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861911"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Znát pojem BareMetal Infrastructure

V tomto článku pokryjeme některé důležité BareMetal výrazy.

- **Revize**: existuje původní revize razítka známá jako revize 3 (rev 3) a dvě různé revize razítka pro razítka instance BareMetal. Jednotlivá razítka se liší v architektuře a blízkosti hostitelů virtuálních počítačů Azure:
    - **Revize 4** (Rev 4): novější návrh, který poskytuje užší blízkost hostitelům virtuálních počítačů Azure a snižuje latenci mezi virtuálními počítači Azure a jednotkami instancí BareMetal. 
    - **Revize 4,2** (Rev 4,2): nejnovější BareMetal infrastruktura s použitím existující architektury Rev 4. Rev 4 poskytuje užší blízkost pro hostitele virtuálních počítačů Azure. Má významná vylepšení latence sítě mezi virtuálními počítači Azure a jednotkami instancí BareMetal nasazenými v razítkech a na řádcích Rev 4. Pomocí Azure Portal můžete přistupovat k instancím služby BareMetal a spravovat je.    

- **Razítko**: definuje interní velikost nasazení BareMetal instancí Microsoftu. Než se dají jednotky instancí nasadit, musí se v umístění datacentra nasadit razítko instance BareMetal, které se skládá z úložišť COMPUTE, sítě a úložiště. Toto nasazení se nazývá razítko instance BareMetal nebo revize 4,2.

- **Tenant**: zákazník nasazený v razítku instance BareMetal se izoluje do *tenanta.* Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se nemůžou vzájemně zobrazit ani vzájemně komunikovat na úrovni razítek instance BareMetal. Zákazník se může rozhodnout, že mají nasazení v různých klientech. I potom neexistuje žádná komunikace mezi klienty na úrovni razítka instance BareMetal.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [infrastruktuře BareMetal](workloads/sap/baremetal-overview-architecture.md) nebo o tom [, jak identifikovat a pracovat s jednotkami instancí BareMetal](workloads/sap/baremetal-infrastructure-portal.md). 