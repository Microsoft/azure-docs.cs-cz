---
title: Použití sdílené galerie obrázků ve službách Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat účet testovacího prostředí tak, aby používal sdílenou galerii obrázků, aby uživatel mohl sdílet bitovou kopii s ostatními a jiný uživatel mohl tuto bitovou kopii použít k vytvoření virtuálního počítače šablony v testovacím prostředí.
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
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: c611ecdb5a2534f7368e533e3e19e6e3f96de57f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190445"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Použití sdílené galerie obrázků ve službách Azure Lab Services
Tento článek ukazuje, jak učitelé /lab admin můžete uložit šablonu virtuální počítač image pro to, aby ji znovu použít ostatní. Tyto obrázky se ukládají do [galerie sdílených obrázků](../../virtual-machines/windows/shared-image-galleries.md)Azure . Jako první krok správce testovacího prostředí připojí existující sdílenou galerii obrázků k účtu testovacího prostředí. Po připojení sdílené galerie obrázků mohou testovací prostředí vytvořená v účtu testovacího prostředí ukládat obrázky do sdílené galerie obrázků. Ostatní učitelé mohou vybrat tento obrázek ze sdílené galerie obrázků a vytvořit šablonu pro své třídy. 

> [!NOTE]
> V současné době Azure Lab Services podporuje vytváření šablon virtuálních počítačů na základě pouze **generalizované** image virtuálních počítačů (ne specializované image) ve sdílené image galerie. 

## <a name="prerequisites"></a>Požadavky
- Vytvořte sdílenou galerii bitových obrázků pomocí [Azure PowerShellu](../../virtual-machines/windows/shared-images.md) nebo [Azure CLI](../../virtual-machines/linux/shared-images.md).
- Připojili jste sdílenou galerii obrázků k účtu testovacího prostředí. Podrobné pokyny naleznete v tématu [Jak připojit nebo odpojit sdílenou galerii obrázků](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložení obrázku do galerie sdílených obrázků
Po připojení sdílené galerie obrázků může správce účtu testovacího prostředí nebo učitel uložit obrázek do sdílené galerie obrázků, aby ho mohli znovu použít jiní učitelé. 

1. Na stránce **Šablona** pro testovací prostředí vyberte **exportovat do Galerie sdílených obrázků** na panelu nástrojů.

    ![Tlačítko Uložit obrázek](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. V dialogovém okně **Exportovat do Galerie sdílených obrázků** zadejte **název obrazu**a pak vyberte **Exportovat**. 

    ![Dialogové okno Exportovat do galerie sdílených obrázků](../media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. Průběh této operace můžete vidět na stránce **Šablona.** Tahle operace může někdy trvat. 

    ![Export probíhá](../media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. Když je operace exportu úspěšná, zobrazí se následující zpráva:

    ![Export byl dokončen.](../media/how-to-use-shared-image-gallery/exporting-image-completed.png)

Obrázek můžete také nahrát do sdílené galerie obrázků mimo kontext testovacího prostředí. Další informace naleznete v [tématu Přehled sdílené galerie obrázků](../../virtual-machines/windows/shared-images.md). 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Použití obrázku ze sdílené galerie obrázků
Učitel/profesor může vybrat vlastní obrázek, který je k dispozici ve sdílené galerii obrázků pro šablonu během vytváření nové laboratoře.

![Použití obrázku virtuálního počítače z galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Další kroky
Další informace o sdílených galeriích obrázků naleznete v [galerii sdílených obrázků](../../virtual-machines/windows/shared-image-galleries.md).
