---
title: Zabezpečení infrastruktury Azure | Microsoft Docs
description: Článek popisuje, jak Microsoft zajišťuje zabezpečení naše datových centrech Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 313fbc0fea317e8888bf64e7f7817ab0e5c9f049
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102174"
---
# <a name="security-of-azure-infrastructure"></a>Zabezpečení infrastruktury Azure
Microsoft Azure je spuštěná v datových centrech spravované a provozována společností Microsoft. Tyto geograficky rozmístěné datových centrech v souladu se klíče oborové standardy, jako je například ISO/IEC 27001: 2013 a SP NIST 800-53, zabezpečení a spolehlivost. V datacentrech se řídí, monitorovat a spravují Microsoft provozní personál. Provozní personál má letech zkušeností doručování s 24 x 7 kontinuity služby online největší na světě.

Tato řada článků obsahuje informace o Microsoft nemá pro zabezpečení infrastruktury Azure. Adresa články:

- [Fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost](azure-infrastructure-availability.md)
- [Součásti a hranice](azure-infrastructure-components.md)
- [Síťová architektura](azure-infrastructure-network.md)
- [Produkční sítě](azure-production-network.md)
- [SQL Database](azure-infrastructure-sql.md)
- [Provoz](azure-infrastructure-operations.md)
- [Monitorování](azure-infrastructure-monitoring.md)
- [Integrita](azure-infrastructure-integrity.md)
- [Ochrana dat](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Sdílené odpovědnost modelu
Je důležité si uvědomit, dělení zodpovědnosti mezi vámi a společností Microsoft. Místní, vlastní celý zásobník, ale při přechodu do cloudu některé odpovědnosti přenos do společnosti Microsoft. Následující odpovědnost matice zobrazuje oblasti zásobníku v softwaru jako služba (SaaS), platformy jako služba (PaaS) a infrastruktury jako služby (IaaS) je zodpovědná za nasazení, které jste zodpovědní za a Microsoft.

![Sdílená odpovědnost][1]

Odpovědnosti, které jsou vždy zachovány vy, bez ohledu na typ nasazení, jsou:

- Data
- Koncové body
- Účet
- Správa přístupu

Ujistěte se, že rozumíte dělení zodpovědnosti mezi vámi a Microsoft v nasazení SaaS, IaaS a PaaS. V tématu [sdílené odpovědnosti pro Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) další podrobnosti.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
