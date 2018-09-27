---
title: Pochopit SUSE plán slevy a využití - rezervace Azure | Dokumentace Microsoftu
description: Zjistěte, jak SUSE plán slevy pro softwaru SUSE na virtuálních počítačích.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: cwatson
ms.openlocfilehash: 62f75be44ed92528b48fc0f093f5966284662312
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393685"
---
# <a name="understand-how-the-suse-linux-enterprise-software-plan-discount-is-applied"></a>Vysvětlení, jak se použije slevu plán softwaru SUSE Linux Enterprise

Po nákupu plánu operačním systémem SUSE Linux se automaticky využije slevy na nasazených SUSE virtuálních počítačů (VM), které odpovídají rezervace. Plán pro SUSE Linux zahrnují náklady na provozování softwaru SUSE na Virtuálním počítači Azure.

Koupit správného plánu operačním systémem SUSE Linux, musíte pochopit, jaké virtuální počítače SUSE spustíte a počet virtuálních procesorů v těchto virtuálních počítačů. Použijte následující části vám pomůže identifikovat ze souboru CSV využití, co chcete koupit.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>Sleva za vztahuje na různé velikosti virtuálních počítačů s flexibilitou velikost instance

Jako rezervované instance virtuálních počítačů, SUSE plánování nákupy nabízejí flexibilitu velikost instance. To znamená, že slevy platí i při nasazení virtuálního počítače se počet různých virtuálních procesorů. Sleva se vztahuje na různé velikosti virtuálních počítačů v rámci plánu softwaru.

Sleva závisí na poměr uvedené v následujících tabulkách. Poměr porovnává nároky na relativní pro každého měřiče v této skupině. Poměr závisí na virtuální procesory virtuálních počítačů. Hodnota poměr použijte k výpočtu určete počet instancí virtuálního počítače získat slevu plán operačním systémem SUSE Linux.

Například pokud jste koupit plán pro SUSE Linux Enterprise Server pro HPC Priority pro virtuální počítač s 3 nebo 4 virtuální procesory, poměr pro tuto rezervaci je 2. Sleva zahrnují náklady na software SUSE pro:

- 2 nasazené virtuální počítače s 1 nebo 2 virtuální procesory
- 1 virtuální počítač nasazen s 3 nebo 4 virtuální procesory,
- nebo % 77 0.77 nebo o virtuální počítač s 5 nebo víc virtuálních procesorů.

Poměr 5 nebo víc virtuálních procesorů je 2.6. Proto rezervace pro SUSE s virtuálním Počítačem s 5 nebo víc virtuálních procesorů se týká pouze část náklady na software, což je přibližně % 77.

## <a name="understand-your-suse-vm-usage-before-buying-a-suse-linux-plan"></a>Vysvětlení využití virtuálního počítače SUSE před zakoupením plán operačním systémem SUSE Linux

Následující tabulky popisují softwarové plány, které si můžete koupit rezervaci pro jejich využití měřičů a poměry pro každý.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>Server operačním systémem SUSE Linux Enterprise pro HPC Priority

Název na portálu Azure marketplace:

- SLES 12 SP3 pro prostředí HPC (priorita)

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče| Poměr| Příklad velikost virtuálního počítače|
| -------| ------------------------| --- |--- |
|SLES pro HPC 1 – 2 virtuální procesory|e275a668-CE79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES pro HPC 3-4 virtuální procesory|e531e1c0-09c9-4d83-B7D0-a2c6741faa22|2|D4s_v3|
|SLES pro HPC 5 + virtuálních procesorů|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>Server operačním systémem SUSE Linux Enterprise pro HPC Standard

Název na portálu Azure marketplace:

- SLES 12 SP3 pro prostředí HPC

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- | --- | ------------------------| --- | --- |
|SLES pro HPC 1 – 2 virtuální procesory |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES pro HPC 3-4 virtuální procesory|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES pro HPC 5 + virtuálních procesorů |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Azure portal marketplace názvy:

- SLES for SAP 15 (priorita)
- SLES pro SAP 12 SP3 (priorita)
- SLES pro SAP 12 SP2 (priorita)

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- | --- |
|SLES pro SAP Priority 1 – 2 virtuální procesory|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES pro SAP Priority 3-4 virtuální procesory |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES pro SAP Priority 5 + virtuálních procesorů |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>Priorita operačním systémem SUSE Linux Enterprise Server

Azure portal marketplace názvy:

- SLES 15 (PRIORITA)
- SLES 12 SP3 (priorita)
- SLES 11 SP4 (priorita)

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 – 4 virtuální procesory |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 – 4 virtuální procesory |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|Virtuální procesory SLES 6 |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 virtuálních procesorů |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|Jader SLES 12 virtuálních procesorů |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 virtuálních procesorů |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 virtuálních procesorů |c5228804-1de6-4BD4-a61c-501d9003acc8|3.2| |
|Počet jader SLES 24 virtuálních procesorů |-005 d 4075 ac11 822ccde9e8f6|3.2| ND24s|
|SLES 32 virtuálních procesorů |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|Počet jader SLES 40 virtuálních procesorů |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 virtuálních procesorů |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 jader virtuálních procesorů |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 jader virtuálních procesorů |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 jader vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Azure portal marketplace názvy:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- |--- |
|Virtuální procesory jader SLES 1 – 2 |4b2fecfc-B110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|Počet jader SLES 3-4 virtuální procesory |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + virtuálních procesorů |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="next-steps"></a>Další postup

Další informace o rezervacích, naleznete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplatíte plány softwaru SUSE s Azure rezervace](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Předplatit si virtuální počítače se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud máte další otázky, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.