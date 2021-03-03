---
title: Vytvoření skupiny umístění bezkontaktní komunikace pomocí portálu
description: Naučte se vytvořit skupinu umístění blízkosti pomocí Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/24/2020
ms.author: cynthn
ms.openlocfilehash: 201f2f585c907ff82f28b96562efe3622dae6422
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666035"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Vytvoření skupiny umístění bezkontaktní komunikace pomocí portálu

Pokud chcete co nejblíže získat virtuální počítače a dosáhnout nejnižší možné latence, měli byste je nasadit v rámci [skupiny umístění blízkosti](../co-location.md#proximity-placement-groups).

Skupina umístění blízkosti je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěné blízko sebe. Skupiny umístění blízkosti jsou užitečné pro úlohy, u kterých je minimální latence požadavek.

> [!NOTE]
> Skupiny umístění blízkosti nelze použít se vyhrazenými hostiteli.
>
> Pokud chcete používat zóny dostupnosti spolu se skupinami umístění, musíte se ujistit, že virtuální počítače ve skupině umístění jsou také všechny ve stejné zóně dostupnosti.
>

## <a name="create-the-proximity-placement-group"></a>Vytvořit skupinu umístění blízkosti

1. Ve vyhledávání zadejte **skupinu umístění blízkosti** .
1. V části **služby** ve výsledcích hledání vyberte možnost **skupiny umístění pro Proximity**.
1. Na stránce **skupiny umístění pro blízkosti** vyberte **Přidat**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) Zkontrolujte, že je vybrané správné předplatné.
1. V části **Skupina prostředků** vyberte **vytvořit novou** a vytvořte novou skupinu, nebo vyberte prázdnou skupinu prostředků, která už existuje, z rozevíracího seznamu. 
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


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Přidání virtuálních počítačů v sadě dostupnosti do skupiny umístění s blízkými událostmi

Pokud je virtuální počítač součástí skupiny dostupnosti, musíte před přidáním virtuálních počítačů přidat skupinu dostupnosti do skupiny umístění.

1. Na [portálu](https://portal.azure.com) vyhledejte *skupiny dostupnosti* a z výsledků vyberte svou skupinu dostupnosti.
1. Stop\deallocate každý virtuální počítač ve skupině dostupnosti tak, že vyberete virtuální počítač a potom na stránce pro virtuální počítač vyberete **zastavit** a pak vyberete **OK** , aby se virtuální počítač zastavil.
1. Na stránce skupiny dostupnosti zajistěte, aby všechny virtuální počítače měly **stav** uvedený jako **Zastaveno (přidělení zrušeno)**.
1. V nabídce vlevo vyberte **Konfigurace**.
1. V části **Skupina umístění blízkosti** vyberte skupinu umístění z rozevíracího seznamu a pak vyberte **Uložit**.
1. V nabídce vlevo vyberte **Přehled** , abyste mohli znovu zobrazit seznam virtuálních počítačů. 
1. Vyberte každý virtuální počítač ve skupině dostupnosti a pak na stránce pro každý virtuální počítač vyberte **Spustit** . 


## <a name="add-existing-vm-to-placement-group"></a>Přidat existující virtuální počítač do skupiny umístění 


1. Na stránce pro virtuální počítač vyberte **zastavit**.
1. Jakmile je stav virtuálního počítače uvedený jako **Zastaveno (přidělení zrušeno)**, v levé nabídce vyberte **Konfigurace** .
1. V části **Skupina umístění blízkosti** vyberte skupinu umístění z rozevíracího seznamu a pak vyberte **Uložit**.
1. V nabídce vlevo vyberte **Přehled** a pak vyberte **Spustit** , aby se virtuální počítač restartoval.

 

## <a name="next-steps"></a>Další kroky

Můžete také použít [Azure PowerShell](proximity-placement-groups.md) k vytvoření skupin umístění blízkosti.