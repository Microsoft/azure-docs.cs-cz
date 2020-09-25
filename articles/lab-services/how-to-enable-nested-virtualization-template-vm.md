---
title: Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services | Microsoft Docs
description: V tomto článku se dozvíte, jak nastavit vnořenou virtualizaci na počítači šablony v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91251470"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Povolit vnořenou virtualizaci na virtuálním počítači šablony v Azure Lab Services

V současné době vám Azure Lab Services umožňuje nastavit jeden virtuální počítač šablony v testovacím prostředí a vytvořit pro každého uživatele jednu kopii. Pokud jste Educator výukové sítě, zabezpečení nebo třídy IT, možná budete muset každému studentovi poskytnout prostředí, ve kterém může vzájemně komunikovat více virtuálních počítačů přes síť.

Vnořená virtualizace umožňuje vytvořit prostředí s několika virtuálními počítači v rámci virtuálních počítačů šablon testovacího prostředí. Publikování šablony poskytne každého uživatele v testovacím prostředí s virtuálním počítačem nastaveným s několika virtuálními počítači v rámci něj.  Tento článek popisuje, jak nastavit vnořenou virtualizaci na počítači šablony v Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Co je vnořená virtualizace?

Vnořená virtualizace umožňuje vytvářet virtuální počítače v rámci virtuálního počítače. Vnořená virtualizace se provádí prostřednictvím technologie Hyper-V a je dostupná jenom na virtuálních počítačích s Windows.

Další informace o vnořené virtualizaci naleznete v následujících článcích:

- [Vnořená virtualizace v Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak povolit vnořenou virtualizaci na virtuálním počítači Azure](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Požadavky

Před nastavením testovacího prostředí s vnořenou virtualizací je zde několik věcí, které je potřeba vzít v úvahu.

- Při vytváření nového testovacího prostředí vyberte **střední (vnořená virtualizace)** nebo **velké (vnořené virtualizace)** velikosti pro velikost virtuálního počítače. Tyto velikosti virtuálních počítačů podporují vnořenou virtualizaci.
- Vyberte velikost, která bude poskytovat dobrý výkon pro hostitele i klientské virtuální počítače.  Pamatujte si, že při použití virtualizace musí být zvolená velikost dostačující pro nejen jeden počítač, ale hostitel i všechny počítače s technologií Hyper-V běžící současně.
- Klientské virtuální počítače nebudou mít přístup k prostředkům Azure, jako jsou servery DNS, ve službě Azure Virtual Network.
- Hostitelský virtuální počítač vyžaduje Instalační program, aby mohl klientský počítač mít připojení k Internetu.
- Klientské virtuální počítače jsou licencované jako nezávislé počítače. Informace o licencování pro operační systémy a produkty společnosti Microsoft najdete v tématu věnovaném [licencování společnosti Microsoft](https://www.microsoft.com/licensing/default) . Před nastavením počítače šablony se podívejte na licenční smlouvy pro jakýkoli jiný software, který se používá.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Povolení vnořené virtualizace v šabloně virtuálního počítače

V tomto článku se předpokládá, že jste vytvořili účet testovacího prostředí a testovací prostředí.  Další informace o vytvoření nového účtu testovacího prostředí najdete v [kurzu nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md). Další informace o tom, jak vytvořit testovací prostředí, najdete v tématu [Nastavení kurzu pro prostředí učebny](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Při vytváření testovacího prostředí vyberte možnost **Velká (vnořená virtualizace)** nebo **střední (vnořená virtualizace)** pro velikost virtuálního počítače.  Vnořená virtualizace nebude jinak fungovat.  

Chcete-li se připojit k počítači šablony, přečtěte si téma [Vytvoření a Správa šablony učebny](how-to-create-manage-template.md).

Pokud chcete povolit vnořenou virtualizaci, je potřeba provést několik úkolů.  

- **Povolte roli Hyper-V**. Pro vytváření a spouštění virtuálních počítačů Hyper-V na virtuálním počítači služby testovacího prostředí musí být povolená role Hyper-V.
- **Povolte protokol DHCP**.  Pokud má virtuální počítač služby testovacího prostředí povolenou roli DHCP, může být virtuálním počítačům technologie Hyper-V automaticky přiřazena IP adresa.
- **Vytvořte síť NAT pro virtuální počítače Hyper-V**.  Síť NAT je nastavená tak, aby virtuální počítače Hyper-V mohly mít přístup k Internetu.  Virtuální počítače s technologií Hyper-V můžou vzájemně komunikovat.

>[!NOTE]
>Síť NAT vytvořená na virtuálním počítači služby testovacího prostředí umožní virtuálnímu počítači Hyper-V přístup k Internetu a dalším virtuálním počítačům Hyper-V na stejném virtuálním počítači služby testovacího prostředí.  Virtuální počítač Hyper-V nebude mít přístup k prostředkům Azure, jako jsou servery DNS, ve službě Azure Virtual Network.

Provedení výše uvedených úkolů se dá provést pomocí skriptu nebo pomocí nástrojů Windows.  Další podrobnosti najdete v částech níže.

### <a name="using-script-to-enable-nested-virtualization"></a>Použití skriptu pro povolení vnořené virtualizace

Postup použití automatizované instalace pro vnořenou virtualizaci s Windows serverem 2016 nebo Windows serverem 2019 najdete v tématu [Povolení vnořené virtualizace na virtuálním počítači šablony v Azure Lab Services pomocí skriptu](how-to-enable-nested-virtualization-template-vm-using-script.md). K instalaci role Hyper-V budete používat skripty ze [skriptů technologie Hyper-v služby Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) .  Skripty budou také nastavovat sítě, aby virtuální počítače Hyper-V mohly mít přístup k Internetu.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Povolení vnořené virtualizace pomocí nástrojů Windows

Nastavení vnořená virtualizace pro Windows Server 2016 nebo Windows Server 2019 pomocí rolí Windows a nástrojů pro správu najdete v tématu [Povolení vnořené virtualizace na virtuálním počítači šablony v Azure Lab Services ručně](how-to-enable-nested-virtualization-template-vm-ui.md).  Návod také popisuje, jak nastavit síť, aby virtuální počítače Hyper-V mohly mít přístup k Internetu.
