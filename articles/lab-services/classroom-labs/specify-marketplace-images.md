---
title: Určení imagí Marketplace pro testovací prostředí v Azure Lab Services
description: V tomto článku se dozvíte, jak zadat image na webu Marketplace, které může aplikace Lab Creator použít k vytvoření cvičení v účtu testovacího prostředí v Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: ad56041f853d030e3a286610fe4872bffecaee12
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444664"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Určení imagí Marketplace dostupných pro tvůrci testovacího prostředí
Jako vlastník účtu testovacího prostředí můžete určit image z Marketplace, které můžou autoři testovacích prostředí použít k vytváření testovacích prostředí v tomto účtu testovacího prostředí. 

## <a name="select-images-available-for-labs"></a>Výběr imagí dostupných pro laboratoře
V nabídce vlevo vyberte **Marketplace images** (Image z Marketplace). Ve výchozím nastavení se zobrazí úplný seznam imagí (povolených i zakázaných). Pomocí možnosti **Enabled only**/**Disabled only** (Jenom povolené / Jenom zakázané) v rozevíracím seznamu v horní části můžete seznam filtrovat, aby se v něm zobrazovaly jen povolené/zakázané image. 
    
![Stránka imagí v Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

V tomto seznamu se zobrazí pouze image z Marketplace, které splňují následující podmínky:
    
- Vytváří jeden virtuální počítač.
- Ke zřízení virtuálních počítačů používají Azure Resource Manager.
- Nevyžadují zakoupení dalšího licenčního plánu.

## <a name="disable-images-for-a-lab"></a>Zakázání imagí pro testovací prostředí 
Pokud chcete pro testovací prostředí zakázat jednu Image, vyberte **... (tři tečky)** v posledním sloupci a vyberte možnost **Zakázat obrázek**. 

![Zakázání jedné image](../media/tutorial-setup-lab-account/disable-one-image.png) 

Případně můžete zaškrtnout políčko před názvem obrázku a vybrat možnost **zakázat vybrané obrázky** na panelu nástrojů. 

Chcete-li zakázat více imagí současně, zaškrtněte políčka před názvy obrázků a vyberte možnost **zakázat vybrané obrázky** na panelu nástrojů. 

![Zakázání několika imagí](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Povolení imagí pro testovací prostředí
Chcete-li povolit zakázanou bitovou kopii, vyberte **... (tři tečky)** v posledním sloupci a vyberte **Povolit obrázek**. Případně můžete zaškrtnout políčko před názvem obrázku a vybrat možnost **Povolit vybrané obrázky** na panelu nástrojů. 

Chcete-li zakázat více imagí současně, zaškrtněte políčka před názvy obrázků a vyberte možnost **Povolit vybrané obrázky** na panelu nástrojů. 

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
