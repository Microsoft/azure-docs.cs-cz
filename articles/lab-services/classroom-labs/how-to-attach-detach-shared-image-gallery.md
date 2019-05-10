---
title: Připojení nebo odpojení sdílené bitové kopie Galerie v Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak připojit Galerie sdílené bitové kopie do testovacího prostředí ve službě Azure Lab Services.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413895"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Připojení nebo odpojení sdílené bitové kopie Galerie v Azure Lab Services
Učitelé a laboratoře správce můžete uložit image šablony virtuálního počítače v Azure [sdílené bitové kopie Galerie](../../virtual-machines/windows/shared-image-galleries.md) pro ni znovu použije jinými uživateli. Správce testovacího prostředí jako první krok, připojí existující Galerie sdílené bitové kopie k účtu testovacího prostředí. Po připojení galerii sdílené bitové kopie labs vytvořené v účtu testovacího prostředí můžete uložit obrázky v galerii sdílené bitové kopie. Další učitelé můžete vybrat tuto image z Galerie sdílené bitové kopie k vytvoření šablony pro své třídy. 

Tento článek ukazuje, jak připojit nebo odpojit Galerie sdílené bitové kopie na účet testovacího prostředí. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurace v době vytvoření účtu služby testovacího prostředí
Při vytváření účtu testovacího prostředí můžete připojit Galerie sdílené bitové kopie do účtu testovacího prostředí. Můžete vybrat existující Galerie sdílené bitové kopie z rozevíracího seznamu nebo vytvořte novou. Chcete-li vytvořit a připojit sdílené bitové kopie Galerie účet testovacího prostředí, vyberte **vytvořit nový**, zadejte název pro galerie a zadejte **OK**. 

![Konfigurace sdílené bitové kopie Galerie v době vytvoření účtu služby testovacího prostředí](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Nakonfigurovat po vytvoření účtu testovacího prostředí
Po vytvoření účtu testovacího prostředí můžete provádět následující úlohy:

- Vytvořte a připojte Galerie sdílené bitové kopie
- Připojení sdílené bitové kopie Galerie účet testovacího prostředí
- Odpojení sdílené bitové kopie Galerie z účtu testovacího prostředí

## <a name="create-and-attach-a-shared-image-gallery"></a>Vytvořte a připojte Galerie sdílené bitové kopie
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v nabídce vlevo. Vyberte **Lab Services** v **DEVOPS** oddílu. Pokud vyberte hvězdičku (`*`) vedle položky **Lab Services**, přidá se do **Oblíbené** části v nabídce vlevo. V příštím a vyšší, vyberte **Lab Services** pod **Oblíbené**.

    ![Všechny služby -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Vyberte svůj účet testovacího prostředí zobrazíte **účet testovacího prostředí** stránky. 
4. Vyberte **Galerie obrázků Shared** v levé nabídce a vyberte **+ vytvořit** na panelu nástrojů.  

    ![Vytvoření tlačítka Galerie sdílené bitové kopie](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. V **vytvořit sdílené bitové kopie galerii** okno, zadejte **název** pro galerii a zadejte **OK**. 

    ![Vytvořit okno Galerie sdílené bitové kopie](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services vytvoří galerii sdílené bitové kopie a připojený k účtu testovacího prostředí. Všechny testovací prostředí vytvořené v rámci tohoto účtu testovacího prostředí mají přístup do Galerie připojené sdílené bitové kopie. 

    ![Galerie přiloženého obrázku](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    V dolním podokně zobrazí obrázky v galerii sdílené bitové kopie. V této nové galerie nejsou žádné obrázky. Při nahrávání imagí do galerie, můžete je zobrazit na této stránce.     

    Ve výchozím nastavení jsou povolené všechny Image v galerii připojené sdílené bitové kopie. Můžete povolit nebo zakázat vybrané Image tak, že je vyberete v seznamu a pomocí **povolit vybrané bitové kopie** nebo **zakázat vybrané bitové kopie** tlačítko.

## <a name="attach-an-existing-shared-image-gallery"></a>Připojit existující Galerie sdílené bitové kopie
Následující postup ukazuje, jak se připojit k účtu testovacího prostředí existující Galerie sdílené bitové kopie. 

1. Na **účet testovacího prostředí** stránce **Galerie obrázků Shared** v levé nabídce a vyberte **připojit** na panelu nástrojů. 

    ![Sdílený obrázek Galerie – tlačítko pro přidání](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **připojit existující Galerie obrázků Shared** stránky, vyberte galerii sdílené bitové kopie a vyberte **OK**.

    ![Vyberte existující Galerie](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zobrazí se následující obrazovka: 

    ![Moje galerie v seznamu](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    V tomto příkladu nejsou žádné Image v galerii imagí sdílené ještě.

    Identita Azure Lab Services se přidá jako přispěvatelé do Galerie sdílené bitové kopie, který je připojen k testovacím prostředí. Umožňuje učitelům / správce IT pro uložení virtuálního počítače Image v galerii sdílené bitové kopie. Všechny testovací prostředí vytvořené v rámci tohoto účtu testovacího prostředí mají přístup do Galerie připojené sdílené bitové kopie. 

    Ve výchozím nastavení jsou povolené všechny Image v galerii připojené sdílené bitové kopie. Můžete povolit nebo zakázat vybrané Image tak, že je vyberete v seznamu a pomocí **povolit vybrané bitové kopie** nebo **zakázat vybrané bitové kopie** tlačítko. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložit obrázek v galerii sdílené bitové kopie
Po připojení sdílené bitové kopie galerie můžete uložit nebo nahrání obrázku do Galerie sdílené bitové kopie, tak, aby mohou využívat jiné učitelé správce účtu testovacího prostředí nebo učitel. Pokyny pro nahrávání obrázku do Galerie sdílené bitové kopie najdete v tématu [Galerie obrázků Shared přehled](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Který je nyní zobrazován testovací prostředí v Učebnách uživatelské rozhraní (UI) nepodporuje ukládání bitové kopie testovacího prostředí do Galerie sdílené bitové kopie. 

## <a name="detach-a-shared-image-gallery"></a>Odpojení sdílené bitové kopie Galerie
Lze připojit pouze jedna Galerie sdílené bitové kopie do testovacího prostředí. Pokud se chcete připojit další galerie sdílené bitové kopie, odpojte tu před novým připojením. Chcete-li odpojit sdílenou image Galerie ve svém testovacím prostředí, vyberte **odpojit** na panelu nástrojů a Potvrdit operaci odpojení. 

![Odpojení sdílené bitové kopie Galerie z účtu testovacího prostředí](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Další postup
Další informace o tom, jak uložit obrázek testovacího prostředí do sdílené bitové kopie Galerie nebo použít některou image z Galerie sdílené bitové kopie k vytvoření virtuálního počítače najdete v tématu [použití Galerie sdílené bitové kopie](how-to-use-shared-image-gallery.md).

Další informace o sdílených obecně Galerie obrázků, najdete v části [sdílené bitové kopie Galerie](../../virtual-machines/windows/shared-image-galleries.md).
