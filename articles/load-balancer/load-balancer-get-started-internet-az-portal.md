---
title: Vytvoření Load Balanceru úrovně Standard s zónově redundantních front-endu pomocí webu Azure portal
titlesuffix: Azure Load Balancer
description: Zjistěte, jak vytvořit veřejný Load Balancer Standard s zónově redundantní veřejné IP adresy front-endu pomocí webu Azure portal
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: fc5dcd76ca8ecfb820a8d157fee9cc0a92bd2d5b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135678"
---
#  <a name="create-a-public-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Vytvořte veřejný Load Balancer Standard s zónově redundantních front-endu pomocí webu Azure portal

Tento článek popisuje postup vytvořením veřejné [Load balancer úrovně Standard](https://aka.ms/azureloadbalancerstandard) s zónově redundantních front-endu pomocí standardní veřejné IP adresy. Zónově redundantní ve výchozím nastavení je na jeden front-endu IP adresu Load balanceru úrovně Standard.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
 Podpora pro zóny dostupnosti je k dispozici pro vyberte prostředky Azure a oblasti a velikostní řady virtuálních počítačů. Další informace o tom, jak začít a které prostředky Azure, oblasti a velikostní řady virtuálních počítačů můžete vyzkoušet zóny dostupnosti s, najdete v části [přehledu zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Vytvořte zóny redundantní load balancer

1. V prohlížeči přejděte na web Azure Portal: [ http://portal.azure.com ](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. V levém horním rohu obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroje pro vyrovnávání zatížení.**
3. V **vytvořit nástroj pro vyrovnávání zatížení** stránce v části **název** typ **myLoadBalancer**.
4. V části **Typ** vyberte **Veřejný**.
5. V části skladové položky, vyberte **standardní**.
6. Klikněte na tlačítko **veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a v **vytvoření veřejné IP adresy** stránky podle názvu, typu **myPublicIPStandard**.
    >[!NOTE] 
    > Veřejnou IP adresu vytvořenou v tomto kroku je standardní SKU a zónově redundantní ve výchozím nastavení. 
8. V části **umístění**vyberte **východní USA 2**a potom klikněte na tlačítko **OK**. Nástroj pro vyrovnávání zatížení se pak začne nasazovat. Úspěšné dokončení nasazení trvá několik minut.

## <a name="next-steps"></a>Další postup
- Další informace o [Load balanceru úrovně Standard a dostupnost zóny](load-balancer-standard-availability-zones.md).



