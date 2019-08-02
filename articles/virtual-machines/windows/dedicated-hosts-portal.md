---
title: Nasazení vyhrazených hostitelů Azure pomocí Azure Portal | Microsoft Docs
description: Nasaďte virtuální počítače na vyhrazené hostitele pomocí Azure Portal.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: 0a3cb76d1e28559727957c1007b0992fb8e90f2b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700558"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Verze Preview: Nasazení virtuálních počítačů na vyhrazené hostitele pomocí portálu

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Na **nové** stránce v části **Oblíbené**vyberte **Windows Server 2016 Datacenter**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné, a pak jako **skupinu prostředků**vyberte *myDedicatedHostsRG* . 
1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Umístění** zvolte *USA – východ*.
1. V části **Možnosti dostupnosti** vyberte **zóna dostupnosti**, v rozevíracím seznamu vyberte *1* .
1. V poli Velikost vyberte **změnit velikost**. V seznamu dostupných velikostí vyberte jednu z Esv3 řad, jako je **Standard E2s V3**. Možná budete muset filtr vymazat, aby se zobrazily všechny dostupné velikosti.
1. V **Účtu správce** zadejte uživatelské jméno, například *azureuser*, a heslo. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. V části **pravidla portů pro příchozí spojení**zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **RDP (3389)** .
1. V horní části stránky vyberte kartu **Upřesnit** a v části **hostitel** vyberte *MyHostGroup* pro **skupinu hostitelů** a *myHost* pro **hostitele**. 
    ![Vybrat skupinu hostitelů a hostitele](./media/dedicated-hosts-portal/advanced.png)
1. Zbytek ponechte ve výchozím nastavení a pak v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.
1. Když se zobrazí zpráva, že ověření proběhlo úspěšně, vyberte **vytvořit**.


## <a name="next-steps"></a>Další postup

- Další informace najdete v tématu Přehled [vyhrazených hostitelů](dedicated-hosts.md) . 

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Vyhrazeného hostitele můžete nasadit taky pomocí [Azure PowerShell](dedicated-hosts-powershell.md).