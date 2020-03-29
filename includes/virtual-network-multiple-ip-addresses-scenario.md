---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174929"
---
## <a name="scenario"></a>Scénář
Virtuální počítač s jednou samostatnou pomocí je vytvořen a připojen k virtuální síti. Virtuální ho virtuálního *private* počítačů mj. *public* Ip adresy jsou přiřazeny k následujícím konfiguracím IP:

* **PROTOKOL IPConfig-1:** Přiřadí *statickou* privátní IP adresu a *statickou* veřejnou IP adresu.
* **PROTOKOL IPConfig-2:** Přiřadí *statickou* privátní IP adresu a *statickou* veřejnou IP adresu.
* **PROTOKOL IPConfig-3:** Přiřadí *statickou* privátní IP adresu a žádnou veřejnou IP adresu.
  
    ![Několik IP adres](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Konfigurace IP jsou přidružené k nic při vytvoření nic a nic je připojen k virtuálnímu počítače při vytvoření virtuálního počítače. Typy ADRES IP použité pro scénář jsou pro ilustraci. Můžete přiřadit libovolné ip adresy a typy přiřazení, které požadujete.

> [!NOTE]
> Přestože kroky v tomto článku přiřadí všechny konfigurace IP jedné nic, můžete také přiřadit více konfigurací IP libovolné nic ve virtuálním počítači s více samostatnými mikonien. Pokud se chcete dozvědět, jak vytvořit virtuální počítač s více síťové karty, přečtěte si článek [Vytvořit virtuální počítač s více síťové karty.](../articles/virtual-machines/windows/multiple-nics.md)