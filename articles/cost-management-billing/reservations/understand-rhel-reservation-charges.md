---
title: Slevy rezervačních plánů pro Red Hat – Azure
description: Zjistěte, jak se na software Red Hat na virtuálních počítačích uplatňují slevy za plán Red Hat.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: a10e6204184f28dbb62083bc72c6963ea8db082b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568626"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Vysvětlení způsobu uplatňování slevy za plán rezervace softwaru Red Hat Linux Enterprise v Azure

Jakmile si zakoupíte plán Red Hat Linux, na nasazené virtuální počítače se softwarem Red Hat vyhovující rezervaci se začne automaticky uplatňovat sleva. Plán Red Hat Linux pokrývá náklady na chod softwaru Red Hat na virtuálním počítači Azure.

Pokud si chcete koupit správný plán Red Hat Linux, potřebujete mít přehled o tom, jaké virtuální počítače Red Hat používáte a jaký je počet vCPU na těchto virtuálních počítačích. Následující části vám na základě vašeho souboru CSV s informacemi o využití pomůžou určit, který plán si máte koupit.

## <a name="discount-applies-to-different-vm-sizes"></a>Sleva se vztahuje na různé velikosti virtuálních počítačů

Podobně jako je to u rezervovaných instancí virtuálních počítačů, i plány Red Hat nabízejí flexibilitu velikosti instance. To znamená, že vaše sleva platí i v případě, že nasadíte virtuální počítač s jiným počtem vCPU. Sleva se vztahuje na různé velikosti virtuálních počítačů v rámci softwarového plánu.

Výše slevy závisí na koeficientu uvedeném v následujících tabulkách. Koeficient zohledňuje relativní využití paměti pro každou velikost virtuálního počítače v dané skupině. Koeficient závisí na počtu vCPU virtuálního počítače. Pomocí koeficientu můžete vypočítat, kolik instancí virtuálních počítačů bude mít nárok na slevu za plán Red Hat Linux.

Pokud si například zakoupíte plán softwaru Red Hat Linux Enterprise Server pro virtuální počítač s 1 až 4 vCPU, má daná rezervace koeficient 1. Sleva pokryje náklady na software Red Hat pro:

- 1 nasazený virtuální počítač s 1 až 4 vCPU
- nebo 0,46 (asi 46 %) nákladů na Red Hat Enterprise Linux pro virtuální počítač s 5 nebo více vCPU

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Názvy z Marketplace na webu Azure Portal:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2

[Kontrola, na které měřiče Red Hat Enterprise Linuxu se plán vztahuje](https://phoenixnap.com/kb/how-to-check-redhat-version)

## <a name="next-steps"></a>Další kroky

Další informace o rezervacích najdete v následujících článcích:

- [Co jsou rezervace v Azure?](save-compute-costs-reservations.md)
- [Předplacení plánů softwaru Red Hat pomocí rezervací Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Předplacení virtuálních počítačů se službou Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Správa rezervací v Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
