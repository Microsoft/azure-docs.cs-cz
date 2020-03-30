---
title: Připojení nebo odpojení sdílené galerie obrázků ve službách Azure Lab Services | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak připojit sdílenou galerii obrázků do testovacího prostředí učebny ve službě Azure Lab Services.
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
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284314"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Připojení nebo odpojení sdílené galerie obrázků ve službě Azure Lab Services
Učitelé/správce testovacího prostředí můžou uložit bitovou kopii virtuálního počítače šablony do [galerie sdílených bitových obrázků](../../virtual-machines/windows/shared-image-galleries.md) Azure, aby ji mohli znovu použít ostatní. Jako první krok správce testovacího prostředí připojí existující sdílenou galerii obrázků k účtu testovacího prostředí. Po připojení sdílené galerie obrázků mohou testovací prostředí vytvořená v účtu testovacího prostředí ukládat obrázky do sdílené galerie obrázků. Ostatní učitelé mohou vybrat tento obrázek ze sdílené galerie obrázků a vytvořit šablonu pro své třídy. 

Když je bitová kopie uložena do sdílené galerie bitových obrázků, služba Azure Lab Services replikuje uloženou bitovou kopii do jiných oblastí dostupných ve stejné [zeměpisné oblasti](https://azure.microsoft.com/global-infrastructure/geographies/). Zajišťuje, že obrázek je k dispozici pro laboratoře vytvořené v jiných oblastech ve stejné zeměpisné oblasti. Uložení obrazů do sdílené galerie obrázků znamená další náklady, které zahrnují náklady na všechny replikované bitové kopie. Tyto náklady jsou oddělené od nákladů na využití azure lab služby. Další informace o cenách galerie sdílených obrázků naleznete v [tématu Galerie sdílených obrázků – fakturace]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

V tomto článku se zobrazí postup připojení nebo odpojení sdílené galerie obrázků k účtu testovacího prostředí. 

> [!NOTE]
> V současné době Azure Lab Services podporuje vytváření šablon virtuálních počítačů na základě pouze **generalizované** image virtuálních počítačů (ne specializované image) ve sdílené image galerie. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Konfigurovat v době vytvoření účtu testovacího prostředí
Při vytváření účtu testovacího prostředí můžete k účtu testovacího prostředí připojit sdílenou galerii obrázků. Můžete buď vybrat existující sdílenou galerii obrázků z rozevíracího seznamu, nebo vytvořit novou. Pokud chcete vytvořit a připojit sdílenou galerii obrázků k účtu testovacího prostředí, vyberte **Vytvořit nový**, zadejte název galerie a zadejte **OK**. 

![Konfigurace galerie sdílených obrázků v době vytvoření účtu testovacího prostředí](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Konfigurovat po vytvoření účtu testovacího prostředí
Po vytvoření účtu testovacího prostředí můžete provést následující úkoly:

- Vytvoření a připojení sdílené galerie obrázků
- Připojení sdílené galerie obrázků k účtu testovacího prostředí
- Odpojení sdílené galerie obrázků z účtu testovacího prostředí

## <a name="create-and-attach-a-shared-image-gallery"></a>Vytvoření a připojení sdílené galerie obrázků
1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé nabídce vyberte **Všechny služby.** V části **DEVOPS** vyberte **Lab Services.** Pokud vyberete`*`hvězdičku ( ) vedle **položky Lab Services**, přidá se do části **OBLÍBENÉ položky** v levé nabídce. Od příštího dne vyberte **lab služby** v části **OBLÍBENÉ POLOŽKY**.

    ![Všechny služby -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Výběrem účtu testovacího prostředí zobrazíte stránku **Účet testovacího prostředí.** 
4. V levé nabídce vyberte **Sdílená galerie obrázků** a na panelu nástrojů vyberte **+ Vytvořit.**  

    ![Tlačítko Vytvořit sdílenou galerii obrázků](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. V okně **Vytvořit sdílenou galerii obrázků** zadejte **název** galerie a zadejte **OK**. 

    ![Vytvořit okno galerie sdílených obrázků](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services vytvoří sdílenou galerii bitových obrázků a připojí ji k účtu testovacího prostředí. Všechna testovací prostředí vytvořená v tomto testovacím účtu mají přístup k připojené galerii sdílených obrázků. 

    ![Připojená galerie obrázků](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    V dolním podokně se zobrazí obrázky ve sdílené galerii obrázků. V této nové galerii nejsou žádné obrázky. Když obrázky nahrajete do galerie, zobrazí se na této stránce.     

    Všechny obrázky v připojené galerii sdílených obrázků jsou ve výchozím nastavení povoleny. Vybrané obrázky můžete povolit nebo zakázat tak, že je vyberete v seznamu a použijete tlačítko **Povolit vybrané obrazy** nebo **Zakázat vybrané obrazy.**

## <a name="attach-an-existing-shared-image-gallery"></a>Připojení existující galerie sdílených obrázků
Následující postup ukazuje, jak připojit existující sdílenou galerii obrázků k účtu testovacího prostředí. 

1. Na stránce **Účet testovacího prostředí** vyberte v levé nabídce **sdílenou galerii obrázků** a na panelu nástrojů vyberte **Připojit.** 

    ![Galerie sdílených obrázků - tlačítko Přidat](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na stránce **Připojit existující Galerii sdílených obrázků** vyberte galerii sdílených obrázků a vyberte **OK**.

    ![Výběr existující galerie](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Zobrazí se následující obrazovka: 

    ![Moje galerie v seznamu](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    V tomto příkladu ještě nejsou žádné obrázky ve sdílené galerii obrázků.

    Identita Azure Lab Services se přidá jako přispěvatel do sdílené galerie obrázků, která je připojena k testovacímu prostředí. Umožňuje učitelům / IT admin ukládat image virtuálních počítačů do sdílené galerie obrázků. Všechna testovací prostředí vytvořená v tomto testovacím účtu mají přístup k připojené galerii sdílených obrázků. 

    Všechny obrázky v připojené galerii sdílených obrázků jsou ve výchozím nastavení povoleny. Vybrané obrázky můžete povolit nebo zakázat tak, že je vyberete v seznamu a použijete tlačítko **Povolit vybrané obrazy** nebo **Zakázat vybrané obrazy.** 

## <a name="detach-a-shared-image-gallery"></a>Odpojení sdílené galerie obrázků
Do testovacího prostředí lze připojit pouze jednu sdílenou galerii obrázků. Pokud chcete připojit jinou sdílenou galerii obrázků, odpojte před připojením nové galerie aktuální. Chcete-li odpojit sdílenou galerii obrázků z testovacího prostředí, vyberte na panelu nástrojů **odpojit** a potvrďte operaci odpojení. 

![Odpojení sdílené galerie obrázků od účtu testovacího prostředí](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Další kroky
Informace o tom, jak uložit obrázek testovacího prostředí do sdílené galerie obrázků nebo pomocí obrázku ze sdílené galerie obrázků vytvořit virtuální hod, najdete v tématu [Jak používat sdílenou galerii obrázků](how-to-use-shared-image-gallery.md).

Další informace o sdílených galeriích obrázků obecně naleznete v [galerii sdílených obrázků](../../virtual-machines/windows/shared-image-galleries.md).
