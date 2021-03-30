---
title: Umožňuje tvůrci testovacího prostředí vybrat umístění v Azure Lab Services
description: Tento článek popisuje, jak může správce účtu testovacího prostředí dovolit tvůrcům testovacího prostředí vybrat umístění pro své laboratoře.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444195"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Umožňuje tvůrci testovacího prostředí vybrat umístění pro testovací prostředí v Azure Lab Services
V Azure Lab Services může vlastník účtu testovacího prostředí dovolit tvůrcům testovacího prostředí (pedagogům) vybrat umístění pro testovací prostředí, které vytvoří. Toto umístění se může lišit od umístění účtu testovacího prostředí. Umístění je skupina oblastí Azure. Například umístění USA je skupina oblastí, například Východní USA, Západní USA a tak dále. 

Jako vlastník účtu testovacího prostředí můžete při vytváření účtu testovacího prostředí a po vytvoření účtu testovacího prostředí (nebo existujícího účtu testovacího prostředí) vybrat možnost zvolit **umístění testovacího** prostředí. 

## <a name="at-the-time-of-lab-account-creation"></a>V době vytváření účtu testovacího prostředí
Když vytvoříte účet testovacího prostředí, zobrazí se na první obrazovce (karta **základy** ) Tato možnost. 

![Povolit možnost v době vytváření testovacího prostředí](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Tato možnost je zakázaná, pokud vyberete partnerský virtuální síť pro svůj účet testovacího prostředí na kartě **Upřesnit** .  

![Když je povolena rovnocenná virtuální síť](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Po vytvoření účtu testovacího prostředí
Po vytvoření účtu testovacího prostředí můžete tuto možnost povolit nebo zakázat pomocí následujících kroků: 

1. Na stránce **účet testovacího prostředí** vyberte v nabídce vlevo možnost **Nastavení testovacího prostředí** .
2. Vyberte možnost **Povolení testovacího prostředí pro výběr umístění testovacího** prostředí, pokud chcete, aby autor testovacího prostředí mohl vybrat umístění pro testovací prostředí. Pokud je tato verze zakázaná, laboratoře se automaticky vytvoří ve stejném umístění, ve kterém účet testovacího prostředí existuje. 
    
    Toto pole je zakázáno, když vyberete virtuální síť pro pole **partnerské virtuální sítě** . Je to proto, že laboratoře v účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí pro přístup k prostředkům v partnerské virtuální síti. 
1. Na panelu nástrojů vyberte **Uložit**. 

    ![Nastavení testovacího prostředí](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Žádná virtuální síť a výběr umístění nejsou povolené.
V tomto scénáři jste nepovolili možnost **Povolit nástroj pro tvorbu testovacího prostředí pro výběr umístění testovacího prostředí** . 

![Žádné umístění testovacího prostředí](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Pak tvůrci testovacích prostředí (pedagogy) nevidí možnost vybrat umístění pro testovací prostředí. Pro každou možnost velikosti, která je k dispozici, uvidí cenu za hodinu. Když vytvoří testovací prostředí, vytvoří se v oblasti Azure, která je ve stejném umístění jako oblast Azure, ve které je jejich účet testovacího prostředí. Pokud je například účet testovacího prostředí v **západní USA**, testovací prostředí se může vytvořit v **střed USA – jih** , ale nevytvoří se v oblasti **Kanada – východ**. Nezaručujeme vám žádné informace o oblasti, kterou zvolíme z toho, kde se nachází v tomto umístění. Pokud je velikost aktuálně omezená, pak autor testovacího prostředí uvidí zaškrtávací políčko, kde uvidí velikosti, které běžně podporují, ale aktuálně nejsou k dispozici. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Virtuální síť a výběr umístění nejsou povolené.
V tomto scénáři je možnost **Povolení testovacího prostředí pro výběr umístění testovacího** prostředí zakázaná, protože jste vybrali partnerský virtuální síť pro účet testovacího prostředí. Pak budou tvůrci testovacího prostředí zobrazovat stejnou obrazovku jako s předchozí možností. Vzhledem k tomu, že všechny virtuální počítače musí být ve stejné oblasti Azure jako virtuální síť, testovací prostředí se vytvoří ve stejné oblasti Azure, ve které je virtuální síť. Pokud je tato konkrétní oblast omezená na velikost, bude se tato velikost zobrazovat jako nedostupná. 

## <a name="location-selection-is-enabled"></a>Výběr umístění je povolený.
Když vyberete možnost **Povolení programu Lab Creator pro výběr umístění testovacího prostředí**, tvůrci testovacích prostředí (pedagogy) uvidí možnost vybrat umístění při vytváření testovacího prostředí. 

![Vybrat umístění testovacího prostředí](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Tvůrci testovacího prostředí uvidí celou řadu cen pro všechna umístění, která jsou ve velikosti, a můžou zvolit umístění. Testovací prostředí se vytvoří v jakékoli oblasti Azure, která se mapuje na toto umístění.

Pokud je umístění omezené, v seznamu se ve výchozím nastavení nezobrazí. Rozbalte rozevírací seznam a vyberte **Zobrazit nedostupná umístění této velikosti**. 

![Zobrazit nedostupná umístění](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Náklady
Předchozí ceny vycházejí z velikosti virtuálního počítače, kterou zvolíte pro testovací prostředí. Cena je teď založená na kombinaci operačního systému (OS), velikosti a umístění. 

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Připojení sítě testovacího prostředí k partnerské virtuální síti](how-to-connect-peer-virtual-network.md)
- [Připojení Galerie sdílených imagí k testovacímu prostředí](how-to-attach-detach-shared-image-gallery.md)
- [Přidat uživatele jako vlastníka testovacího prostředí](how-to-add-user-lab-owner.md)
- [Zobrazit nastavení brány firewall pro testovací prostředí](how-to-configure-firewall-settings.md)
- [Konfigurace dalších nastavení pro testovací prostředí](how-to-configure-lab-accounts.md)