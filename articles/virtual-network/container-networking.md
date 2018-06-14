---
title: Virtuální síť Azure pro kontejnery | Microsoft Docs
description: Další informace o modulu plug-in pro clustery Kubernetes CNI, která umožňuje kontejnery pro komunikaci se a další prostředky ve virtuální síti.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2017
ms.locfileid: "26766394"
---
# <a name="container-networking"></a>Kontejner sítě

Azure poskytuje [kontejner sítě rozhraní (CNI) modul plug-in](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) , které umožňuje nasazovat a spravovat vlastní Kubernetes clusteru s nativní funkce sítě Azure. Tento modul plug-in, ve výchozím nastavení zapnutá, při nasazení Kubernetes clustery s [stroj služby Azure kontejneru](https://github.com/Azure/acs-engine) (nebo modul služby ACS).

## <a name="networking-capabilities"></a>Možnosti sítě

Kontejnery můžete využít širokou škálu možností, které nabízí virtuální sítě, jako například:
-   Můžete vytvořit samostatné virtuální sítě pro cluster nebo nasazení cluster v existující virtuální síť. 
-   Každý pod v clusteru přijímá IP adresu z v rámci virtuální sítě a může komunikovat přímo s další pracovními stanicemi soustředěnými kolem v clusteru a všechny virtuální počítače ve virtuální síti. 
-   Pod se můžou připojit další pracovními stanicemi soustředěnými kolem a virtuální počítače ve peered virtuálních sítích a sítích na pracovišti, přes připojení VPN typu site-to-site a ExpressRoute. Místní prostředky může komunikovat s pracovními stanicemi soustředěnými kolem. 
-   Služba Kubernetes k Internetu prostřednictvím nástroje pro vyrovnávání zatížení Azure můžou zpřístupnit.  
-   Pracovními stanicemi soustředěnými kolem v podsíti, který má povolené koncové body služby lze bezpečně připojit ke službám Azure (úložiště a databáze SQL, např.).
-   Trasy definované uživatelem můžete směrovat přenosy z pracovními stanicemi soustředěnými kolem virtuální síťové zařízení. 
-   Pracovními stanicemi soustředěnými kolem mají přístup k veřejné prostředkům na Internetu.
-   Můžete přiřadit pod veřejnou IP adresu, které mohou být přidruženy s názvem DNS.
 
## <a name="limits"></a>Omezení
Až na 4000 uzly v clusteru s podporou Kubernetes a až 250 pracovními stanicemi soustředěnými kolem na uzel a celkový limit 16 000 pracovními stanicemi soustředěnými kolem každý cluster, můžete nasadit při použití modulu plug-in.

## <a name="constraints"></a>Omezení
- Tento modul plug-in není povolena při nasazování clusteru Kubernetes se [Azure Container Service (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) nebo [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cluster s Kubernetes.
- Neexistuje žádná nativní podpora pro Kubernetes zásady sítě, včetně zásad DNS nebo přístupu.
- Tento modul plug-in nepodporuje za pod zásady sítě.

## <a name="pricing"></a>Ceny
Není nijak zpoplatněn pro používání modulu plug-in CNI.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [nasadit Kubernetes cluster](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) v vaše [vlastní virtuální sítě](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
