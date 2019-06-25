---
title: 'Zkopírujte a vložte do a z virtuálního počítače: Azure Bastionu | Dokumentace Microsoftu'
description: V tomto článku se dozvíte, jak zkopírovat a vložit do a z virtuálního počítače Azure pomocí Bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191808"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Zkopírujte a vložte do virtuálního počítače: Azure Bastion (Preview)

Tento článek pomůže můžete zkopírovat a vložit text do a z virtuálních počítačů při použití Azure Bastionu. Než začnete pracovat s virtuálním Počítačem, ujistěte se, že jste postupovali podle kroků pro [vytvořit hostitel typu bašta](bastion-create-host-portal.md). Poté se připojte k virtuálnímu počítači, který chcete pracovat buď pomocí [RDP](bastion-connect-vm-rdp.md) nebo [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Pro prohlížeče, které podporují pokročilé přístup přes rozhraní API schránky můžete zkopírovat a vložit text mezi místním zařízením a vzdálené relace stejným způsobem, kopírování a vkládání mezi aplikacemi na vaše místní zařízení. Pro jiné prohlížeče můžete použít nástroj palety Bastionu schránky přístup.

  ![Povolit schránky](./media/bastion-vm-manage/allow.png)

Je podporován pouze text, kopírování a vkládání. Pro přímou kopírování a vkládání váš prohlížeč vás může vyzvat k schránky přístup při inicializaci relace Bastionu. **Povolit** webové stránky přistupovat do schránky.

## <a name="to"></a>Zkopírujte do vzdálené relace

Po připojení k virtuálnímu počítači pomocí [webu Azure portal](https://aka.ms/BastionHost) ve verzi Preview Bastionu:

1. Zkopírujte do místní schránky text nebo obsah z místního zařízení.
1. Během vzdálené relace spusťte nástroj palety Bastionu schránky přístup tak, že vyberete dvě šipky. Šipky se nacházejí v vlevo uprostřed relace.

    ![Nástroj palety](./media/bastion-vm-manage/left.png)

    ![Schránky](./media/bastion-vm-manage/clipboard.png)

1. Obvykle zkopírovaný text automaticky zobrazí na paletě Bastionu kopírování vložit. Pokud není text, vložte text do textového pole na paletě.
1. Jakmile je text v textovém poli, můžete vložit jej do vzdálené relace.

    ![Vložit](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Zkopírovat ze vzdálené relace

Po připojení k virtuálnímu počítači pomocí [webu Azure portal](https://aka.ms/BastionHost) ve verzi Preview Bastionu:

1. Zkopírujte text nebo obsah ze vzdálené relace do vzdáleného schránky (pomocí Ctrl-C).

    ![Nástroj palety](./media/bastion-vm-manage/remote.png)

1. Během vzdálené relace spusťte nástroj palety Bastionu schránky přístup tak, že vyberete dvě šipky. Šipky se nacházejí v vlevo uprostřed relace.

    ![Schránky](./media/bastion-vm-manage/clipboard2.png)

1. Obvykle zkopírovaný text automaticky zobrazí na paletě Bastionu kopírování vložit. Pokud není text, vložte text do textového pole na paletě.
1. Jakmile je text v textovém poli, můžete ho vložit do místního zařízení.

    ![Vložit](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Další postup

Přečtěte si [Bastionu nejčastější dotazy k](bastion-faq.md).