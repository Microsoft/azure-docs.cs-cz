---
title: Použití Galerie sdílených imagí v Azure Lab Services | Microsoft Docs
description: Naučte se, jak nakonfigurovat účet testovacího prostředí pro použití Galerie sdílených imagí, aby uživatel mohl sdílet image s ostatními a jiný uživatel může image použít k vytvoření virtuálního počítače šablony v testovacím prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: f438c32deb7e923f08396b0580d807d6e5b5e69a
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585040"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Použití Galerie sdílených imagí v Azure Lab Services
V tomto článku se dozvíte, jakým způsobem může správce učitelů/testovacího prostředí uložit image virtuálního počítače šablony, aby ji mohli znovu použít jiní uživatelé. Tyto image jsou uložené v [galerii sdílených imagí](../../virtual-machines/windows/shared-image-galleries.md)Azure. Jako první krok správce testovacího prostředí připojí existující galerii sdílených imagí k účtu testovacího prostředí. Jakmile je galerie sdílených imagí připojená, laboratoře vytvořené v účtu testovacího prostředí můžou ukládat image do galerie sdílených imagí. Ostatní učitelé můžou tuto image vybrat z Galerie sdílených imagí a vytvořit tak šablonu pro své třídy. 

## <a name="prerequisites"></a>Požadavky
- Pomocí [Azure PowerShell](../../virtual-machines/windows/shared-images.md) nebo rozhraní příkazového [řádku Azure](../../virtual-machines/linux/shared-images.md)můžete vytvořit galerii sdílených imagí.
- Připojili jste galerii sdílených imagí k účtu testovacího prostředí. Podrobné pokyny najdete v tématu [Postup připojení nebo odpojení Galerie sdílených imagí](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložení obrázku do galerie sdílených imagí
Po připojení Galerie sdílených imagí může správce účtu testovacího prostředí nebo učitel Uložit image do galerie sdílených imagí, aby ji mohli používat i ostatní učitelé. 

1. Na stránce **Šablona** testovacího prostředí vyberte **exportovat do galerie sdílených imagí** na panelu nástrojů.

    ![Tlačítko Uložit obrázek](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. V dialogovém okně **exportovat do sdílené bitové kopie** zadejte **název bitové kopie**a pak vyberte **exportovat**. 

    ![Dialog Exportovat do galerie sdílených imagí](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Průběh této operace můžete zobrazit na stránce **šablony** . Tato operace může nějakou dobu trvat. 

    ![Probíhá export.](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Po úspěšném provedení operace exportu se zobrazí následující zpráva:

    ![Export dokončen](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Můžete také nahrát obrázek do galerie sdílených imagí mimo kontext testovacího prostředí. Další informace najdete v tématu [Přehled Galerie sdílených imagí](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Použití obrázku z Galerie sdílených imagí
Učitel/profesor může vybrat vlastní image, která je k dispozici v galerii sdílených imagí pro šablonu během nového vytváření testovacího prostředí.

![Použití image virtuálního počítače z Galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Další kroky
Další informace o galeriích sdílených imagí najdete v tématu [Galerie sdílených imagí](../../virtual-machines/windows/shared-image-galleries.md).
