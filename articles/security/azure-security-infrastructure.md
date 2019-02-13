---
title: Zabezpečení infrastruktury Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak Microsoft spolupracuje zabezpečit našich datových centrech Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: dc9b4db37e811d8bac6df2d532fd3629d98c9650
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104759"
---
# <a name="azure-infrastructure-security"></a>Zabezpečení infrastruktury Azure
Microsoft Azure je spuštěná v datacentrech spravovaná a provozované společností Microsoft. Tato datová centra rozptýlené v souladu s klíče oborové standardy, jako jsou ISO/IEC 27001: 2013 a SP NIST 800-53, zabezpečení a spolehlivost. Tato datová centra jsou spravovaná, monitorovat a spravovat prostřednictvím Microsoft provozní personál. Provozní personál se po několika letech zkušeností doručování největších světových online služeb s 24 x 7 kontinuity podnikových procesů.

Tato série článků obsahuje informace o Microsoft nemá pro zabezpečení infrastruktury Azure. Adresa články:

- [fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost](azure-infrastructure-availability.md)
- [Komponenty a hranice](azure-infrastructure-components.md)
- [Architektura sítě](azure-infrastructure-network.md)
- [Produkční sítě](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Provoz](azure-infrastructure-operations.md)
- [Monitorování](azure-infrastructure-monitoring.md)
- [Integrita](azure-infrastructure-integrity.md)
- [Ochrana dat](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Sdílená odpovědnost modelu
Je důležité pochopit, dělení zodpovědnosti mezi vámi a společností Microsoft. On-premises celý balík, které vlastníte, ale při přesunu do cloudu, určité podmínky přenos do společnosti Microsoft. Následující obrázek znázorňuje oblasti odpovědnosti, podle typu nasazení do zásobníku (software jako služba [SaaS], platforma jako služba [PaaS], infrastruktura jako služba [IaaS] i v místním).

![Obrázek znázorňující odpovědnosti][1]

Vždy je zodpovědná za následující příkaz, bez ohledu na typ nasazení:

- Data
- Koncové body
- Účet
- Správa přístupu

Ujistěte se, že rozumíte dělení zodpovědnosti mezi vámi a společností Microsoft v nasazení SaaS, PaaS a IaaS. Další informace najdete v tématu [sdílená odpovědnost za cloud computingu](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Další postup
Další informace o co společnost Microsoft k zajištění infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
