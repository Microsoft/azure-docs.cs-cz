---
title: Změnit zobrazení relace virtuálního počítače na zobrazení na celé obrazovce v Azure Bastionu | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak změnit zobrazení na celou obrazovku.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191548"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>Změňte zobrazení na celé obrazovce pro relace virtuálního počítače: Azure Bastion (Preview)

Tento článek pomůže můžete změnit zobrazení virtuálního počítače pro zobrazení na celé obrazovce a zpět v prohlížeči. Než začnete pracovat s virtuálním Počítačem, ujistěte se, že jste postupovali podle kroků pro [vytvořit hostitel typu bašta](bastion-create-host-portal.md). Poté se připojte k virtuálnímu počítači, který chcete pracovat buď pomocí [RDP](bastion-connect-vm-rdp.md) nebo [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="launch-the-clipboard-tool"></a>Spusťte nástroj schránky

Během vzdálené relace spusťte nástroj palety Bastionu schránky přístup tak, že vyberete dvě šipky, které se nachází na vlevo uprostřed relace.

![nástroje](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Vyberte zobrazení na celé obrazovce

Vyberte **na celou obrazovku –** tlačítkem přepnete na zobrazení na celé obrazovce prostředí relace. Po přepnutí, bude reinicializovat zachycení relace zobrazení na celé obrazovce.

![zobrazení na celé obrazovce](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Další postup

Přečtěte si [Bastionu nejčastější dotazy k](bastion-faq.md).
Zjistěte, jak [kopírování a vkládání](bastion-vm-copy-paste.md) do a z virtuálního počítače Azure.