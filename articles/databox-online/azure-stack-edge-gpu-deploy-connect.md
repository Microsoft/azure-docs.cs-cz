---
title: Kurz pro připojení, konfiguraci a aktivaci zařízení Azure Stack Edge pomocí GPU v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge vám dává pokyn k připojení, nastavení a aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: c98d6644dba0b3ca51f07f01ec27fee697369f2b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087723"
---
# <a name="tutorial-connect-to-azure-stack-edge-with-gpu"></a>Kurz: připojení k Azure Stack Edge pomocí GPU

V tomto kurzu se dozvíte, jak se můžete připojit k zařízení Azure Stack Edge pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojení může trvat přibližně 5 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Připojení k fyzickému zařízení


## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením Azure Stack hraničního zařízení pomocí GPU se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Nakonfigurujte adaptér Ethernet v počítači pro připojení k Azure Stack hraničnímu zařízení se statickou IP adresou 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu 1 na svém zařízení. Pomocí následující ilustrace Identifikujte PORT 1 na vašem zařízení.

    ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)


3. Otevřete okno prohlížeče a přejděte k místnímu webovému uživatelskému rozhraní zařízení v `https://192.168.100.10` .  
    Tato akce může trvat několik minut, než se zařízení zapnete. 

    Zobrazí se chyba nebo upozornění označující, že došlo k potížím s certifikátem zabezpečení webu. 
   
    ![Chybová zpráva certifikátu zabezpečení webu](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Vyberte možnost **pokračovat na tuto webovou stránku**.  
    Tyto kroky se můžou lišit v závislosti na prohlížeči, který používáte.

5. Přihlaste se k webovému uživatelskému rozhraní vašeho zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Přihlašovací stránka zařízení Azure Stack Edge](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. V příkazovém řádku změňte heslo správce zařízení.  
    Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat tři z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.

Teď jste na stránce **Přehled** svého zařízení. Dalším krokem je konfigurace nastavení sítě pro vaše zařízení.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Připojení k fyzickému zařízení


Informace o tom, jak nakonfigurovat nastavení sítě na Azure Stack hraničním zařízení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace sítě](./azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
