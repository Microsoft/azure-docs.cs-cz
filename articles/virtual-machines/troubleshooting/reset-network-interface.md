---
title: Resetování síťové rozhraní pro virtuální počítač Windows Azure | Dokumentace Microsoftu
description: Ukazuje, jak resetování síťové rozhraní virtuálního počítače Windows Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 23cf02e8cc33b3a66a04ae0472b1e5a6baa59cc2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418989"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Resetování síťové rozhraní pro virtuální počítač Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Poté, co jste zakázali výchozí síťové rozhraní (NIC) nebo ručně nastaví statické IP adresy pro síťové rozhraní, nemůžete se připojit k Microsoft Azure Windows virtuální počítač (VM) Tento článek ukazuje, jak resetování síťové rozhraní virtuálního počítače Windows Azure, která tento problém odstraní vzdáleného připojení.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Resetování síťové rozhraní

### <a name="for-classic-vms"></a>Pro klasické virtuální počítače

Resetování síťové rozhraní, postupujte podle těchto kroků:

1.  Přejděte na [Azure Portal]( https://ms.portal.azure.com).
2.  Vyberte **virtuální počítače (Classic)**.
3.  Vyberte ovlivněné virtuální počítač.
4.  Vyberte **IP adresy**.
5.  Pokud **přiřazení privátní IP adresy** není **statické**, změňte ho na **statické**.
6.  Změnit **IP adresu** na jinou IP adresu, která je dostupná v podsíti.
7.  Vyberte Uložit.
8.  Virtuální počítač se restartuje za účelem inicializace nového síťového rozhraní v systému.
9.  Zkuste RDP k vašemu počítači. V případě úspěchu, můžete změnit privátní IP adresu zpět na původní Pokud byste o ni. V opačném případě ho nechat. 

### <a name="for-vms-deployed-in-resource-group-model"></a>Pro virtuální počítače nasazené v modelu skupiny prostředků

1.  Přejděte na [Azure Portal]( https://ms.portal.azure.com).
2.  Vyberte ovlivněné virtuální počítač.
3.  Vyberte **síťová rozhraní**.
4.  Vyberte síťové rozhraní přidružené k počítači
5.  Vyberte **konfigurací protokolu IP**.
6.  Vyberte IP adresu. 
7.  Pokud **přiřazení privátní IP adresy** není **statické**, změňte ho na **statické**.
8.  Změnit **IP adresu** na jinou IP adresu, která je dostupná v podsíti.
9. Virtuální počítač se restartuje za účelem inicializace nového síťového rozhraní v systému.
10. Zkuste RDP k vašemu počítači. V případě úspěchu, můžete změnit privátní IP adresu zpět na původní Pokud byste o ni. V opačném případě ho nechat. 

## <a name="delete-the-unavailable-nics"></a>Odstranění není k dispozici síťové karty
Poté, co je to možné k počítači pomocí vzdálené plochy, je potřeba odstranit staré síťové karty, aby se zabránilo případným problémům:

1.  Spustit nástroj Správce zařízení.
2.  Vyberte **zobrazení** > **zobrazit skrytá zařízení**.
3.  Vyberte **síťové adaptéry**. 
4.  Zkontrolujte adaptéry s názvem "Síťový adaptér Microsoft Hyper-V".
5.  Možná se šedě zobrazí nedostupný adaptér. Klikněte pravým tlačítkem na adaptéru a vyberte odinstalovat.

    ![Obrázek karty síťového rozhraní](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Není k dispozici adaptéry, které mají název "Síťový adaptér Microsoft Hyper-V" pouze odinstalujte. Pokud provádíte odinstalaci všech skryté adaptérů, mohlo by dojít ke další problémy.
    >
    >

6.  Nyní všechny nedostupné adaptéry měly být odstraněné z vašeho systému.
