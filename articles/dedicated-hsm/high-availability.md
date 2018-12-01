---
title: Azure vyhrazené modulu hardwarového zabezpečení, vysoké dostupnosti | Dokumentace Microsoftu
description: Azure vyhrazené HSM nabízí možnosti úložiště klíčů v Azure, která splňují standard FIPS 140-2 Level 3 certifikační
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: barclayn
ms.openlocfilehash: b02e3da086a3f33a55650fa72661f65028bfb78b
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52680017"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure vyhrazené modulu hardwarového zabezpečení, vysoké dostupnosti

Azure vyhrazené modulu hardwarového zabezpečení je podporovány s vysokou dostupností datových centrech společnosti Microsoft. Libovolné datacentrum s vysokou dostupností je však zranitelné na místním selháním a extrémních případech, selhání na místní úrovni. Microsoft nasazuje zařízením hardwarového zabezpečení v různých datacentrech v jedné oblasti a ujistěte se, že zřizování více zařízení nevede k těmto zařízením sdílení jediného racku. Další úroveň vysoké dostupnosti můžete dosáhnout spárováním těchto modulů hardwarového zabezpečení v datových centrech v oblasti. Je také možné pár zařízení v různých oblastech adres regionální převzetí služeb při selhání v případě zotavení po havárii. S touto konfigurací víceúrovňová vysoké dostupnosti se automaticky řešit jakékoli neúspěchy zařízení aplikací pracovat. Všech datových centrech také mít náhradní zařízení a součásti na místě tak včas se dá nahradit žádné zařízení se nezdařilo.

## <a name="high-availability-example"></a>Příklad vysoké dostupnosti

Informace o tom, jak nakonfigurovat zařízení HSM pro zajištění vysoké dostupnosti na úrovni softwaru je v "Gemalto Luna sítě HSM Průvodce správou". Tento dokument je k dispozici na [Gemalto zákaznického portálu služeb podpory](https://supportportal.gemalto.com/csm/).

Následující diagram znázorňuje architekturu s vysokou dostupností. Používá více zařízení v oblasti a více zařízení spárovat v samostatné oblasti. Tato architektura používá minimálně čtyři zařízení HSM a součásti virtuální sítě.

![Diagram vysoké dostupnosti](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Další postup

Doporučuje se, že všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení, jsou dobře pochopitelné i před zřizování zařízení a návrh aplikace nebo nasazení.
Další témata úrovně koncept:

* [Architektura nasazení](deployment-architecture.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)

Pro konkrétní podrobnosti o konfiguraci zařízení HSM pro zajištění vysoké dostupnosti najdete na zákaznický portál podpory Gemalto příručky pro správce a najdete v části 6.
