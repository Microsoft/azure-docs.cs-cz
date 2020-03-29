---
title: Připojení k virtuálním počítačům prostřednictvím prohlížeče – Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak se připojit k virtuálním počítačům prostřednictvím prohlížeče.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974277"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Připojení k virtuálním počítačům prostřednictvím prohlížeče 

DevTest Labs se integruje s [Azure Bastion](https://docs.microsoft.com/azure/bastion/), který umožňuje připojení k virtuálním počítačům prostřednictvím prohlížeče. Informace o povolení této funkce v devTest Labs najdete v [tématu Povolení připojení prohlížeče na virtuálních počítačích testovacího prostředí](enable-browser-connection-lab-virtual-machines.md).

Jakmile je *připojení prohlížeče* povoleno, uživatelé testovacího prostředí mají přístup k virtuálním počítačům prostřednictvím prohlížeče.  

## <a name="create-a-lab-virtual-machine"></a>Vytvoření testovacího virtuálního počítače

Nejprve je třeba vytvořit virtuální počítač testovacího prostředí v rámci virtuální sítě, která má Bastion nakonfigurován na něm. Vyberte druhou **podsíť,** kterou jste vytvořili, ne AzureBastionSubnet. Virtuální síť můžete vybrat během vytváření virtuálního počítače tak, že přejdete na kartu **Upřesnit nastavení.**

![Vytvoření virtuálního počítače](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Spuštění virtuálního počítače v prohlížeči

Jakmile je virtuální počítač vytvořen, můžete jej spustit v prohlížeči kliknutím na tlačítko *Připojení prohlížeče* a zadáním uživatelského jména a hesla pro stroj.  

![Spuštění v prohlížeči](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Další kroky

[Přidání virtuálního počítače do testovacího prostředí v azure devtest labs](devtest-lab-add-vm.md)
