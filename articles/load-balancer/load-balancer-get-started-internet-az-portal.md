---
title: Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí portálu Azure | Microsoft Docs
description: Naučte se vytvořit standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí portálu Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 1f1c8d0305334d85500b501aee5a71664bb49050
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí portálu Azure

Tento článek popisuje postup procesem vytvoření veřejné [standardní nástroje pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) s zónově redundantní front-end pomocí standardní veřejné IP adresy. Zónově redundantní ve výchozím nastavení je na jednom front-endovou IP adresu na standardní Vyrovnávání zatížení.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
 Podpora pro dostupnost zóny je k dispozici pro vyberte prostředků Azure a oblasti a rodiny velikost virtuálního počítače. Další informace o tom, jak začít pracovat a které prostředky Azure, oblasti a rodiny velikost virtuálního počítače můžete zkusit dostupnost zóny s, najdete v části [přehled dostupnosti zón](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení redundantní zóny

1. V prohlížeči přejděte na portálu Azure: [ http://portal.azure.com ](http://portal.azure.com) a přihlaste se pomocí účtu Azure.
2. Na nejvyšší levé straně obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroj pro vyrovnávání zatížení.**
3. V **vytvořit nástroj pro vyrovnávání zatížení** v části **název** typ **myLoadBalancer**.
4. V části **Typ** vyberte **Veřejný**.
5. V části SKU, vyberte **standardní**.
6. Klikněte na tlačítko **veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**a v **vytvoření veřejné IP adresy** stránky v části název, typ **myPublicIPStandard**.
    >[!NOTE] 
    > Veřejné IP adresy vytvořené v tomto kroku je standardní SKU a je zónově redundantní ve výchozím nastavení. 
8. V části **umístění**, vyberte **východní US2**a potom klikněte na **OK**. Nástroj pro vyrovnávání zatížení se pak začne nasazovat. Úspěšné dokončení nasazení trvá několik minut.

    ![Vytvoření vyrovnávání standardní zónově redundantní zatížení pomocí portálu Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Další postup
- Další informace o [nástroj pro vyrovnávání zatížení a dostupnost zóny](load-balancer-standard-availability-zones.md).



