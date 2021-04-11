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
ms.openlocfilehash: 57d11e1c4109caded4287592a6d71a803e44b68e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061600"
---
# <a name="tutorial-connect-to-azure-stack-edge-mini-r"></a>Kurz: připojení k Azure Stackmu hraničnímu minimu R

V tomto kurzu se dozvíte, jak se pomocí místního webového uživatelského rozhraní připojovat k vašemu Azure Stackho minimu zařízení R.

Dokončení procesu připojování může trvat přibližně 5 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k fyzickému zařízení



## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením Azure Stack hraničního zařízení se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge](azure-stack-edge-mini-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Nakonfigurujte adaptér Ethernet v počítači pro připojení k zařízení Azure Stack Edge pro se statickou IP adresou 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu 1 vašeho zařízení. Pokud připojujete počítač přímo k zařízení (bez přepínače), použijte křížený kabel nebo adaptér USB sítě Ethernet. Pomocí následujícího obrázku identifikujte port 1 na vašem zařízení.

    ![Kabeláž pro Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)

[!INCLUDE [azure-stack-edge-gateway-delpoy-connect](../../includes/azure-stack-edge-gateway-deploy-connect.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Připojení k fyzickému zařízení


Informace o tom, jak nakonfigurovat nastavení sítě v zařízení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace sítě](./azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
