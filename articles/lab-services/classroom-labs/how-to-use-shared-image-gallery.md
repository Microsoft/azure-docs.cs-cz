---
title: Pomocí Galerie sdílené bitové kopie v Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat účet testovacího prostředí, který chcete použít sdílené bitové kopie Galerie tak, aby může uživatel sdílet s jinými bitovou kopii a jiný uživatel můžete použít image k vytvoření šablony virtuálního počítače v testovacím prostředí.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695167"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Pomocí Galerie sdílené bitové kopie v Azure Lab Services
Tento článek popisuje, jak správce učitelé/testovacího prostředí můžete uložit image šablony virtuálního počítače, aby se znovu použít jiní. Tyto Image se ukládají v Azure [sdílené bitové kopie Galerie](../../virtual-machines/windows/shared-image-galleries.md). Správce testovacího prostředí jako první krok, připojí existující Galerie sdílené bitové kopie k účtu testovacího prostředí. Po připojení galerii sdílené bitové kopie labs vytvořené v účtu testovacího prostředí můžete uložit obrázky v galerii sdílené bitové kopie. Další učitelé můžete vybrat tuto image z Galerie sdílené bitové kopie k vytvoření šablony pro své třídy. 

## <a name="prerequisites"></a>Požadavky
Vytvořením Galerie sdílené bitové kopie pomocí [prostředí Azure PowerShell](../../virtual-machines/windows/shared-images.md) nebo [rozhraní příkazového řádku Azure](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Připojení sdílené bitové kopie Galerie k účtu testovacího prostředí
Následující postup ukazuje, jak se připojit k účtu testovacího prostředí Galerie sdílené bitové kopie. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v nabídce vlevo. Vyberte **Lab Services** v **DEVOPS** oddílu. Pokud vyberte hvězdičku (`*`) vedle položky **Lab Services**, přidá se do **Oblíbené** části v nabídce vlevo. V příštím a vyšší, vyberte **Lab Services** pod **Oblíbené**.

    ![Všechny služby -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Vyberte svůj účet testovacího prostředí zobrazíte **účet testovacího prostředí** stránky. 
4. Vyberte **Galerie obrázků Shared** v levé nabídce a vyberte **připojit** na panelu nástrojů. 

    ![Sdílený obrázek Galerie – tlačítko pro přidání](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **připojit existující Galerie obrázků Shared** stránky, vyberte galerii sdílené bitové kopie a vyberte **OK**.

    ![Vyberte existující Galerie](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zobrazí se následující obrazovka: 

    ![Moje galerie v seznamu](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    V tomto příkladu nejsou žádné Image v galerii imagí sdílené ještě.

Identita Azure Lab Services se přidá jako přispěvatelé do Galerie sdílené bitové kopie, který je připojen k testovacím prostředí. Umožňuje učitelům / správce IT pro uložení virtuálního počítače Image v galerii sdílené bitové kopie. Všechny testovací prostředí vytvořené v rámci tohoto účtu testovacího prostředí mají přístup do Galerie připojené sdílené bitové kopie. 

Ve výchozím nastavení jsou povolené všechny Image v galerii připojené sdílené bitové kopie. Můžete povolit nebo zakázat vybrané Image tak, že je vyberete v seznamu a pomocí **povolit vybrané bitové kopie** nebo **zakázat vybrané bitové kopie** tlačítko. 

## <a name="detach-a-shared-image-gallery"></a>Odpojení sdílené bitové kopie Galerie
Lze připojit pouze jedna Galerie sdílené bitové kopie do testovacího prostředí. Pokud se chcete připojit další galerie sdílené bitové kopie, odpojte tu před novým připojením. Chcete-li odpojit sdílenou image Galerie ve svém testovacím prostředí, vyberte **odpojit** na panelu nástrojů a Potvrdit operaci odpojení. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložit obrázek v galerii sdílené bitové kopie
Po Galerie sdílené bitové kopie, učitel uložit image šablony do Galerie sdílené bitové kopie, takže můžete využívat jiné učitele.

![Uložit image virtuálního počítače v galerii](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Použít některou image z Galerie sdílené bitové kopie
Učitelů/profesor můžete si vybrat vlastní image dostupných v galerii sdílené bitové kopie šablony při vytvoření nového testovacího prostředí.

![Použijte image virtuálního počítače z Galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Další postup
Další informace o galeriích sdílené bitové kopie, naleznete v tématu [sdílené bitové kopie Galerie](../../virtual-machines/windows/shared-image-galleries.md).
