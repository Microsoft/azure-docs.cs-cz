---
title: Sleva za plán softwaru – Azure | Microsoft Docs
description: Zjistěte, jak se na software na virtuálních počítačích uplatňují slevy za plán softwaru.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: banders
ms.openlocfilehash: 2c17b2f9dc12fa7bff2dae8603bcf95a3a8ddfa0
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350836"
---
# <a name="azure-software-plan-discount"></a>Sleva za plán softwaru Azure

Plány softwaru Azure pro SUSE a RedHat jsou rezervace, které platí pro nasazené virtuální počítače. Sleva za plán softwaru se uplatňuje na využívání softwaru v nasazených virtuálních počítačích, které odpovídají rezervaci.

Když určitý virtuální počítač vypnete, sleva se automaticky použije na jiný odpovídající virtuální počítač, pokud takový existuje. Plán softwaru pokrývá náklady na provoz softwaru na virtuálním počítači. Další poplatky, například za výpočetní prostředky, úložiště a prostředky sítě, se účtují zvlášť.

Pokud si chcete zvolit správný plán, je potřeba nejprve porozumět využití vašich virtuálních počítačů a zjistit si počet vCPU na těchto virtuálních počítačích. Následující části vám na základě vašich dat o využití pomůžou určit, který plán si máte koupit.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

Nevyužitá sleva za rezervaci se *nenahrazuje*. Pokud tedy nemáte na každou hodinu odpovídající prostředky, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když vypnete určitý prostředek, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Kontrola využití virtuálních počítačů se softwarem RedHat před nákupem

V datech o využití si zjistěte název produktu a zakupte si plán softwaru RedHat stejného typu a velikosti.

Pokud například využíváte produkt **Red Hat Enterprise Linux – licence na virtuální počítač s 1–4 vCPU**, měli byste si zakoupit **Red Hat Enterprise Linux** pro **virtuální počítač s 1–4 vCPU**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Kontrola využití virtuálních počítačů se softwarem SUSE před nákupem

V datech o využití si zjistěte název produktu a zakupte si plán softwaru SUSE stejného typu a velikosti.

Pokud například využíváte produkt **SUSE Linux Enterprise Server Priority – podpora virtuálních počítačů s 2–4 vCPU**, měli byste si zakoupit **SUSE Linux Enterprise Server Priority** pro **2–4 vCPU**.

![Příklad výběru produktu k zakoupení](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>U plánů SUSE se sleva vztahuje na různé velikosti virtuálních počítačů

Plány SUSE, podobně rezervované instance virtuálních počítačů, nabízejí flexibilitu velikosti instance. To znamená, že vaše sleva platí i v případě, že nasadíte virtuální počítač s jiným počtem vCPU. Sleva se vztahuje na různé velikosti virtuálních počítačů v rámci softwarového plánu.

Výše slevy závisí na koeficientu uvedeném v následujících tabulkách. Koeficient zohledňuje relativní využití paměti pro každý měřič v dané skupině. Koeficient závisí na počtu vCPU virtuálního počítače. Pomocí koeficientu můžete vypočítat, kolik instancí virtuálních počítačů bude mít nárok na slevu za plán SUSE.

Pokud si například zakoupíte plán softwaru SUSE Linux Enterprise Server for HPC Priority pro virtuální počítač se 3 nebo 4 vCPU, má daná rezervace koeficient 2. Sleva pokryje náklady na software SUSE pro:

- 2 nasazené virtuální počítače s 1 nebo 2 vCPU
- 1 nasazený virtuální počítač se 3 nebo 4 vCPU
- Nebo 0,77, tedy asi 77 % virtuálního počítače s 5 nebo více vCPU

Koeficient při 5 nebo více vCPU činí 2,6. Z toho důvodu rezervace softwaru SUSE pro virtuální počítač s 5 nebo více vCPU pokrývá jen určitou část nákladů na software, zhruba ve výši 77 %.

Následující tabulky představují softwarové plány, na které si můžete koupit rezervaci, související měřiče využití a příslušné koeficienty.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Název z Marketplace na webu Azure Portal:

- SLES 12 SP3 pro HPC (Priority)

|Virtuální počítač se softwarem SUSE | MeterId| Koeficient| Velikost ukázkového virtuálního počítače|
| -------| ------------------------| --- |--- |
|SLES pro HPC pro 1–2 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES pro HPC pro 3–4 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES pro HPC pro 5 a více vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Název z Marketplace na webu Azure Portal:

- SLES 12 SP3 pro HPC

|Virtuální počítač se softwarem SUSE | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- | --- | ------------------------| --- |
|SLES pro HPC pro 1–2 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES pro HPC pro 3–4 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SLES pro HPC pro 5 a více vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Názvy z Marketplace na webu Azure Portal:

- SLES pro SAP 15 (Priority)
- SLES pro SAP 12 SP3 (Priority)
- SLES pro SAP 12 SP2 (Priority)

|Virtuální počítač se softwarem SUSE | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- |------------------------| --- | --- |
|SLES pro SAP Priority pro 1–2 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES pro SAP Priority pro 3–4 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES pro SAP Priority pro 5 a více vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Názvy z Marketplace na webu Azure Portal:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|Virtuální počítač se softwarem SUSE | MeterId | Koeficient|Velikost ukázkového virtuálního počítače|
| ------- |------------------------| --- |--- |
|SLES pro 1–2 jádra vCPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES pro 3–4 jádra vCPU |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SLES pro 5 a více vCPU |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích najdete v následujících článcích:

- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
- [Předplacení plánů softwaru SUSE z rezervací Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)