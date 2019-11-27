---
title: Vytvoření Load Balancer s oblastmi front-endu-Azure Portal
titleSuffix: Azure Load Balancer
description: Naučte se vytvářet Standard Load Balancer s oblastmi front-endu s Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225341"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Vytvoření Standard Load Balancer s použitím Zona front-endu pomocí Azure Portal

Tento článek vás provede vytvořením veřejné [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) s konfigurací IP adresy ve front-endu. Informace o tom, jak zóny dostupnosti pracují s Standard Load Balancer, najdete v tématu [Standard Load Balancer a zóny dostupnosti](load-balancer-standard-availability-zones.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky, oblasti a řady velikostí virtuálních počítačů Azure můžete vyzkoušet, najdete v tématu [přehled zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Vytvoření load balanceru s oblastmi front-endové IP adresy

1. V prohlížeči přejděte na Azure Portal: [https://portal.azure.com](https://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **síťové** > **Load Balancer.**
3. Na stránce **vytvořit nástroj pro vyrovnávání zatížení** v části **název** zadejte **myLoadBalancer**.
4. V části **Typ** vyberte **Veřejný**.
5. V části SKU vyberte **standardní**.
6. Klikněte na **zvolit veřejnou IP adresu**, klikněte **na vytvořit novou**a na stránce **vytvořit veřejnou IP adresu** do pole název zadejte **MyPublicIPZonal**, u položky SKU vyberte možnost **standardní**, pro položku zóna dostupnosti vyberte **1**.
    
>[!NOTE] 
> Veřejnou IP adresu vytvořenou v tomto kroku je standardní SKU ve výchozím nastavení.

1. V části **Skupina prostředků**klikněte na **vytvořit novou**a jako název skupiny prostředků zadejte **myResourceGroupZLB** .
1. V **oblasti umístění**vyberte **západní Evropa**a pak klikněte na **OK**. Nástroj pro vyrovnávání zatížení se pak začne nasazovat. Úspěšné dokončení nasazení trvá několik minut.

    ![Vytvoření Standard Load Balancer redundantních v zóně s Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Standard Load Balancer a zónách dostupnosti](load-balancer-standard-availability-zones.md).



