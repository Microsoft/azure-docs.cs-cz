---
title: Kurz pro připojení, konfiguraci, aktivaci Azure Stack hraničního zařízení pro R v Azure Portal | Microsoft Docs
description: Přečtěte si, jak se můžete připojit k zařízení Azure Stack Edge pro R pomocí místního webového uživatelského rozhraní.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 1ded68783ec45c649ab4aa41996cb0113a7fa42d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059917"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Kurz: připojení k Azure Stack Edge pro R

V tomto kurzu se dozvíte, jak se můžete připojit k zařízení Azure Stack Edge pro R pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojování může trvat přibližně 5 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k fyzickému zařízení


## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením Azure Stack hraničního zařízení pro R se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Na adaptéru sítě Ethernet na počítači, pomocí kterého se připojujete k zařízení Azure Stack Edge Pro R, nakonfigurujte statickou IP adresu 192.168.100.5 a podsíť 255.255.255.0.

2. Připojte počítač k portu 1 vašeho zařízení. Pokud připojujete počítač přímo k zařízení (bez přepínače), použijte křížený kabel nebo adaptér USB sítě Ethernet. Pomocí následujícího obrázku identifikujte port 1 na vašem zařízení.

    ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)


3. Otevřete okno prohlížeče a přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`.  
    Po zapnutí zařízení může tato akce několik minut trvat.

    Zobrazí se vám chyba nebo upozornění indikující problém s certifikátem zabezpečení webu. 
   
    ![Chybová zpráva certifikátu zabezpečení webu](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-1.png)


4. Vyberte možnost **pokračovat na tuto webovou stránku**.  
    Tyto kroky se můžou lišit v závislosti na použitém prohlížeči.

5. Přihlaste se k webovému uživatelskému rozhraní vašeho zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Přihlašovací stránka zařízení Azure Stack Edge](media/azure-stack-edge-pro-r-deploy-connect/connect-web-ui-3.png)

6. V příkazovém řádku změňte heslo správce zařízení.  
    Nové heslo musí mít 8 až 16 znaků. Musí obsahovat kombinaci tří z následujících čtyř typů znaků: velká písmena, malá písmena, číslice a speciální znaky.

Teď jste na stránce **Přehled** svého zařízení. Dalším krokem je konfigurace nastavení sítě pro vaše zařízení.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Připojení k fyzickému zařízení


Informace o tom, jak nakonfigurovat nastavení sítě na Azure Stack hraničních zařízeních pro R, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace sítě](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
