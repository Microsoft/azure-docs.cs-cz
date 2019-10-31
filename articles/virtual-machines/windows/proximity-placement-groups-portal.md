---
title: Vytvoření skupiny umístění blízkosti pomocí portálu
description: Naučte se vytvořit skupinu umístění blízkosti pomocí Azure Portal.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180248"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Vytvoření skupiny umístění blízkosti pomocí portálu

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.


## <a name="create-the-proximity-placement-group"></a>Vytvořit skupinu umístění blízkosti

1. Ve vyhledávání zadejte **skupinu umístění blízkosti** .
1. V části **služby** ve výsledcích hledání vyberte možnost **skupiny umístění pro Proximity**.
1. Na stránce **skupiny umístění pro blízkosti** vyberte **Přidat**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné.
1. V části **Skupina prostředků** vyberte **vytvořit novou** a vytvořte novou skupinu, nebo v rozevíracím seznamu vyberte existující skupinu prostředků.
1. V **oblasti** vyberte umístění, kde chcete vytvořit skupinu umístění blízkosti.
1. Do **skupiny umístění blízkosti** zadejte název a potom vyberte **zkontrolovat + vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit** a vytvořte skupinu umístění blízkosti.

    ![Snímek obrazovky s vytvořením skupiny umístění blízkosti](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Když vytváříte virtuální počítač na portálu, přejdete na kartu **Upřesnit** . 
1. V části Výběr **skupiny umístění blízkosti** vyberte správnou skupinu umístění. 

    ![Snímek obrazovky oddílu skupiny umístění blízkosti při vytváření nového virtuálního počítače na portálu](./media/ppg/vm-ppg.png)

1. Až dokončíte provádění všech dalších požadovaných výběrů, vyberte **zkontrolovat + vytvořit**.
1. Po úspěšném ověření vyberte **vytvořit** a NASAĎTE virtuální počítač do skupiny umístění.




## <a name="next-steps"></a>Další kroky

Můžete také použít [Azure PowerShell](proximity-placement-groups.md) k vytvoření skupin umístění blízkosti.

