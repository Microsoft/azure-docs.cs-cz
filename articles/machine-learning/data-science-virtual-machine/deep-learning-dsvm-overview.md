---
title: Seznámení s virtuálním počítačem s hloubkovým učením – Azure | Microsoft Docs
description: Klíčové analytické scénáře a komponenty pro Virtual Machines s hloubkovým učením.
keywords: obsáhlý Learning, AI, nástroje pro datové vědy, virtuální počítač pro datové vědy, nástroje pro datové vědy, datové vědy pro Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 8541713219eb8daf880605089fe49fc585836bdc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534952"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Seznámení s virtuálním počítačem s hloubkovým učením

> [!NOTE]
> Všechny nástroje pro virtuální počítače s hlubokým učením (DLVM) jsou teď přeložené do [Data Science VM (DSVM)](dsvm-tools-overview.md). DLVM a DSVM jsou nyní stejné. Místo DLVM prosím zvažte vytvoření DSVM.

## <a name="why-deep-learning-virtual-machine"></a>Proč se virtuální počítač s hloubkovým učením? 

Stále se objevují algoritmy hloubkového učení nebo rozsáhlé neuronové sítě, které se v mnoha problémech se strojovým učením vycházejí z oblíbených metod. Jsou obzvláště dobré v Cognition úlohách, jako je obrázek, text, zvuk/video, porozumění často se přibližuje k úrovním lidského vnímání v některých specifických doménách s pokročilými neuronové síťovými architekturami a přístupem k velké sadě dat pro výukové modely. Obsáhlý Learning vyžaduje velké množství výpočetního výkonu pro výuku modelů s těmito velkými datovými sadami. Díky cloudu a dostupnosti grafických procesorů (GPU) je možné vytvořit sofistikované architektury hloubkové neuronové a naučit se je ve velké sadě dat na výkonné výpočetní infrastruktuře v cloudu.  [Data Science Virtual Machine](overview.md) poskytuje bohatou sadu nástrojů a ukázek pro přípravu dat, strojové učení a rozsáhlou výuku. Ale jedním z výzev, kterým uživatelé čelí, je zjistit nástroje a ukázky pro konkrétní scénáře, jako je obsáhlý Learning, a taky snadněji zřídit instance virtuálních počítačů na bázi GPU. Tento virtuální počítač s hlubokou výukou (DLVM) tyto výzvy řeší. 

## <a name="what-is-deep-learning-virtual-machine"></a>Co je to virtuální počítač s hloubkovým učením? 
Virtuální počítač s hloubkovým učením je speciálně nakonfigurovaná varianta [Data Science Virtual Machine](overview.md) (DSVM), která usnadňuje používání instancí virtuálních počítačů založených na GPU pro školení modelů pro obsáhlý Learning. Podporuje se ve Windows 2016 a Ubuntu Data Science Virtual Machine.  Sdílí stejné základní image virtuálních počítačů (a tedy celou bohatou sadu nástrojů) jako DSVM, ale je nakonfigurovaná tak, aby usnadnila hloubkové učení. Poskytujeme také ucelené ukázky pro obrázky a porozumění textu, které se dají široce použít na mnoho scénářů pro čtení AI z reálného života. Virtuální počítač s hloubkovým učením se taky snaží vytvořit bohatou sadu nástrojů a ukázek na DSVM snáze zjistitelné zpřístupnění katalogu nástrojů a ukázek na virtuálním počítači. V souvislosti s nástroji poskytuje virtuální počítač pro obsáhlý Learning několik oblíbených architektur hloubkového učení, nástroje pro získání a předzpracování imagí a textových dat. Úplný seznam nástrojů najdete na [stránce přehled Data Science Virtual Machine](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Další kroky

Začněte s virtuálním počítačem s hloubkovým učením pomocí následujících kroků:

* [Zřízení Data Science Virtual Machine Windows](provision-vm.md)
* [Zřízení Data Science Virtual Machine Ubuntu](dsvm-ubuntu-intro.md)

