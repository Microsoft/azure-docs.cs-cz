---
title: Azure náklady na software Windows instancí virtuálních počítačů rezervy | Microsoft Docs
description: Další informace, které měřidla softwaru Windows nejsou součástí náklady na vyhrazené instanci virtuálního počítače.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 20fd3c6c118b33ca91d717d30d77bb9c7843f4e6
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>Náklady na software Windows není součástí vyhrazené instance

Pokud nemáte výhody použití služby Azure hybridní na vyhrazené Instance virtuálních počítačů, budou se vám účtovat pro měřidla softwaru systému Windows, uvedené v následující části.

## <a name="windows-software-meters-not-included-in-reserved-instance-cost"></a>Měřidla softwaru systému Windows není součástí náklady na vyhrazené instanci

| ID měřiče | MeterName v souboru využití | Použít virtuální počítač |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Rezervace Windows Svr Burst (1 jádro) | Řada B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Rezervace Windows Svr Burst (2 jádra) | Řada B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Rezervace Windows Svr Burst (4 jádra) | Řada B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Rezervace Windows Svr Burst (8 jader) | Řada B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Rezervace Windows Svr (1 jádro) | Všechny kromě řady B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Rezervace Windows Svr (2 jádra) | Všechny kromě řady B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Rezervace Windows Svr (4 jádra) | Všechny kromě řady B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Rezervace Windows Svr (6 jader) | Všechny kromě řady B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Rezervace Windows Svr (8 jader) | Všechny kromě řady B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Rezervace Windows Svr (12 jader) | Všechny kromě řady B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Rezervace Windows Svr (16 jader) | Všechny kromě řady B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Rezervace Windows Svr (20 jader) | Všechny kromě řady B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Rezervace Windows Svr (24 jader) | Všechny kromě řady B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Rezervace Windows Svr (32 jader) | Všechny kromě řady B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Rezervace Windows Svr (40 jader) | Všechny kromě řady B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Rezervace Windows Svr (64 jader) | Všechny kromě řady B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Rezervace Windows Svr (72 jader) | Všechny kromě řady B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Rezervace Windows Svr (128 jader) | Všechny kromě řady B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Rezervace Windows Svr (256 jader) | Všechny kromě řady B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Rezervace Windows Svr (96 jader) | Všechny kromě řady B |

Náklady na každý z těchto měřidla můžete získat prostřednictvím rozhraní API RateCard Azure. Informace o tom, jak získat sazby pro azure měření najdete v tématu [získání informací o cenách a metadat pro prostředky používané v předplatné Azure](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>Další postup
Další informace o vyhrazenou instancí virtuálního počítače, naleznete v následujících článcích.

- [Předem pro virtuální počítače s instancí vyhrazené virtuálních počítačů](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa instancí vyhrazený virtuální počítač](billing-manage-reserved-vm-instance.md)
- [Ušetřit peníze u virtuálních počítačů s vyhrazenou instancí virtuálního počítače](billing-save-compute-costs-reservations.md)
- [Pochopit, jak se použije slevu vyhrazenou instanci virtuálního počítače](billing-understand-vm-reservation-charges.md)
- [Pochopení vyhrazenou instanci využití pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Pochopení vyhrazenou instanci využití pro podnikového zápisu](billing-understand-reserved-instance-usage-ea.md)
