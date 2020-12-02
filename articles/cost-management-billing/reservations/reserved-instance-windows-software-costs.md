---
title: Náklady na rezervace softwaru pro Azure
description: Zjistěte, které softwarové měřiče nejsou zahrnuté do nákladů služby Azure Reserved VM Instances.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 625c40ea17cf20449f1be241a1605a80bf6a73f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352996"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Softwarové náklady nezahrnuté ve službě Azure Reserved VM Instances

Slevy na rezervované instance virtuálního počítače a rezervované kapacity SQL se týkají jenom nákladů na infrastrukturu, a ne nákladů na software. Pokud používáte virtuální počítač Windows a nemáte na rezervovaných instancích virtuálních počítačů Zvýhodněné hybridní využití Azure, budou se vám účtovat softwarové měřiče uvedené v následující části. U nasazení SQL PaaS se náklady na IP adresu i dál účtují pomocí samostatného měřiče, pokud není vybrané Zvýhodněné hybridní využití Azure.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Softwarové měřiče Windows nezahrnuté v nákladech na rezervaci

| MeterId | Název měřiče v souboru využití | Použito virtuálním počítačem |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Rezervované počítače s Windows Serverem – datové sekvence (1jádrové) | Řada B |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Rezervované počítače s Windows Serverem – datové sekvence (2jádrové) | Řada B |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Rezervované počítače s Windows Serverem – datové sekvence (4jádrové) | Řada B |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Rezervované počítače s Windows Serverem – datové sekvence (8jádrové) | Řada B |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Rezervované počítače s Windows Serverem (1jádrové) | Všechny kromě řady B |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Rezervované počítače s Windows Serverem (2jádrové) | Všechny kromě řady B |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Rezervované počítače s Windows Serverem (4jádrové) | Všechny kromě řady B |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Rezervované počítače s Windows Serverem (6jádrové) | Všechny kromě řady B |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Rezervované počítače s Windows Serverem (8jádrové) | Všechny kromě řady B |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Rezervované počítače s Windows Serverem (12jádrové) | Všechny kromě řady B |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Rezervované počítače s Windows Serverem (16jádrové) | Všechny kromě řady B |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Rezervované počítače s Windows Serverem (20jádrové) | Všechny kromě řady B |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Rezervované počítače s Windows Serverem (24jádrové) | Všechny kromě řady B |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Rezervované počítače s Windows Serverem (32jádrové) | Všechny kromě řady B |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Rezervované počítače s Windows Serverem (40jádrové) | Všechny kromě řady B |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Rezervované počítače s Windows Serverem (64jádrové) | Všechny kromě řady B |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Rezervované počítače s Windows Serverem (72jádrové) | Všechny kromě řady B |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Rezervované počítače s Windows Serverem (128jádrové) | Všechny kromě řady B |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Rezervované počítače s Windows Serverem (256jádrové) | Všechny kromě řady B |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Rezervované počítače s Windows Serverem (96jádrové) | Všechny kromě řady B |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>Softwarové měřiče cloudových služeb nezahrnuté v nákladech na rezervaci

| MeterId | Název měřiče v souboru využití |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services – licence pro 1 vCPU|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services – licence pro 2 vCPU|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services – licence pro 4 vCPU|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services – licence pro 8 vCPU|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services – licence pro 16 vCPU|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services – licence pro 20 vCPU|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services – licence pro 32 vCPU|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services – licence pro 64 vCPU|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services – licence pro 80 vCPU|

## <a name="get-rates-for-azure-meters"></a>Získat sazby za měřiče Azure

Náklady na každý z těchto měřičů můžete získat prostřednictvím rozhraní Azure RateCard API. Informace o tom, jak získat sazby za měřiče Azure, najdete v popisu [získání informací o cenách a metadatech pro prostředky používané v rámci předplatného Azure](/previous-versions/azure/reference/mt219004(v=azure.100)).

## <a name="next-steps"></a>Další kroky
Další informace o rezervacích pro Azure najdete v následujících článcích:

- [Co jsou rezervace v Azure?](save-compute-costs-reservations.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Správa rezervací v Azure](manage-reserved-vm-instance.md)
- [Vysvětlení způsobu uplatnění slevy za rezervaci](../manage/understand-vm-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).