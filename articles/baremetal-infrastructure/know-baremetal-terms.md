---
title: Zjistěte, jaké jsou požadavky na infrastrukturu Azure BareMetal.
description: Poznejte pojem infrastruktura Azure BareMetal.
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829121"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Znát pojem BareMetal Infrastructure

V tomto článku pokryjeme některé důležité BareMetal výrazy.

- **Revize**: Existují dvě různé revize razítka pro BareMetal instance. Každá verze se liší v architektuře a blízkosti hostitelů virtuálních počítačů Azure:
    - **Revize 3** (rev 3): je původní návrh.
    - **Revize 4** (Rev 4): je nový návrh, který poskytuje užší blízkost hostitelům virtuálních počítačů Azure a snižuje latenci mezi virtuálními počítači Azure a jednotkami instancí BareMetal. 
    - **Revize 4,2** (Rev 4,2): je nejnovější BareMetal infrastruktura, která používá existující architekturu Rev 4. Pomocí Azure Portal můžete přistupovat k instancím služby BareMetal a spravovat je.  

- **Razítko**: definuje interní velikost nasazení BareMetal instancí Microsoftu. Než se dají jednotky instancí nasadit, musí se v umístění datacentra nasadit razítko instance BareMetal, které se skládá z výpočetních, síťových a úložných stojanů. Toto nasazení se nazývá razítko instance BareMetal nebo revize 4,2.

- **Tenant**: zákazník nasazený v razítku instance BareMetal se izoluje do *tenanta.* Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se nemůžou vzájemně zobrazit ani vzájemně komunikovat na úrovni razítek instance BareMetal. Zákazník se může rozhodnout, že mají nasazení v různých klientech. I potom neexistuje žádná komunikace mezi klienty na úrovni razítka instance BareMetal.

## <a name="next-steps"></a>Další kroky
Naučte se identifikovat a interagovat s jednotkami instancí BareMetal prostřednictvím [Azure Portal](workloads/sap/baremetal-infrastructure-portal.md).


 