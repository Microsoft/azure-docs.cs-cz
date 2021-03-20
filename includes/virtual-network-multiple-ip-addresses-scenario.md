---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "67174929"
---
## <a name="scenario"></a>Scenario
Vytvoří se virtuální počítač s jedním síťovým ADAPTÉRem a připojí se k virtuální síti. Virtuální počítač vyžaduje tři různé *privátní* IP adresy a dvě *veřejné* IP adresy. IP adresy jsou přiřazené k následujícím konfiguracím protokolu IP:

* **Ipconfig-1:** Přiřadí *statickou* privátní IP adresu a *statickou* veřejnou IP adresu.
* **Ipconfig-2:** Přiřadí *statickou* privátní IP adresu a *statickou* veřejnou IP adresu.
* **Ipconfig-3:** Přiřadí *statickou* privátní IP adresu bez veřejné IP adresy.
  
    ![Několik IP adres](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Konfigurace IP adres se k síťovému rozhraní připojí, když se vytvoří síťová karta a síťový adaptér je připojený k virtuálnímu počítači, když se vytvoří virtuální počítač. Typy IP adres, které se používají pro daný scénář, jsou určené pro ilustraci. Můžete přiřadit libovolné typy IP adres a přiřazení, které požadujete.

> [!NOTE]
> I když kroky v tomto článku přiřadí všechny konfigurace IP adresy jedinému síťovému rozhraní, můžete také přiřadit více konfigurací IP adres ke kterékoli síťové kartě ve virtuálním počítači s více síťovými kartami. Pokud chcete zjistit, jak vytvořit virtuální počítač s více síťovými rozhraními, přečtěte si článek [Vytvoření virtuálního počítače s několika síťovými](../articles/virtual-machines/windows/multiple-nics.md) kartami.