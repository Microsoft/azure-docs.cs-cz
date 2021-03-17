---
title: Kurz pro připojení k Azure Stackmu hraničnímu Mini R v Azure Portal
description: Přečtěte si, jak se můžete připojit k Azure Stackmu minimu zařízení R pomocí místního webového uživatelského rozhraní.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: fe76391a5cfce8d7d39e47131db108ab87e5aed5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468230"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Kurz: připojení k Azure Stackmu hraničnímu minimu R

V tomto kurzu se dozvíte, jak se pomocí místního webového uživatelského rozhraní připojovat k vašemu Azure Stackho minimu zařízení R.

Dokončení procesu připojení může trvat přibližně 5 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady
> * Připojení k fyzickému zařízení



## <a name="prerequisites"></a>Předpoklady

Před konfigurací a nastavením Azure Stack hraničního zařízení se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Nakonfigurujte adaptér Ethernet v počítači pro připojení k zařízení Azure Stack Edge pro se statickou IP adresou 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu 1 na svém zařízení. Pokud počítač připojujete přímo k zařízení (bez přepínače), použijte křížený kabel nebo adaptér USB Ethernet. Pomocí následující ilustrace Identifikujte PORT 1 na vašem zařízení.

    ![Kabeláž pro Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Předpoklady
> * Připojení k fyzickému zařízení


Informace o tom, jak nakonfigurovat nastavení sítě v zařízení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace sítě](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
