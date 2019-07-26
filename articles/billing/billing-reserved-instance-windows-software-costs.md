---
title: Rezervace nákladů na software pro Azure | Microsoft Docs
description: Zjistěte, které softwarové měřiče nejsou zahrnuté do nákladů na rezervované instance virtuálních počítačů Azure.
services: billing
documentationcenter: ''
author: yashar
manager: yashar
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: banders
ms.openlocfilehash: 52e2e2503303c2a7525a3a6c156f648c097b27dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478614"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Náklady na software nejsou součástí Azure Reserved VM Instances

Rezervovaná instance virtuálního počítače a slevy na rezervované kapacity SQL se vztahují jenom na náklady na infrastrukturu a ne na náklady na software. Pokud používáte virtuální počítač s Windows a nemáte Zvýhodněné hybridní využití Azure na rezervovaných instancích virtuálních počítačů, budou se vám účtovat softwarové měřiče uvedené v následující části. U nasazení SQL PaaS se náklady na IP adresu i nadále účtují pomocí samostatného měřiče, pokud není vybraná Zvýhodněné hybridní využití Azure.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Softwarové měřiče Windows nejsou zahrnuté v ceně za rezervované náklady

| ID měřiče | Měření v souboru využití | Používáno virtuálním počítačem |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Rezervace – Windows počítače Burst (1 jádro) | B Series |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Rezervace – Windows počítače Burst (2 jádra) | B Series |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Rezervace – Windows počítače Burst (4 jádra) | B Series |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Rezervace – Windows počítače Burst (8 jader) | B Series |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Rezervované počítače s Windows (1 jádro) | Všechny kromě řady B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Rezervované počítače s Windows (2 jádra) | Všechny kromě řady B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Rezervované počítače s Windows (4 jádra) | Všechny kromě řady B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Rezervované počítače s Windows (6 jader) | Všechny kromě řady B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Rezervované počítače s Windows (8 jader) | Všechny kromě řady B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Rezervované počítače s Windows (12 jader) | Všechny kromě řady B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Rezervované počítače s Windows (16 jader) | Všechny kromě řady B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Rezervované počítače s Windows (20 jader) | Všechny kromě řady B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Rezervované počítače s Windows (24 jader) | Všechny kromě řady B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Rezervované počítače s Windows (32 jádra) | Všechny kromě řady B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Rezervované počítače s Windows (40 jádra) | Všechny kromě řady B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Rezervované počítače s Windows (64 jádra) | Všechny kromě řady B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Rezervované počítače s Windows (72 jádra) | Všechny kromě řady B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Rezervované počítače s Windows (128 jádra) | Všechny kromě řady B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Rezervované počítače s Windows (256 jádra) | Všechny kromě řady B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Rezervované počítače s Windows (96 jádra) | Všechny kromě řady B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Ceny za rezervované v cloudových službách nejsou zahrnuté v ceně za rezervované

| ID měřiče | Měření v souboru využití |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 licence vCPU|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU licence|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 licence vCPU|
|13103090-ca72-4825-ab12-7f16c4931d95|Licence pro Cloud Services 8 vCPU|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU licence|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Licence na Cloud Services 20 vCPU|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Licence Cloud Services 32 vCPU|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Licence Cloud Services 64 vCPU|
|7a803026-244c-4659-834c-11e6b2d6b76f|Licence Cloud Services 80 vCPU|

## <a name="rates-for-azure-meters"></a>Sazby za Azure měřiče

Náklady na každý z těchto měřičů můžete získat prostřednictvím rozhraní Azure RateCard API. Informace o tom, jak získat sazby za Azure měřiče, najdete v tématu [získání informací o cenách a metadatech pro prostředky používané v rámci předplatného Azure](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Další postup
Další informace o rezervacích pro Azure najdete v následujících článcích:

- [Co jsou rezervace pro Azure?](billing-save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací pro Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení způsobu použití slevy rezervace](billing-understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).
