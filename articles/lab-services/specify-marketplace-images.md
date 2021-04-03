---
title: Určení imagí Marketplace pro testovací prostředí v Azure Lab Services
description: V tomto článku se dozvíte, jak zadat image na webu Marketplace, které může aplikace Lab Creator použít k vytvoření cvičení v účtu testovacího prostředí v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434732"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Určení imagí Marketplace dostupných pro tvůrci testovacího prostředí
Jako vlastník účtu testovacího prostředí můžete určit image z Marketplace, které můžou autoři testovacích prostředí použít k vytváření testovacích prostředí v tomto účtu testovacího prostředí. 

## <a name="select-images-available-for-labs"></a>Výběr imagí dostupných pro laboratoře
V nabídce vlevo vyberte **Marketplace images** (Image z Marketplace). Ve výchozím nastavení se zobrazí úplný seznam imagí (povolených i zakázaných). Seznam můžete filtrovat tak, aby se zobrazily jenom povolené nebo zakázané obrázky , a to tak, že v / rozevíracím seznamu v horní části vyberete možnost jenom **zakázaná** . 
    
![Stránka imagí v Marketplace](./media/tutorial-setup-lab-account/marketplace-images-page.png)

V tomto seznamu se zobrazí pouze image z Marketplace, které splňují následující podmínky:
    
- Vytváří jeden virtuální počítač.
- Ke zřízení virtuálních počítačů používají Azure Resource Manager.
- Nevyžadují zakoupení dalšího licenčního plánu.

## <a name="disable-images-for-a-lab"></a>Zakázání imagí pro testovací prostředí 
Pokud chcete pro testovací prostředí zakázat jednu Image, vyberte **... (tři tečky)** v posledním sloupci a vyberte možnost **Zakázat obrázek**. 

![Zakázání jedné image](./media/tutorial-setup-lab-account/disable-one-image.png) 

Případně můžete zaškrtnout políčko před názvem obrázku a vybrat možnost **zakázat vybrané obrázky** na panelu nástrojů. 

Chcete-li zakázat více imagí současně, zaškrtněte políčka před názvy obrázků a vyberte možnost **zakázat vybrané obrázky** na panelu nástrojů. 

![Zakázání několika imagí](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Povolení imagí pro testovací prostředí
Chcete-li povolit zakázanou bitovou kopii, vyberte **... (tři tečky)** v posledním sloupci a vyberte **Povolit obrázek**. Případně můžete zaškrtnout políčko před názvem obrázku a vybrat možnost **Povolit vybrané obrázky** na panelu nástrojů. 

Chcete-li zakázat více imagí současně, zaškrtněte políčka před názvy obrázků a vyberte možnost **Povolit vybrané obrázky** na panelu nástrojů. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Povolení imagí v době vytváření testovacího prostředí
V průběhu vytváření testovacího prostředí můžete povolit více imagí: 

1. Přihlaste se k [webu Azure Lab Services](https://labs.azure.com) pomocí přihlašovacích údajů **vlastníka účtu testovacího prostředí** .
2. Vyberte výchozí bitovou kopii virtuálního počítače nebo šipku dolů. 
3. Vyberte **Povolit další možnosti obrázku**. 

    ![Povolit další možnosti obrázku](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Podle pokynů v předchozí části povolte vybrané image. 
5. Možná budete muset zavřít nové okno **testovacího prostředí** a znovu ho otevřít, aby se zobrazily obrázky, které jste vybrali v předchozím kroku. 



## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access Labs](how-to-use-classroom-lab.md)
