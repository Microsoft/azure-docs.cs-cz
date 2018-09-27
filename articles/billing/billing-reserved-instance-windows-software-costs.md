---
title: Náklady na software rezervace Windows Azure | Dokumentace Microsoftu
description: Zjistěte, jaké měření softwaru Windows nejsou zahrnuty v nákladů na rezervované Instance virtuálních počítačů Azure.
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
ms.date: 05/09/2018
ms.author: cwatson
ms.openlocfilehash: 6ec1d0e1b8a768dd61a42c8e9284a93aee3c9337
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392528"
---
# <a name="windows-software-costs-not-included-with-azure-reserved-vm-instances"></a>Náklady na software Windows, které nejsou součástí Azure Reserved VM Instances

Pokud nemáte Azure Hybrid Use Benefit na vaší rezervované instance virtuálních počítačů, pak bude vám účtována měření softwaru Windows uvedené v následující části.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Není součástí náklady na rezervaci měření softwaru Windows

| ID měřiče | MeterName v souboru využití | Které virtuální počítač používá |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Počítače s Windows rezervace serverem datové sekvence (1 jádro) | B Series |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Počítače s Windows rezervace serverem datové sekvence (2 jádra) | B Series |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Počítače s Windows rezervace serverem datové sekvence (4 jádra) | B Series |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Počítače s Windows rezervace serverem datové sekvence (8 jader) | B Series |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Počítače s Windows rezervace serverem (1 jádro) | Všechna kromě řady B Series |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Počítače s Windows rezervace serverem (2 jádra) | Všechna kromě řady B Series |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Počítače s Windows rezervace serverem (4 jádra) | Všechna kromě řady B Series |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Počítače s Windows rezervace serverem (6 jader) | Všechna kromě řady B Series |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Počítače s Windows rezervace serverem (8 jader) | Všechna kromě řady B Series |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Počítače s Windows rezervace serverem (12 jader) | Všechna kromě řady B Series |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Počítače s Windows rezervace serverem (16jádrové) | Všechna kromě řady B Series |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Počítače s Windows rezervace serverem (20 jader) | Všechna kromě řady B Series |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Počítače s Windows rezervace serverem (24 jader) | Všechna kromě řady B Series |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Počítače s Windows rezervace serverem (32jádrové) | Všechna kromě řady B Series |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Počítače s Windows rezervace serverem (40 jader) | Všechna kromě řady B Series |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Počítače s Windows rezervace serverem (64jádrové) | Všechna kromě řady B Series |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Počítače s Windows rezervace serverem (72jádrové) | Všechna kromě řady B Series |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Počítače s Windows rezervace serverem (128jádrové) | Všechna kromě řady B Series |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Počítače s Windows rezervace serverem (256jádrové) | Všechna kromě řady B Series |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Počítače s Windows rezervace serverem (96 jader) | Všechna kromě řady B Series |

Náklady na každý z těchto měřičů můžete získat pomocí Azure RateCard API. Informace o tom, jak získat sazby pro azure měřičů najdete v tématu [získání informací o cenách a metadat pro prostředky používané v rámci předplatného Azure](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>Další postup
Další informace o rezervacích Azure, najdete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplatit si virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení, jak se sleva za rezervaci použije](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.



