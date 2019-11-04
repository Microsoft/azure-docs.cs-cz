---
title: 'Kopírování a vkládání do a z virtuálního počítače: Azure bastionu | Microsoft Docs'
description: V tomto článku se dozvíte, jak kopírovat a vkládat do a z virtuálního počítače Azure pomocí bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: ed8d52a4932271020dfb2a010392b312fa38703b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519356"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Zkopírování a vložení do virtuálního počítače: Azure bastionu

Tento článek vám pomůže kopírovat a vkládat text do a z virtuálních počítačů při použití Azure bastionu. Než začnete pracovat s virtuálním počítačem, ujistěte se, že jste postupovali podle pokynů k [Vytvoření hostitele bastionu](bastion-create-host-portal.md). Pak se připojte k virtuálnímu počítači, se kterým chcete pracovat pomocí [protokolu RDP](bastion-connect-vm-rdp.md) nebo [SSH](bastion-connect-vm-ssh.md).

V prohlížečích podporujících pokročilý přístup k rozhraní API ve schránce můžete kopírovat a vkládat text mezi místními a vzdálenými relacemi stejným způsobem jako při kopírování a vkládání mezi aplikacemi na místním zařízení. Pro jiné prohlížeče můžete použít paletu nástroje pro přístup ke schránce bastionu.

   ![Povoluje schránku](./media/bastion-vm-manage/allow.png)

Podporuje se jenom textové kopírování a vkládání. V případě přímých kopírování a vkládání může prohlížeč při inicializaci relace bastionu zobrazit výzvu k zadání přístupu do schránky. **Povolí** webové stránce přístup ke schránce.

## <a name="to"></a>Kopírovat do vzdálené relace

Po připojení k virtuálnímu počítači pomocí [Azure Portal ](https://portal.azure.com)proveďte následující kroky:

1. Zkopíruje text nebo obsah z místního zařízení do místní schránky.
1. Během vzdálené relace spusťte na paletě nástrojů pro přístup ke schránce bastionu výběr dvou šipek. Šipky se nacházejí v levém středu relace.

   ![Paleta nástrojů](./media/bastion-vm-manage/left.png)

   ![Obsah](./media/bastion-vm-manage/clipboard.png)
1. Zkopírovaný text se obvykle automaticky zobrazuje na paletě pro vložení bastionu kopírování. Pokud text tam není, vložte text do textové oblasti na paletě.
1. Jakmile je text v oblasti textu, můžete ho vložit do vzdálené relace.

   ![Vlož](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Kopírovat ze vzdálené relace

Po připojení k virtuálnímu počítači pomocí [Azure Portal ](https://portal.azure.com)proveďte následující kroky:

1. Kopírovat text nebo obsah ze vzdálené relace do vzdálené schránky (pomocí kombinace kláves CTRL-C).

   ![Paleta nástrojů](./media/bastion-vm-manage/remote.png)
1. Během vzdálené relace spusťte na paletě nástrojů pro přístup ke schránce bastionu výběr dvou šipek. Šipky se nacházejí v levém středu relace.

   ![Obsah](./media/bastion-vm-manage/clipboard2.png)
1. Zkopírovaný text se obvykle automaticky zobrazuje na paletě pro vložení bastionu kopírování. Pokud text tam není, vložte text do textové oblasti na paletě.
1. Jakmile se text v oblasti textu nachází, můžete ho vložit do místního zařízení.

   ![Vlož](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).