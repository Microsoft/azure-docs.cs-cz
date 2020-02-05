---
title: Řešení Azure VMware (AVS) – přístup k klientovi vSphere
description: Popisuje, jak získat přístup k serveru vCenter z privátního cloudu služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022660"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Přístup k portálu služby AVS privátní cloud vCenter

Můžete spustit portál služby AVS Private Cloud vCenter z Azure Portal nebo na portálu AVS. portál vCenter umožňuje správu infrastruktury VMware v privátním cloudu služby AVS.

## <a name="before-you-begin"></a>Než začnete

Aby bylo možné získat přístup k portálu vCenter, musí být navázáno síťové připojení a musí být povoleno rozlišení názvů DNS. Síťové připojení k privátnímu cloudu služby AVS můžete vytvořit pomocí kterékoli z následujících možností.

* [Připojení z místního prostředí k funkci AVS pomocí ExpressRoute](on-premises-connection.md)
* [Konfigurace připojení VPN k privátnímu cloudu služby AVS](set-up-vpn.md)

Postup nastavení překladu názvů DNS vaší komponenty infrastruktury privátního cloudu služby AVS najdete v tématu [Konfigurace DNS pro překlad IP adres pro službu AVS privátní cloud vCenter přístup z místních pracovních stanic](on-premises-dns-setup.md) .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Přístup k vCenter z Azure Portal

Portál vCenter vašeho privátního cloudu služby AVS můžete spustit z Azure Portal.

1. Vyberte **Všechny služby**.

2. Vyhledejte **služby AVS**.

3. Vyberte službu AVS vašeho privátního cloudu služby AVS, ke které se chcete připojit.

4. Na stránce **Přehled** klikněte na **Zobrazit privátní cloudy VMware AVS.**

    ![Služba AVS – přehled služby](media/cloudsimple-service-overview.png)

5. V seznamu privátních cloudů služby AVS vyberte privátní cloud AVS a klikněte na **Spustit klienta vSphere**.

    ![Spustit klienta vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Přístup k vCenter z portálu pro funkci AVS

Portál vCenter vašeho privátního cloudu služby AVS můžete spustit z portálu pro funkci AVS.

1. Přístup k [portálu služby AVS](access-cloudsimple-portal.md).

2. Z **prostředků** vyberte privátní cloud služby AVS, ke kterému chcete získat přístup, a klikněte na **Spustit klienta vSphere**.

    ![Spustit klienta vSphere – prostředky](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Portál vCenter můžete také spustit ze souhrnné obrazovky vašeho privátního cloudu služby AVS.

    ![Spustit klienta vSphere – souhrn](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Další kroky

* [Vytváření a správa sítí VLAN a podsítí pro privátní cloudy služby AVS](create-vlan-subnet.md)
* [Model oprávnění privátního cloudu AVS pro VMware vCenter](learn-private-cloud-permissions.md)