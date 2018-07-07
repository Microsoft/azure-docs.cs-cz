---
title: Zabezpečení infrastruktury Azure | Dokumentace Microsoftu
description: Tento článek popisuje, jak Microsoft zajišťuje zabezpečení našich datových centrech Azure.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 397bd1f904b676a6ba020ec78fb1cad05c460be1
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903852"
---
# <a name="azure-infrastructure-security"></a>Zabezpečení infrastruktury Azure
Microsoft Azure je spuštěná v datacentrech spravovaná a provozované společností Microsoft. Tato datová centra rozptýlené v souladu s klíče oborové standardy, jako jsou ISO/IEC 27001: 2013 a SP NIST 800-53, zabezpečení a spolehlivost. Tato datová centra jsou spravovaná, monitorovat a spravovat prostřednictvím Microsoft provozní personál. Provozní personál se po několika letech zkušeností doručování největších světových online služeb s 24 x 7 kontinuity podnikových procesů.

Tato série článků obsahuje informace o Microsoft nemá pro zabezpečení infrastruktury Azure. Adresa články:

- [Fyzické zabezpečení](azure-physical-security.md)
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
Je důležité pochopit, dělení zodpovědnosti mezi vámi a společností Microsoft. On-premises celý balík, které vlastníte, ale při přesunu do cloudu určité podmínky přenos do společnosti Microsoft. Následující odpovědnost matice zobrazuje oblasti zásobníku v podobě softwaru jako služby (SaaS), platforma jako služba (PaaS) a infrastruktura jako služba (IaaS) nasazení, které budete muset a Microsoft je zodpovědný za.

![Sdílená odpovědnost][1]

Odpovědnosti, které jsou vždy zachovány vámi, bez ohledu na typ nasazení, jsou:

- Data
- Koncové body
- Účet
- Správa přístupu

Ujistěte se, že rozumíte dělení zodpovědnosti mezi vámi a společností Microsoft v nasazení SaaS, PaaS a IaaS. Zobrazit [sdílet odpovědnost za Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf) další podrobnosti.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrany zákaznických dat v Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
