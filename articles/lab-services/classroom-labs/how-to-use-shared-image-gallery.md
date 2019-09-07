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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 65cc2d9ac2b96822f2c1b740f3180ba1d9eaf98c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389963"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Použití Galerie sdílených imagí v Azure Lab Services
V tomto článku se dozvíte, jakým způsobem může správce učitelů/testovacího prostředí uložit image virtuálního počítače šablony, aby ji mohli znovu použít jiní uživatelé. Tyto image jsou uložené v [galerii sdílených imagí](../../virtual-machines/windows/shared-image-galleries.md)Azure. Jako první krok správce testovacího prostředí připojí existující galerii sdílených imagí k účtu testovacího prostředí. Jakmile je galerie sdílených imagí připojená, laboratoře vytvořené v účtu testovacího prostředí můžou ukládat image do galerie sdílených imagí. Ostatní učitelé můžou tuto image vybrat z Galerie sdílených imagí a vytvořit tak šablonu pro své třídy. 

## <a name="prerequisites"></a>Požadavky
- Pomocí [Azure PowerShell](../../virtual-machines/windows/shared-images.md) nebo rozhraní příkazového [řádku Azure](../../virtual-machines/linux/shared-images.md)můžete vytvořit galerii sdílených imagí.
- Připojili jste galerii sdílených imagí k účtu testovacího prostředí. Podrobné pokyny najdete v tématu [Postup připojení nebo odpojení Galerie sdílených imagí](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložení obrázku do galerie sdílených imagí
Po připojení Galerie sdílených imagí může správce účtu testovacího prostředí nebo učitel Uložit image do galerie sdílených imagí, aby ji mohli používat i ostatní učitelé. 

1. Na domovské stránce testovacího prostředí vyberte možnost **Uložit obrázek** na dlaždici v části **Šablona** .

    ![Tlačítko Uložit obrázek](../media/how-to-use-shared-image-gallery/save-image-button.png)
2.  V okně **Uložit bitovou kopii virtuálního počítače** zadejte název bitové kopie a vyberte **Uložit**. 

    ![Uložit okno image virtuálního počítače](../media/how-to-use-shared-image-gallery/save-virtual-machine-image.png)
3. Ověřte stav dlaždice testovací prostředí. 

    ![Stav operace Uložit obrázek](../media/how-to-use-shared-image-gallery/save-image-status.png)
4. Potvrďte, že operace proběhla úspěšně.

    ![Operace uložení obrázku byla úspěšná.](../media/how-to-use-shared-image-gallery/save-image-successful.png)

Můžete také nahrát obrázek do galerie sdílených imagí mimo kontext testovacího prostředí. Další informace najdete v tématu [Přehled Galerie sdílených imagí](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Použití obrázku z Galerie sdílených imagí
Učitel/profesor může vybrat vlastní image, která je k dispozici v galerii sdílených imagí pro šablonu během nového vytváření testovacího prostředí.

![Použití image virtuálního počítače z Galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Další kroky
Další informace o galeriích sdílených imagí najdete v tématu [Galerie sdílených imagí](../../virtual-machines/windows/shared-image-galleries.md).
