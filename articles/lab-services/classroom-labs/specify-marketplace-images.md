---
title: Určení image marketplace pro testovací prostředí ve službě Azure Lab Services
description: Tento článek ukazuje, jak určit image Marketplace, které může tvůrce testovacího prostředí použít k vytvoření testovacích prostředí v testovacím účtu ve službě Azure Lab Services.
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
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: a64dee6da521764a38fc60bee06545f6a561c297
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257690"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Určení bitových kopií Marketplace dostupných tvůrcům testovacího prostředí
Jako vlastník účtu testovacího prostředí můžete určit image z Marketplace, které můžou autoři testovacích prostředí použít k vytváření testovacích prostředí v tomto účtu testovacího prostředí. 

## <a name="select-images-available-for-labs"></a>Výběr obrázků dostupných pro laboratoře
V nabídce vlevo vyberte **Marketplace images** (Image z Marketplace). Ve výchozím nastavení se zobrazí úplný seznam imagí (povolených i zakázaných). Seznam můžete filtrovat tak, aby zobcoviděl pouze povolené/zakázané obrázky, a to tak, že v hlavním seznamu vyberete možnost **Pouze povoleno**/**zakázáno.** 
    
![Stránka imagí v Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

V tomto seznamu se zobrazí pouze image z Marketplace, které splňují následující podmínky:
    
- Vytváří jeden virtuální počítač.
- Ke zřízení virtuálních počítačů používají Azure Resource Manager.
- Nevyžadují zakoupení dalšího licenčního plánu.

## <a name="disable-images-for-a-lab"></a>Zakázání bitových kopií pro testovací prostředí 
Chcete-li zakázat jeden obrázek pro testovací prostředí, vyberte **... (tři tečky)** v posledním sloupci a vyberte **Zakázat obraz**. 

![Zakázání jedné image](../media/tutorial-setup-lab-account/disable-one-image.png) 

Případně zaškrtnete políčko před názvem obrázku a na panelu nástrojů vyberete **Zakázat vybrané obrazy.** 

Chcete-li zakázat více obrazů najednou, zaškrtněte políčka před názvy obrázků a na panelu nástrojů vyberte **Zakázat vybrané obrazy.** 

![Zakázání několika imagí](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Povolení obrázků pro testovací prostředí
Chcete-li povolit zakázaný obrázek, vyberte **... (tři tečky)** v posledním sloupci a vyberte **Povolit obraz**. Případně zaškrtnete políčko před názvem obrázku a na panelu nástrojů **vyberete Povolit vybrané obrazy.** 

Chcete-li zakázat více obrazů najednou, zaškrtněte políčka před názvy obrázků a na panelu nástrojů vyberte **Povolit vybrané obrazy.** 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Povolení obrázků v době vytvoření testovacího prostředí
Při vytváření testovacího prostředí můžete povolit další obrázky: 

1. Přihlášení k [webu Azure Lab Services](https://labs.azure.com) pomocí přihlašovacích údajů **vlastníka účtu testovacího prostředí**
2. Vyberte výchozí obrázek virtuálního počítače nebo šipku dolů. 
3. Vyberte **Povolit další volby obrazu**. 

    ![Povolení dalších možností obrazu](../media/specify-marketplace-images/enable-more-images-menu.png)
4. Chcete-li povolit vybrané obrázky, postupujte podle pokynů z předchozí části. 
5. Možná budete muset zavřít okno **Nové testovací prostředí** a znovu ho otevřít, aby se zobcelo, které jste vybrali v předchozím kroku. 



## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí vytvářejte a spravujte testovací prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavte a publikujte šablony](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel laboratoře, přístup k učebně labs](how-to-use-classroom-lab.md)
