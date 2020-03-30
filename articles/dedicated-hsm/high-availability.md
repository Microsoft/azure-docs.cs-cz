---
title: Vysoká dostupnost – Azure Dedicated HSM | Dokumenty společnosti Microsoft
description: Azure Dedicated HSM příklad vysoké dostupnosti a základní aspekty
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882250"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicated HSM vysoká dostupnost

Azure Dedicated HSM je podpořena vysoce dostupnými datovými centry Microsoftu. Všechny vysoce dostupné datové centrum je však náchylné k lokalizované selhání a v extrémních případech selhání regionální úrovně. Společnost Microsoft nasazuje zařízení hsm v různých datových centrech v rámci oblasti, aby zajistila zřizování více zařízení nevede k tomu, že tato zařízení sdílejí jeden stojan. Další úroveň vysoké dostupnosti lze dosáhnout spárováním těchto souborů zásad zabezpečení v datových centrech v oblasti pomocí funkce Gemalto HA Group. Je také možné spárovat zařízení mezi oblastmi k řešení místní převzetí služeb při selhání v situaci zotavení po havárii. Díky této vícevrstvé konfiguraci s vysokou dostupností bude automaticky vyřešeno jakékoli selhání zařízení, aby aplikace fungovaly. Všechna datová centra mají také náhradní zařízení a komponenty na místě, takže jakékoli neúspěšné zařízení lze vyměnit včas.

## <a name="high-availability-example"></a>Příklad vysoké dostupnosti

Informace o konfiguraci zařízení pro zabezpečení hsm pro vysokou dostupnost na úrovni softwaru naleznete v příručce "Gemalto Luna Network HSM Administration Guide". Tento dokument je k dispozici na [stránce Gemalto HSM .](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)

Následující diagram znázorňuje vysoce dostupnou architekturu. Používá více zařízení v oblasti a více zařízení spárovaných v samostatné oblasti. Tato architektura používá minimálně čtyři zařízení hsm a součásti virtuální sítě.

![Diagram vysoké dostupnosti](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Další kroky

Doporučuje se, aby všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení, jsou dobře pochopeny před zřizováním zařízení a návrhem nebo nasazením aplikací.
Další témata na úrovni konceptu:

* [Architektura nasazení](deployment-architecture.md)
* [Fyzické zabezpečení](physical-security.md)
* [Síťové služby](networking.md)
* [Možnosti podpory](supportability.md)
* [Sledování](monitoring.md)

Konkrétní podrobnosti o konfiguraci zařízení s pomocí hsm pro vysokou dostupnost naleznete na portálu podpory zákazníků Gemalto pro průvodce správcem a v části 6.
