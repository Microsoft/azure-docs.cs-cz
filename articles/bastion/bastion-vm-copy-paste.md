---
title: 'Kopírování a vkládání do a z virtuálního počítače: Azure Bastion'
description: V tomto článku se dozvíte, jak kopírovat a vkládání do a z virtuálního počítače Azure pomocí Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989531"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopírování a vkládání do virtuálního počítače: Azure Bastion

Tento článek vám pomůže zkopírovat a vložit text do a z virtuálních počítačů při použití Azure Bastion. Než začnete pracovat s virtuálním počítačem, ujistěte se, že jste postupovali podle kroků k [vytvoření hostitele bašty](bastion-create-host-portal.md). Potom se připojte k virtuálnímu virtuálnímu virtuálnímu zařízení, se kterým chcete pracovat pomocí [rdp](bastion-connect-vm-rdp.md) nebo [SSH](bastion-connect-vm-ssh.md).

U prohlížečů, které podporují pokročilý přístup k rozhraní API schránky, můžete kopírovat a vkládat text mezi místním zařízením a vzdálenou relací stejným způsobem, jakým kopírujete a vkládáte mezi aplikacemi v místním zařízení. Pro ostatní prohlížeče můžete použít paletu nástrojů pro přístup ke schránce Bastion.

   ![Povolit schránku](./media/bastion-vm-manage/allow.png)

Podporována je pouze kopírování a vkládání textu. Pro přímé kopírování a vkládání vás prohlížeč může vyzvat k přístupu do schránky při inicializování relace Bastion. **Povolte** webové stránce přístup ke schránce.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopírování do vzdálené relace

Po připojení k virtuálnímu počítači pomocí [portálu Azure ](https://portal.azure.com)proveďte následující kroky:

1. Zkopírujte text/obsah z místního zařízení do místní schránky.
1. Během vzdálené relace spusťte paletu nástrojů pro přístup ke schránce bastionu výběrem dvou šipek. Šipky jsou umístěny v levém středu relace.

   ![paleta nástrojů](./media/bastion-vm-manage/left.png)

   ![Schránky](./media/bastion-vm-manage/clipboard.png)
1. Kopírovaný text se obvykle automaticky zobrazí na paletě kopírování bastionu. Pokud text není k dispozici, vložte text do textové oblasti palety.
1. Jakmile je text v textové oblasti, můžete jej vložit do vzdálené relace.

   ![Vložit](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopírování ze vzdálené relace

Po připojení k virtuálnímu počítači pomocí [portálu Azure ](https://portal.azure.com)proveďte následující kroky:

1. Zkopírujte text/obsah ze vzdálené relace do vzdálené schránky (pomocí ctrl-c).

   ![paleta nástrojů](./media/bastion-vm-manage/remote.png)
1. Během vzdálené relace spusťte paletu nástrojů pro přístup ke schránce bastionu výběrem dvou šipek. Šipky jsou umístěny v levém středu relace.

   ![Schránky](./media/bastion-vm-manage/clipboard2.png)
1. Kopírovaný text se obvykle automaticky zobrazí na paletě kopírování bastionu. Pokud text není k dispozici, vložte text do textové oblasti palety.
1. Jakmile je text v textové oblasti, můžete jej vložit do místního zařízení.

   ![Vložit](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k [baště](bastion-faq.md).