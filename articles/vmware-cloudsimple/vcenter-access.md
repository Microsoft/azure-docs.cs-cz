---
title: Řešení Azure VMware od clouduSimple – klient Access vSphere
description: Popisuje, jak získat přístup k programu vCenter privátního cloudu.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022660"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Přístup k portálu private cloud vCenter

Portál Private Cloud vCenter můžete spustit z portálu Azure nebo cloudového portálu.  Portál vCenter umožňuje spravovat infrastrukturu VMware ve vašem privátním cloudu.

## <a name="before-you-begin"></a>Než začnete

Pro přístup k portálu vCenter musí být navázáno síťové připojení a musí být povoleno překlad názvů DNS.  Síťové připojení k privátnímu cloudu můžete navázat pomocí kterékoli z níže uvedených možností.

* [Připojení z místního prostředí ke CloudSimple pomocí ExpressRoute](on-premises-connection.md)
* [Konfigurace připojení VPN k privátnímu cloudu CloudSimple](set-up-vpn.md)

Pokud chcete nastavit překlad názvů DNS součástí infrastruktury Private Cloud VMware, [přečtěte si část Konfigurace dns pro překlad názvů pro privátní cloud vCenter přístup z místních pracovních stanic](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

## <a name="access-vcenter-from-azure-portal"></a>Přístup k virtuálnímu centru z webu Azure Portal

Portál vCenter privátního cloudu můžete spustit z portálu Azure.

1. Vyberte **Všechny služby**.

2. Vyhledejte **cloudové jednoduché služby**.

3. Vyberte službu CloudSimple vašeho privátního cloudu, ke které se chcete připojit.

4. Na stránce **Přehled** klikněte na **Zobrazit soukromé cloudy v Mware.**

    ![Přehled služby CloudSimple](media/cloudsimple-service-overview.png)

5. Ze seznamu Privátních cloudů vyberte privátní cloud a klikněte na **Spustit klienta vSphere**.

    ![Spuštění klienta vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Přístup k virtuálnímu centru z portálu CloudSimple

Portál vCenter privátního cloudu můžete spustit z portálu CloudSimple.

1. Přístup k [portálu CloudSimple](access-cloudsimple-portal.md).

2. V nabídce **Resources** vyberte privátní cloud, ke kterému chcete získat přístup, a klikněte na **spustit klienta vSphere**.

    ![Spuštění klienta vSphere – prostředky](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Portál vCenter můžete také spustit ze souhrnné obrazovky privátního cloudu.

    ![Spuštění klienta vSphere – souhrn](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Další kroky

* [Vytvoření a správa sítí VLAN/podsítí pro privátní cloudy](create-vlan-subnet.md)
* [CloudSimple Private Cloud model oprávnění VMware vCenter](learn-private-cloud-permissions.md)