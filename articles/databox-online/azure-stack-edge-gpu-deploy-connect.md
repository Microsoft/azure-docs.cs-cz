---
title: Kurz pro připojení, konfiguraci, aktivaci Azure Stack Edge pro zařízení s grafickým procesorem v Azure Portal | Microsoft Docs
description: Přečtěte si, jak se můžete připojit k zařízení Azure Stack Edge pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/29/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: f069f711ccf5672041ea6f3b15a52fa2b3ba2022
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90900026"
---
# <a name="tutorial-connect-to-azure-stack-edge-pro-with-gpu"></a>Kurz: připojení k Azure Stack Edge pro pomocí GPU

V tomto kurzu se dozvíte, jak se můžete připojit k zařízení Azure Stack Edge pro pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojení může trvat přibližně 5 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k fyzickému zařízení


## <a name="prerequisites"></a>Požadavky

Než nakonfigurujete a nastavíte Azure Stack Edge pro zařízení s grafickým procesorem, ujistěte se, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Nakonfigurujte adaptér Ethernet v počítači pro připojení k zařízení Azure Stack Edge pro se statickou IP adresou 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu 1 na svém zařízení. Pokud počítač připojujete přímo k zařízení (bez přepínače), použijte křížený kabel nebo adaptér USB Ethernet. Pomocí následující ilustrace Identifikujte PORT 1 na vašem zařízení.

    ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    Rozdílné zařízení může vypadat trochu jinak, v závislosti na přesném modelu, který jste dostali. Další informace najdete v tématu připojení [zařízení k kabelu](azure-stack-edge-gpu-deploy-install.md#cable-the-device).


3. Otevřete okno prohlížeče a přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`.  
    Po zapnutí zařízení může tato akce několik minut trvat.

    Zobrazí se vám chyba nebo upozornění indikující problém s certifikátem zabezpečení webu. 
   
    ![Chybová zpráva certifikátu zabezpečení webu](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Vyberte možnost **pokračovat na tuto webovou stránku**.  
    Tyto kroky se můžou lišit v závislosti na použitém prohlížeči.

5. Přihlaste se k webovému uživatelskému rozhraní vašeho zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Přihlašovací stránka pro zařízení Azure Stack Edge pro](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. V příkazovém řádku změňte heslo správce zařízení.  
    Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat tři z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.

Teď jste na stránce **Přehled** svého zařízení. Dalším krokem je konfigurace nastavení sítě pro vaše zařízení.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Připojení k fyzickému zařízení


Informace o tom, jak nakonfigurovat nastavení sítě na zařízení Azure Stack Edge pro, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace sítě](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
