---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269658"
---
## <a name="scenario"></a>Scénář
Virtuální počítač s jedním Síťovým vytvoření a připojení k virtuální síti. Virtuální počítač vyžaduje tři různé *privátní* IP adresy a dva *veřejné* IP adresy. IP adresy jsou přiřazeny k následující konfigurace protokolu IP:

* **IPConfig-1:** přiřadí *statické* privátní IP adresu a *statické* veřejnou IP adresu.
* **IPConfig – 2:** přiřadí *statické* privátní IP adresu a *statické* veřejnou IP adresu.
* **IPConfig-3:** přiřadí *statické* privátní IP adresu a žádná veřejná IP adresa.
  
    ![Několik IP adres](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Konfigurace protokolu IP jsou přidružené k síťové KARTĚ, při vytvoření síťového adaptéru a síťového rozhraní je připojen k virtuálnímu počítači při vytvoření virtuálního počítače. Typy IP adresy používané pro tento scénář jsou ukázky. Můžete přiřadit libovolné IP adresy a přiřazení typy budete potřebovat.

> [!NOTE]
> Přestože postup v tomto článku přiřadí všechny konfigurace protokolu IP pro jednu síťovou kartu, můžete také přiřadit více konfigurací protokolu IP na všechny síťové adaptéry na virtuálním počítači s více síťovými Kartami. Zjistěte, jak vytvořit virtuální počítač s několika síťovými kartami, přečtěte si [vytvoření virtuálního počítače s několika síťovými kartami](../articles/virtual-machines/windows/multiple-nics.md) článku.