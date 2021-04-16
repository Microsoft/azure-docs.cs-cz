---
title: Zjistěte, jaké jsou požadavky na infrastrukturu Azure BareMetal.
description: Poznejte pojem infrastruktura Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: aa7d9693b3417ff0bb6c6a61800aee72cd416c48
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536781"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Znát pojem BareMetal Infrastructure

V tomto článku se podíváme na některé důležité výrazy, které se týkají infrastruktury BareMetal.

- **Revize**: existuje původní revize razítka známá jako revize 3 (rev 3) a dvě další revize razítka pro BareMetal instance. Jednotlivá razítka se liší v architektuře a blízkosti hostitelů virtuálních počítačů Azure:
    - **Revize 4** (Rev 4): novější návrh, který poskytuje užší blízkost hostitelům virtuálních počítačů Azure a snižuje latenci mezi virtuálními počítači Azure a instancemi SAP HANA. 
    - **Revize 4,2** (Rev 4,2): nejnovější BareMetal infrastruktura s použitím existující architektury Rev 4. Rev 4 poskytuje užší blízkost pro hostitele virtuálních počítačů Azure. Má významná vylepšení latence sítě mezi virtuálními počítači Azure a BareMetal instancemi nasazenými v razítkech nebo řádcích Rev 4. Pomocí Azure Portal můžete přistupovat k instancím služby BareMetal a spravovat je.    

- **Razítko**: definuje interní velikost nasazení BareMetal instancí Microsoftu. Před nasazením instancí se musí v umístění datacentra nasadit razítko instance BareMetal, které se skládá z úložišť COMPUTE, sítě a úložiště. Toto nasazení se nazývá razítko instance BareMetal.

- **Tenant**: zákazník, který nasazuje razítko instance BareMetal, se jako tenant izoluje *.* Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se nemůžou vzájemně zobrazit ani vzájemně komunikovat na úrovni razítek instance BareMetal. Zákazník se může rozhodnout, že mají nasazení v různých klientech. I potom neexistuje žádná komunikace mezi klienty na úrovni razítka instance BareMetal.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s důležitou terminologií infrastruktury BareMetal, můžete chtít získat další informace:
- Další podrobnosti o [infrastruktuře BareMetal](concepts-baremetal-infrastructure-overview.md)
- Postup [propojení instancí infrastruktury BareMetal v Azure](connect-baremetal-infrastructure.md)

