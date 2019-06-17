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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 8d8b6fffe197d4180b091518dcd1615d0e0b9d19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412851"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Pomocí Galerie sdílené bitové kopie v Azure Lab Services
Tento článek popisuje, jak správce učitelé/testovacího prostředí můžete uložit image šablony virtuálního počítače, aby se znovu použít jiní. Tyto Image se ukládají v Azure [sdílené bitové kopie Galerie](../../virtual-machines/windows/shared-image-galleries.md). Správce testovacího prostředí jako první krok, připojí existující Galerie sdílené bitové kopie k účtu testovacího prostředí. Po připojení galerii sdílené bitové kopie labs vytvořené v účtu testovacího prostředí můžete uložit obrázky v galerii sdílené bitové kopie. Další učitelé můžete vybrat tuto image z Galerie sdílené bitové kopie k vytvoření šablony pro své třídy. 

## <a name="prerequisites"></a>Požadavky
- Vytvořením Galerie sdílené bitové kopie pomocí [prostředí Azure PowerShell](../../virtual-machines/windows/shared-images.md) nebo [rozhraní příkazového řádku Azure](../../virtual-machines/linux/shared-images.md).
- Galerie obrázků sdílené připojení k účtu testovacího prostředí. Podrobné pokyny najdete v tématu [jak připojení nebo odpojení sdílené Galerie obrázků](how-to-attach-detach-shared-image-gallery.md).


## <a name="save-an-image-to-the-shared-image-gallery"></a>Uložit obrázek v galerii sdílené bitové kopie
Po připojení sdílené bitové kopie Galerie učitel můžete uložit nebo tak, aby mohou využívat jiné učitelé nahrání obrázku do Galerie sdílené bitové kopie. Pokyny pro nahrávání obrázku do Galerie sdílené bitové kopie najdete v tématu [Galerie obrázků Shared přehled](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Který je nyní zobrazován testovací prostředí v Učebnách uživatelské rozhraní (UI) nepodporuje ukládání bitové kopie testovacího prostředí do Galerie sdílené bitové kopie. 

## <a name="use-an-image-from-the-shared-image-gallery"></a>Použít některou image z Galerie sdílené bitové kopie
Učitelů/profesor můžete si vybrat vlastní image dostupných v galerii sdílené bitové kopie šablony při vytvoření nového testovacího prostředí.

![Použijte image virtuálního počítače z Galerie](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Další postup
Další informace o galeriích sdílené bitové kopie, naleznete v tématu [sdílené bitové kopie Galerie](../../virtual-machines/windows/shared-image-galleries.md).
