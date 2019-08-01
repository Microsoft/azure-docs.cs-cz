---
title: Zabezpečení infrastruktury Azure | Microsoft Docs
description: Tento článek popisuje, jak Microsoft pracuje na zabezpečení našich DataCentre Azure.
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
ms.openlocfilehash: 1dd9d9d4fc47f13f275e3be87ad282640840f873
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615353"
---
# <a name="azure-infrastructure-security"></a>Zabezpečení infrastruktury Azure
Microsoft Azure běží v datových centrech spravovaných a provozovaných společností Microsoft. Tato geograficky rozptýlená datacentra vyhovují klíčovým oborovým standardům, jako jsou ISO/IEC 27001:2013 a NIST SP 800-53, kvůli zabezpečení a spolehlivosti. Datová centra jsou spravovaná, monitorovaná a spravovaná provozními pracovníky Microsoftu. Provozní pracovníci mají po dobu let zkušeností s poskytováním největších online službyů na světě s nepřetržitou kontinuitou (24 × 7).

Tato série článků poskytuje informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure. Adresa článků:

- [Fyzické zabezpečení](physical-security.md)
- [Dostupnost](infrastructure-availability.md)
- [Součásti a hranice](infrastructure-components.md)
- [Architektura sítě](infrastructure-network.md)
- [Produkční síť](production-network.md)
- [SQL Database](infrastructure-sql.md)
- [Provoz](infrastructure-operations.md)
- [Monitorování](infrastructure-monitoring.md)
- [Způsobilost](infrastructure-integrity.md)
- [Ochrana dat](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Sdílený model odpovědnosti
Je důležité pochopit rozdělení zodpovědnosti mezi vámi a společností Microsoft. V místním prostředí jste vlastníkem celého zásobníku, ale při přesunu do cloudu se některé zodpovědnosti přenášejí do Microsoftu. Následující obrázek znázorňuje oblasti zodpovědnosti podle typu nasazení vašeho zásobníku (software jako služba [SaaS], platforma jako služba [PaaS], infrastruktura jako služba [IaaS] a místní).

![Obrázek znázorňující zodpovědnosti](./media/infrastructure/responsibility-zones.png)

Vždycky zodpovídáte za následující, bez ohledu na typ nasazení:

- Data
- Koncové body
- Účet
- Správa přístupu

Ujistěte se, že rozumíte rozdělení zodpovědnosti mezi vámi a společností Microsoft v nasazení SaaS, PaaS a IaaS. Další informace najdete v tématu [sdílené odpovědnosti pro cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)


