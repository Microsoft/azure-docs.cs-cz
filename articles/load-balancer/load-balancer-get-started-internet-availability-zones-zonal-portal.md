---
title: Vytvoření Load Balanceru s oblastmi front-endu – Azure portal
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit Load balanceru úrovně Standard s oblastmi front-endu pomocí webu Azure portal
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 0a746ace4806495b17462b506f030811247240e2
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247187"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Vytvoření Load Balanceru úrovně Standard s oblastmi front-endu pomocí webu Azure portal

Tento článek popisuje postup vytvořením veřejné [Load balanceru úrovně Standard](https://aka.ms/azureloadbalancerstandard) s konfigurací IP adresy front-endu oblastmi. Vysvětlení fungování zón dostupnosti s Load balanceru úrovně Standard najdete v tématu [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Vytvoření load balanceru s oblastmi front-endové IP adresy

1. V prohlížeči přejděte na web Azure Portal: [ http://portal.azure.com ](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroje pro vyrovnávání zatížení.**
3. V **vytvořit nástroj pro vyrovnávání zatížení** stránce v části **název** typ **myLoadBalancer**.
4. V části **Typ** vyberte **Veřejný**.
5. V části skladové položky, vyberte **standardní**.
6. Klikněte na tlačítko **zvolte veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a v **vytvoření veřejné IP adresy** stránky podle názvu, typu **myPublicIPZonal**, pro skladovou jednotku, vyberte **standardní**, pro zónu dostupnosti, vyberte **1**.
    
>[!NOTE] 
> Veřejnou IP adresu vytvořenou v tomto kroku je standardní SKU ve výchozím nastavení.

7. Pro **skupiny prostředků**, klikněte na tlačítko **vytvořit nový**a pak zadejte **myResourceGroupZLB** jako název skupiny prostředků.
8. Pro **umístění**vyberte **západní Evropa**a potom klikněte na tlačítko **OK**. Nástroj pro vyrovnávání zatížení se pak začne nasazovat. Úspěšné dokončení nasazení trvá několik minut.

    ![Vytvoření zónově redundantní Load balanceru úrovně Standard pomocí webu Azure portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Další postup
- Další informace o [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md).



