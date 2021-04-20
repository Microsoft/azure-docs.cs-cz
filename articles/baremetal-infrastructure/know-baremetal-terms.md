---
title: Zjistěte, jaké jsou požadavky na infrastrukturu Azure BareMetal.
description: Poznejte pojem infrastruktura Azure BareMetal.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725453"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>Znát pojem BareMetal Infrastructure

V tomto článku se podíváme na některé důležité výrazy, které se týkají infrastruktury BareMetal.

- **Revize**: Existují dvě různé revize razítek pro razítka BareMetal infrastruktury (Hana velká instance). Ty se liší v architektuře a blízkosti hostitelů virtuálních počítačů Azure:
    - "Revize 3" (rev 3): původní návrh nasazený v polovině-2016.
    - "Revize 4,2" (Rev 4,2): nový návrh, který poskytuje užší blízkost pro hostitele virtuálních počítačů Azure, s nízkou latencí sítě mezi virtuálními počítači Azure a velkými instancemi HANA. Prostředky v Azure Portal jsou označovány jako "infrastruktura BareMetal" a zákazníci mají přístup ke svým prostředkům jako instance BareMetal z Azure Portal.

- **Razítko**: definuje interní velikost nasazení BareMetal instancí Microsoftu. Před nasazením instancí se musí v umístění datacentra nasadit razítko instance BareMetal, které se skládá z úložišť COMPUTE, sítě a úložiště. Toto nasazení se nazývá razítko instance BareMetal.

- **Tenant**: zákazník, který nasazuje razítko instance BareMetal, se jako tenant izoluje *.* Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se nemůžou vzájemně zobrazit ani vzájemně komunikovat na úrovni razítek instance BareMetal. Zákazník se může rozhodnout, že mají nasazení v různých klientech. I potom neexistuje žádná komunikace mezi klienty na úrovni razítka instance BareMetal.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s důležitou terminologií infrastruktury BareMetal, můžete chtít získat další informace:
- Další podrobnosti o [infrastruktuře BareMetal](concepts-baremetal-infrastructure-overview.md)
- Postup [propojení instancí infrastruktury BareMetal v Azure](connect-baremetal-infrastructure.md)

