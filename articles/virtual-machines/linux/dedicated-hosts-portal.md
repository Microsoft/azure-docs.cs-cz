---
title: Nasazení vyhrazených hostitelů Azure pomocí Azure Portal | Microsoft Docs
description: Nasaďte virtuální počítače na vyhrazené hostitele pomocí Azure Portal.
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 03d8b5353a2dc9840c8231978243e7f94a5bff56
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700389"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Verze Preview: Nasazení virtuálních počítačů na vyhrazené hostitele pomocí portálu

Tento článek vás provede procesem vytvoření [vyhrazeného hostitele](dedicated-hosts.md) Azure pro hostování virtuálních počítačů. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. V levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Ve vyhledávacím poli nad seznamem prostředků Azure Marketplace vyhledejte a vyberte **Ubuntu Server 16.04 LTS** od společnosti Canonical a pak zvolte **Vytvořit**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné, a pak jako **skupinu prostředků**vyberte *myDedicatedHostsRG* . 
1. V části **Podrobnosti o instancích** jako **Název virtuálního počítače** zadejte *myVM* a u možnosti **Umístění** zvolte *USA – východ*.
1. V části **Možnosti dostupnosti** vyberte **zóna dostupnosti**, v rozevíracím seznamu vyberte *1* .
1. V poli Velikost vyberte **změnit velikost**. V seznamu dostupných velikostí vyberte jednu z Esv3 řad, jako je **Standard E2s V3**. Možná budete muset filtr vymazat, aby se zobrazily všechny dostupné velikosti.
1. V části **Účet správce** vyberte **Veřejný klíč SSH**, zadejte své uživatelské jméno a potom do textového pole vložte svůj veřejný klíč. Ve veřejném klíči odeberte počáteční a koncové prázdné znaky.

    ![Účet správce](./media/quick-create-portal/administrator-account.png)

1. V **části příchozí pravidla** > portů**veřejné příchozí porty**zvolte **Povolit vybrané porty** a v rozevíracím seznamu vyberte **SSH (22)** . 
1. V horní části stránky vyberte kartu **Upřesnit** a v části **hostitel** vyberte *MyHostGroup* pro **skupinu hostitelů** a *myHost* pro **hostitele**. 
    ![Vybrat skupinu hostitelů a hostitele](./media/dedicated-hosts-portal/advanced.png)
1. Zbytek ponechte ve výchozím nastavení a pak v dolní části stránky vyberte tlačítko **Zkontrolovat a vytvořit**.
1. Když se zobrazí zpráva, že ověření proběhlo úspěšně, vyberte **vytvořit**.

Nasazení virtuálního počítače bude několik minut trvat.

## <a name="next-steps"></a>Další postup

- Další informace najdete v tématu Přehled [vyhrazených hostitelů](dedicated-hosts.md) .

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Vyhrazeného hostitele můžete nasadit taky pomocí [Azure CLI](dedicated-hosts-cli.md).



