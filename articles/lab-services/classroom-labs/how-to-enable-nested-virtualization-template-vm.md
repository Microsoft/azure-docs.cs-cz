---
title: Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit virtuální počítač šablony s více virtuálními počítači uvnitř.  Jinými slovy povolte vnořenou virtualizaci na virtuálním počítači šablony ve službě Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502012"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services

V současné době Azure Lab Services umožňuje nastavit jednu šablonu virtuálního počítače v testovacím prostředí a zpřístupnit jednu kopii pro každého z vašich uživatelů. Pokud jste profesor výuky sítí, zabezpečení nebo IT třídy, možná budete muset poskytnout každému z vašich studentů prostředí, ve kterém více virtuálních počítačů může mluvit mezi sebou prostřednictvím sítě.

Vnořená virtualizace umožňuje vytvořit prostředí s více virtuálními počítači uvnitř virtuálního počítače šablony testovacího prostředí. Publikování šablony poskytne každému uživateli v testovacím prostředí virtuální počítač nastavený s více virtuálními počítači v něm.  Tento článek popisuje, jak nastavit vnořené virtualizace na počítači šablony ve službě Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Co je vnořená virtualizace?

Vnořená virtualizace umožňuje vytvářet virtuální počítače v rámci virtuálního počítače. Vnořená virtualizace se provádí prostřednictvím technologie Hyper-V a je k dispozici pouze na virtuálních počítačích se systémem Windows.

Další informace o vnořené virtualizaci najdete v následujících článcích:

- [Vnořená virtualizace v Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak povolit vnořenou virtualizaci ve virtuálním počítači Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Požadavky

Před nastavením testovacího prostředí s vnořenou virtualizací, zde je několik věcí, které je třeba vzít v úvahu.

- Při vytváření nového testovacího prostředí vyberte **střední (vnořená virtualizace)** nebo **velké (vnořené virtualizace)** velikosti pro velikost virtuálního počítače. Tyto velikosti virtuálních strojů podporují vnořenou virtualizaci.
- Zvolte velikost, která bude poskytovat dobrý výkon pro virtuální počítače hostitele i klienta.  Nezapomeňte, že při použití virtualizace musí být velikost, kterou zvolíte, dostatečná nejen pro jeden počítač, ale i pro hostitele, stejně jako všechny klientské počítače, které musí být spuštěny souběžně.
- Klientské virtuální počítače nebudou mít přístup k prostředkům Azure, jako jsou servery DNS ve virtuální síti Azure.
- Hostitelský virtuální počítač vyžaduje nastavení, které umožní, aby klientský počítač měl připojení k internetu.
- Klientské virtuální počítače jsou licencovány jako nezávislé počítače. Informace o licencování operačních systémů a produktů společnosti Microsoft naleznete v licenčních službách společnosti [Microsoft.](https://www.microsoft.com/licensing/default) Před nastavením počítače se šablonami zkontrolujte licenční smlouvy pro jakýkoli jiný software používaný.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Povolení vnořené virtualizace v šabloně virtuálního počítače

Tento článek předpokládá, že jste vytvořili účet testovacího prostředí a testovacího prostředí.  Další informace o vytvoření nového účtu testovacího prostředí naleznete [v kurzu k nastavení účtu testovacího prostředí](tutorial-setup-lab-account.md). Další informace o vytvoření testovacího prostředí naleznete [v tématu nastavení kurzu pro testovací prostředí ve třídě](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Vyberte **Velké (vnořené virtualizace)** nebo **Střední (vnořené virtualizace)** pro velikost virtuálního počítače při vytváření testovacího prostředí.  Vnořená virtualizace nebude fungovat jinak.  

Pokud se chcete připojit k počítači se [šablonami, přečtěte si informace o vytvoření a správě šablony učebny](how-to-create-manage-template.md).

### <a name="using-script-to-enable-nested-virtualization"></a>Povolení vnořené virtualizace pomocí skriptu

Pokud chcete použít automatické nastavení pro vnořenou virtualizaci se systémem Windows Server 2016 nebo Windows Server 2019, přečtěte si informace [o povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services pomocí skriptu](how-to-enable-nested-virtualization-template-vm-using-script.md). K instalaci role Hyper-V budete používat skripty ze [skriptů Lab Services Hyper-V.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)  Skripty také nastaví síť, aby virtuální počítače Hyper-V mohly mít přístup k internetu.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Povolení vnořené virtualizace pomocí nástrojů systému Windows

Nastavení vnořené virtualizace pro Windows Server 2016 nebo Windows Server 2019 pomocí rolí Windows a nástrojů pro správu, najdete v [tématu Povolení vnořené virtualizace na virtuálním počítači šablony ve službě Azure Lab Services ručně](how-to-enable-nested-virtualization-template-vm-ui.md).  Pokyny budou také zahrnovat nastavení sítě tak, aby virtuální počítače Hyper-V mohly mít přístup k internetu.
