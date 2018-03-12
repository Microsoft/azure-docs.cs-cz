---
title: "Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí portálu Azure | Microsoft Docs"
description: "Naučte se vytvořit standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí portálu Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: d09115d7b01b72476ebc9eb883a43aa4c59d2e3b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Vytvořte standardní veřejné zatížení na vyrovnávání s zónově redundantní veřejná IP adresa front-endu pomocí portálu Azure

Tento článek popisuje postup procesem vytvoření veřejné [standardní nástroje pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) s zónově redundantní front-end pomocí standardní veřejné IP adresy.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Registrace pro dostupnost zóny, standardní nástroje pro vyrovnávání zatížení a veřejnou IP adresu standardní Preview
 
>[!NOTE]
[Standardní SKU pro vyrovnávání zatížení](https://aka.ms/azureloadbalancerstandard) je aktuálně ve verzi Preview. Během období Preview tato funkce nemusí dosahovat stejné úrovně dostupnosti a spolehlivosti jako funkce, které jsou ve verzi všeobecné dostupnosti. Další informace najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Použít obecně dostupná [základní SKU služby Vyrovnávání zatížení](load-balancer-overview.md) pro vaše produkční služby. 

> [!NOTE]
> Dostupnost zóny jsou ve verzi preview a jsou připraveny pro váš vývojový a testovací scénáře. Podpora je k dispozici pro vyberte prostředků Azure a oblasti a rodiny velikost virtuálního počítače. Další informace o tom, jak začít pracovat a které prostředky Azure, oblasti a rodiny velikost virtuálního počítače můžete zkusit dostupnost zóny s, najdete v části [přehled dostupnosti zón](https://docs.microsoft.com/azure/availability-zones/az-overview). Pokud budete potřebovat podporu, můžete kontaktovat [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) nebo [otevřít lístek podpory Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Před výběrem zóny nebo zónově redundantní možnost pro veřejnou IP adresu front-endu nástroje pro vyrovnávání zatížení, musíte nejdřív dokončit kroky v [zaregistrovat verzi Preview zón dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení redundantní zóny

1. V prohlížeči přejděte na web Azure Portal: [http://portal.azure.com](http://portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Na nejvyšší levé straně obrazovky vyberte **vytvořit prostředek** > **sítě** > **nástroj pro vyrovnávání zatížení.**
3. V ** vytvořit službu Vyrovnávání zatížení, v části **název** typ **myPublicLB**.
4. V části **Typ** vyberte **Veřejný**.
5. V části SKU, vyberte **Standard (Preview)**.
6. Klikněte na tlačítko **veřejnou IP adresu**, klikněte na tlačítko **vytvořit nový**na **vytvoření veřejné IP adresy** stránky v části název, typ **myPublicIPStandard**, a pro **dostupnost zóny (Preview)**, vyberte **Zónově redundantní**.
7. V části **umístění**, vyberte **východní US2**a potom klikněte na **OK**. Nástroj pro vyrovnávání zatížení se pak začne nasazovat. Úspěšné dokončení nasazení trvá několik minut.

    ![Vytvoření vyrovnávání standardní zónově redundantní zatížení pomocí portálu Azure](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Další postup
- Zjistěte, jak [vytvořit veřejnou IP adresu v zóně dostupnosti](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



