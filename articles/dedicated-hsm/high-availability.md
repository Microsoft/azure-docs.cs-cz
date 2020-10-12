---
title: Vysoká dostupnost – vyhrazený modul HARDWAROVÉho zabezpečení Azure | Microsoft Docs
description: Přečtěte si o základních požadavcích na vysoce dostupnou vysokou dostupnost HSM v Azure. Tento článek obsahuje příklad.
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
ms.openlocfilehash: 8f8fa2f12825fe88218fe7033a1721cb49fc7335
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189845"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Vysoká dostupnost služby HSM ve vyhrazeném Azure

Vyhrazený modul HARDWAROVÉho zabezpečení Azure je nepřipojený k datovým centrům Microsoftu s vysokou dostupností. Jakékoli vysoce dostupné datacentrum je ale zranitelné kvůli lokalizovaným chybám a v extrémních případech při selhání regionální úrovně. Microsoft nasazuje zařízení HSM v různých datacentrech v rámci oblasti, aby se zajistilo, že zřizování více zařízení nevede k těmto zařízením, která sdílejí jeden stojan. Další úroveň vysoké dostupnosti je možné dosáhnout spárováním těchto HSM napříč datacentry v oblasti pomocí funkce skupiny identita Gemalto HA. Je také možné spárovat zařízení v různých oblastech a řešit regionální převzetí služeb při selhání v situacích zotavení po havárii. U této vysoce vrstvené konfigurace vysoké dostupnosti se všechna selhání zařízení budou automaticky adresovat, aby aplikace zůstaly v provozu. Všechna datová centra mají také náhradní zařízení a součásti na pracovišti, aby bylo možné jakékoli vadné zařízení nahradit včas.

## <a name="high-availability-example"></a>Příklad vysoké dostupnosti

Informace o tom, jak nakonfigurovat zařízení HSM pro vysokou dostupnost na úrovni softwaru, najdete v příručce pro správu HSM identita Gemalto Luna Network HSM. Tento dokument je k dispozici na  [stránce HSM identita Gemalto](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

Následující diagram znázorňuje architekturu s vysokou dostupností. Používá v oblasti více zařízení a několik zařízení se spáruje v samostatné oblasti. Tato architektura používá minimálně čtyři zařízení HSM a součásti virtuální sítě.

![Diagram vysoké dostupnosti](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Další kroky

Doporučuje se, abyste všechny klíčové koncepty služby, jako je vysoká dostupnost a zabezpečení, dobře pochopili ještě před zřizováním a navrhováním aplikací a jejich nasazením.
Další témata o úrovni konceptu:

* [Architektura nasazení](deployment-architecture.md)
* [Fyzické zabezpečení](physical-security.md)
* [Sítě](networking.md)
* [Možnosti podpory](supportability.md)
* [Monitorování](monitoring.md)

Konkrétní podrobnosti o konfiguraci zařízení HSM pro vysokou dostupnost najdete na portálu zákaznických služeb identita Gemalto pro správce a v části 6.
