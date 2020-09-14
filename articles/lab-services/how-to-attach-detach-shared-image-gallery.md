---
title: Připojení nebo odpojení Galerie sdílených imagí v Azure Lab Services | Microsoft Docs
description: Tento článek popisuje, jak připojit galerii sdílených imagí k testovacímu prostředí učebny v Azure Lab Services.
ms.topic: article
ms.date: 09/11/2020
ms.openlocfilehash: 08d2a97138633a43e9acd69575c4b44e245d4faa
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056468"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Připojení nebo odpojení Galerie sdílených imagí v Azure Lab Services
V tomto článku se dozvíte, jak připojit nebo odpojit galerii sdílených imagí k účtu testovacího prostředí. 

> [!NOTE]
> Když [uložíte obrázek šablony testovacího prostředí](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery) v Azure Lab Services do galerie sdílených imagí, obrázek se nahraje do galerie jako speciální obrázek. [Specializované obrázky](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images) udržují informace specifické pro počítač a profily uživatelů. Do galerie můžete i nadále přímo nahrát zobecněnou image mimo Azure Lab Services. 
>
> Autor testovacího prostředí může vytvořit šablonu virtuálního počítače na základě zobecněných i specializovaných imagí v Azure Lab Services. 

## <a name="scenarios"></a>Scénáře
Tady je několik scénářů, které tato funkce podporuje: 

- Správce účtu testovacího prostředí připojí galerii sdílených imagí k účtu testovacího prostředí a nahraje image do galerie sdílených imagí mimo kontext testovacího prostředí. Pak tvůrci testovacího prostředí můžou pomocí této image z Galerie sdílených imagí vytvořit Labs. 
- Správce účtu testovacího prostředí připojí galerii sdílených imagí k účtu testovacího prostředí. Tvůrce testovacího prostředí (instruktor) uloží přizpůsobenou image testovacího prostředí do galerie sdílených imagí. Ostatní tvůrci testovacích prostředí pak můžou tuto image vybrat z Galerie sdílených imagí a vytvořit šablonu pro jejich cvičení. 

    Když se obrázek uloží do galerie sdílených imagí, Azure Lab Services replikuje uložený obrázek do jiných oblastí dostupných ve stejné [geografické](https://azure.microsoft.com/global-infrastructure/geographies/)oblasti. Zajišťuje, aby byla bitová cvičení dostupná pro testovací prostředí vytvořená v jiných oblastech ve stejné geografické oblasti. Při ukládání imagí do galerie sdílených imagí se vyskytnou další náklady, včetně nákladů na všechny replikované bitové kopie. Tato cena je oddělená od nákladů na využití Azure Lab Services. Další informace o cenách Galerie sdílených imagí najdete v tématu [Galerie sdílených imagí – fakturace](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

> [!IMPORTANT]
> Při použití Galerie sdílených imagí Azure Lab Services podporuje jenom image s méně než 128 GB místa na disku s operačním systémem. Obrázky s více než 128 GB místa na disku nebo více disků nebudou během vytváření testovacího prostředí zobrazeny v seznamu imagí virtuálních počítačů.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurace v době vytváření účtu testovacího prostředí
Když vytváříte účet testovacího prostředí, můžete k účtu testovacího prostředí připojit galerii sdílených imagí. Z rozevíracího seznamu můžete buď vybrat existující galerii sdílených imagí, nebo vytvořit novou. Pokud chcete galerii sdílených imagí vytvořit a připojit k účtu testovacího prostředí, vyberte **vytvořit novou**, zadejte název galerie a zadejte **OK**. 

![Konfigurace Galerie sdílených imagí v době vytváření účtu testovacího prostředí](./media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurace po vytvoření účtu testovacího prostředí
Po vytvoření účtu testovacího prostředí můžete provádět následující úlohy:

- Vytvoření a připojení Galerie sdílených imagí
- Připojit galerii sdílených imagí k účtu testovacího prostředí
- Odpojení Galerie sdílených imagí od účtu testovacího prostředí

## <a name="create-and-attach-a-shared-image-gallery"></a>Vytvoření a připojení Galerie sdílených imagí
1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo vyberte **všechny služby** . V části **DEVOPS** vyberte **testovací služby** . Vyberete-li možnost hvězdička ( `*` ) vedle položky **testovací služby**, přidá se do části **Oblíbené** v nabídce vlevo. Od dalšího okamžiku vyberte v části **Oblíbené položky** **testovací služby** .

    ![Všechny služby – > laboratorní služby](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Vyberte účet testovacího prostředí pro zobrazení stránky **účtu testovacího prostředí** . 
4. V nabídce vlevo vyberte **sdílená Galerie imagí** a na panelu nástrojů vyberte **+ vytvořit** .  

    ![Tlačítko pro vytvoření sdílené Image Galerie](./media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. V okně **vytvořit sdílenou galerii imagí** zadejte **název** galerie a zadejte **OK**. 

    ![Vytvořit okno Galerie sdílených imagí](./media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services vytvoří galerii sdílených imagí a připojí ji k účtu testovacího prostředí. Všechny laboratoře vytvořené v tomto účtu testovacího prostředí mají přístup k galerii sdílených imagí s připojenou příponou. 

    ![Galerie připojených imagí](./media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    V dolním podokně se v galerii sdílených imagí zobrazí obrázky. V této nové galerii neexistují žádné image. Když nahráváte obrázky do galerie, zobrazí se na této stránce.     

    Ve výchozím nastavení jsou všechny obrázky v galerii připojených sdílených imagí povolené. Vybrané obrázky můžete povolit nebo zakázat tak, že je vyberete v seznamu a použijete tlačítko **Povolit vybrané obrázky** nebo **zakázat vybrané bitové** kopie.

## <a name="attach-an-existing-shared-image-gallery"></a>Připojit existující galerii sdílených imagí
Následující postup ukazuje, jak připojit existující galerii sdílených imagí k účtu testovacího prostředí. 

1. Na stránce **účet testovacího prostředí** vyberte v nabídce vlevo položku **sdílená Galerie imagí** a na panelu nástrojů vyberte **připojit** . 

    ![Galerie sdílených imagí – tlačítko Přidat](./media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na stránce **připojit existující galerii sdílených imagí** vyberte galerii sdílených imagí a vyberte **OK**.

    ![Vybrat existující galerii](./media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zobrazí se následující obrazovka: 

    ![Moje galerie v seznamu](./media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    V tomto příkladu v galerii sdílených imagí ještě nejsou žádné image.

    Azure Lab Services identita se přidá jako přispěvatel do galerie sdílených imagí, která je připojená k testovacímu prostředí. Umožňuje učitelům a správcům IT ukládat image virtuálních počítačů do galerie sdílených imagí. Všechny laboratoře vytvořené v tomto účtu testovacího prostředí mají přístup k galerii sdílených imagí s připojenou příponou. 

    Ve výchozím nastavení jsou všechny obrázky v galerii připojených sdílených imagí povolené. Vybrané obrázky můžete povolit nebo zakázat tak, že je vyberete v seznamu a použijete tlačítko **Povolit vybrané obrázky** nebo **zakázat vybrané bitové** kopie. 

## <a name="detach-a-shared-image-gallery"></a>Odpojení Galerie sdílených imagí
K testovacímu prostředí se dá připojit jenom jedna Galerie sdílených imagí. Pokud chcete připojit jinou sdílenou galerii imagí, odpojte stávající před připojením nového. Pokud chcete odpojit galerii sdílených imagí z testovacího prostředí, vyberte na panelu nástrojů **Odpojit** a potvrďte operaci odpojení. 

![Odpojení Galerie sdílených imagí od účtu testovacího prostředí](./media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak uložit bitovou kopii testovacího prostředí do galerie sdílených imagí nebo použít image z Galerie sdílených imagí k vytvoření virtuálního počítače, najdete v tématu [Jak používat sdílenou galerii imagí](how-to-use-shared-image-gallery.md).

Další informace o galerii sdílených imagí obecně najdete v tématu [Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries).
