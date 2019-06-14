---
title: Sleva plán softwaru – Azure | Dokumentace Microsoftu
description: Zjistěte, jak software plán slevy pro software na virtuálních počítačích.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2019
ms.author: banders
ms.openlocfilehash: bcbf5ab48f3476a911fc4ade1eb0c395fb335d43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60370219"
---
# <a name="azure-software-plan-discount"></a>Slevy plán softwaru Azure

Plány Azure software SUSE a Red Hat jsou rezervace, které platí pro nasazené virtuální počítače. Sleva plán softwaru se použijí pro využití softwaru nasazených virtuálních počítačů, které odpovídají rezervace.

Při vypnutí virtuálního počítače se automaticky sleva k jinému odpovídající virtuálnímu počítači, pokud je k dispozici. Plán softwaru zahrnují náklady na provozování softwaru na virtuálním počítači. Další poplatky, jako jsou výpočty, úložiště a sítě se účtují samostatně.

Koupit správného plánu, musíte pochopit využívání virtuálních počítačů a počet virtuálních procesorů v těchto virtuálních počítačů. Použijte následující části vám pomůže identifikovat, co chcete koupit, podle data o využití.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva ve výši rezervace

Sleva za rezervaci se "*použití – it nebo ztratit – it*". Ano Pokud nemáte odpovídající prostředky pro hodinu, potom ztratíte množství rezervací pro určitou hodinu. Nelze provést vpřed nevyužité vyhrazené hodin.

Při vypínání prostředek sleva za rezervaci automaticky použije další odpovídající prostředek v zadaném oboru. Pokud systém nenašel žádné odpovídající prostředky v zadaném oboru, pak jsou vyhrazené hodin *ztráty*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Zkontrolujte využití virtuálních počítačů Red Hat před zakoupením

Získejte název produktu od data o využití a koupit plán Red Hat pomocí stejného typu a velikosti.

Například, pokud vaše použití má produkt **Red Hat Enterprise Linux - 1-4 virtuální procesory licence virtuálního počítače**, byste si zakoupit **Red Hat Enterprise Linux** pro **1 – 4 virtuální procesor virtuálního počítače**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Zkontrolujte využití virtuálního počítače SUSE před zakoupením

Získejte název produktu od data o využití a koupit plán SUSE pomocí stejného typu a velikosti.

Například, pokud je využití produktu **operačním systémem SUSE Linux Enterprise Server Priorita – 2 až 4 virtuální procesory podpora virtuálních počítačů**, byste si zakoupit **operačním systémem SUSE Linux Enterprise Server Priority** pro **2 – 4 virtuální procesory**.

![Příklad výběru produktu k nákupu](./media/billing-understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Sleva za vztahuje na různé velikosti virtuálních počítačů pro plány SUSE

Jako rezervované instance virtuálních počítačů, SUSE plánování nákupy nabízejí flexibilitu velikost instance. To znamená, že slevy platí i při nasazení virtuálního počítače se počet různých virtuálních procesorů. Sleva se vztahuje na různé velikosti virtuálních počítačů v rámci plánu softwaru.

Sleva závisí na poměr uvedené v následujících tabulkách. Poměr porovnává nároky na relativní pro každého měřiče v této skupině. Poměr závisí na virtuální procesory virtuálních počítačů. Hodnota poměr použijte k výpočtu určete počet instancí virtuálního počítače získat slevu plán operačním systémem SUSE Linux.

Například pokud jste koupit plán pro SUSE Linux Enterprise Server pro HPC Priority pro virtuální počítač s 3 nebo 4 virtuální procesory, poměr pro tuto rezervaci je 2. Sleva zahrnují náklady na software SUSE pro:

- 2 nasazené virtuální počítače s 1 nebo 2 virtuální procesory
- 1 virtuální počítač nasazen s 3 nebo 4 virtuální procesory,
- nebo % 77 0.77 nebo o virtuální počítač s 5 nebo víc virtuálních procesorů.

Poměr 5 nebo víc virtuálních procesorů je 2.6. Proto rezervace pro SUSE s virtuálním Počítačem s 5 nebo víc virtuálních procesorů se týká pouze část náklady na software, což je přibližně % 77.

Následující tabulky popisují softwarové plány, které si můžete koupit rezervaci pro jejich využití měřičů a poměry pro každý.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>Server operačním systémem SUSE Linux Enterprise pro HPC Priority

Název na portálu Azure marketplace:

- SLES 12 SP3 pro prostředí HPC (priorita)

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče| Poměr| Příklad velikost virtuálního počítače|
| -------| ------------------------| --- |--- |
|SLES pro HPC 1 – 2 virtuální procesory|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES pro HPC 3-4 virtuální procesory|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES pro HPC 5 + virtuálních procesorů|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>Server operačním systémem SUSE Linux Enterprise pro HPC Standard

Název na portálu Azure marketplace:

- SLES 12 SP3 pro prostředí HPC

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- | --- | ------------------------| --- |
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
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|VIRTUÁLNÍ POČÍTAČ SUSE | ID měřiče | Poměr|Příklad velikost virtuálního počítače|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 – 4 virtuální procesory |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 – 4 virtuální procesory |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|Virtuální procesory SLES 6 |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 virtuálních procesorů |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|Jader SLES 12 virtuálních procesorů |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 virtuálních procesorů |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 virtuálních procesorů |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|Počet jader SLES 24 virtuálních procesorů |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
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
|Virtuální procesory jader SLES 1 – 2 |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|Počet jader SLES 3-4 virtuální procesory |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + virtuálních procesorů |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další postup

Další informace o rezervacích, naleznete v následujících článcích:

- [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
- [Předplatíte plány softwaru SUSE s Azure rezervace](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Správa rezervací Azure](billing-manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
