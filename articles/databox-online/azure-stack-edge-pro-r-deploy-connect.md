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
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: ce97c22cf4bfbe5cca01183574597706a8c239e7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468162"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-r"></a>Kurz: připojení k Azure Stack Edge pro R

V tomto kurzu se dozvíte, jak se můžete připojit k zařízení Azure Stack Edge pro R pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojení může trvat přibližně 5 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady
> * Připojení k fyzickému zařízení


## <a name="prerequisites"></a>Předpoklady

Před konfigurací a nastavením Azure Stack hraničního zařízení pro R se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Nakonfigurujte adaptér Ethernet v počítači pro připojení k zařízení Azure Stack Edge pro R se statickou IP adresou 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu 1 na svém zařízení. Pokud počítač připojujete přímo k zařízení (bez přepínače), použijte křížený kabel nebo adaptér USB Ethernet. Pomocí následující ilustrace Identifikujte PORT 1 na vašem zařízení.

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
    Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat tři z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.

Teď jste na stránce **Přehled** svého zařízení. Dalším krokem je konfigurace nastavení sítě pro vaše zařízení.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Předpoklady
> * Připojení k fyzickému zařízení


Informace o tom, jak nakonfigurovat nastavení sítě na Azure Stack hraničních zařízeních pro R, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace sítě](./azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)
